---
title: Time Series Analysis Platform
categories: [Tech, Intership]
tags: [Project]
render_with_liquid: false
---

## 业务问题  
对于时序数据而言，在不同时间阶段设定不同的报警阈值有较高的研究价值。比如对于一个接口而言，如果始终设定超过上下限的阈值而触发K8s报警显然是不合理的，不仅随着业务扩展接口调用次数增多而导致整体趋势上升，而且对于金融交易业务而言，时间影响十分巨大。如何为K8s不同的指标设定不同的报警阈值十分重要。

## 数据展示
交易数据如下图所示，对于每日交易量而言，QPS呈现午间和晚间两个高峰。对于午间高峰期（由于午间时间较为固定，一般集中在11:45-12:20之间为交易高峰期），时间间隔较短，上升速率较高故曲线斜率极大。而目前常用时序数据预测模型更加关注于整体趋势研究，对于较短时间内且偏离整体趋势较远的点会选择忽略。所以如何拟合变化率很高的时序曲线比较困难。
![hourly_data](../../pic/tsa/houly_data.jpg)
<div style="text-align:center">hourly transaction data trend in two days</div>

## 方案选择
#### Prophet Model  
Prophet是由Facebook开发的一个用于时间序列预测的开源库。它基于统计模型，可以自动处理常见的时间序列数据特征，如季节性、趋势性和节假日效应。Prophet具有以下特点：
1. 使用简单  
所需计算资源不高，调参简单，可用性高。  
2. 灵活性高  
Prophet允许用户根据自己的需求自定义模型参数，如季节性的调整、趋势性。 
3. 考虑节假日效应  
可引入中国节日（但不能自动考虑调休问题）  

在利用Prophet模型实现数据预测相关接口时发现其局限性主要在于预测较短时间段变化率高的曲线时无法拟合。体现在下图中：（时间轴为UTC时间）  
![fitting_result](../../pic/tsa/prophet_fitting.jpg)  


对于此问题尝试缩短总体时间段来凸显午间时间段的权重，将采样时间从全天采样变成近五日内预测点前后2小时的数据。此方法对于部分曲线有一定效果，但是对于变化率极高的曲线依然难以拟合。

但同时，Prophet模型也有很多优点可以运用在其他地方。
- 趋势预测：对于相关业务，比如对于某个业务，某个接口，可以预测其CPU使用率以达到随时扩容的效果，省去租用云计算服务器的部分费用。
- 非高峰的数据预测：由于后续使用的模型所需算力较高，可在非高峰期使用Prophet模型实现数据预测工作。
- 集成学习器组件：类似于上条优势，可以将部分时间段的预测结果乘上相关权重实现集成学习预测功能。


[Facebook Prophet Reference](https://towardsdatascience.com/prophet-vs-neuralprophet-fc717ab7a9d8)

#### StreamAD
[StreamAD](https://github.com/Fengrui-Liu/StreamAD)

#### SARIMAX模型
ARIMA是一类时序数据模型，可以根据自身的过去值（即自身的滞后和滞后的预测误差）“解释”给定的时间序列，因此可以使用方程式预测未来价值。ARIMA模型通过获取之前一段时间的数据和滑动窗口取平均值的操作来预测未来的数据。SARIMAX模型是ARIMAX模型增加了seasonal季节性的参数，在ARIMAX的基础上使得模型可以预测周期性的数据。相较于Prophet模型，SARIMAX模型对于模型的灵敏度更高，更适合异常值预测而非趋势预测。对于此项目而言，每日午间峰值预测便使用SARIMAX模型实现。

训练函数：
```python
    def train(self, pre_trained_model=None):
            if pre_trained_model is None:
                logger.debug("Start to train the model...")
                print(self._analysis_data["y"])
                model = SARIMAX(
                    self._analysis_data["y"],
                    order=(1, 1, 1),
                    seasonal_order=(1, 1, 0, self.seasonality_mins // self.step_mins),
                ).fit()
                params = model.params
                logger.debug("End to train the model...")
                return params
            else:
                params = pickle.loads(pre_trained_model)
                params = SARIMAX(
                    self._analysis_data,
                    order=(1, 1, 1),
                    seasonal_order=(1, 1, 0, self.seasonality_mins // self.step_mins),
                ).update(params)
                return params
```

预测函数：
```python
def predict(
        self, pre_trained_model: Any, forecast_steps: int, forecast_step_mins: int
    ) -> pd.DataFrame:
        """
        1. generate future data points based on the given params
        2. load and reform the model based on the given params
        3. predict the lower and upper bound
        4. return the dataframe
        """
        future_data = super()._prepare_predict(forecast_steps, forecast_step_mins)
        print(future_data)
        df_predict = pd.DataFrame(future_data, columns=["ds"])
        if isinstance(pre_trained_model, pd.Series):
            params = pre_trained_model
        else:
            params = pickle.loads(pre_trained_model)
        model = SARIMAX(
            self._analysis_data,
            order=(1, 0, 1),
            seasonal_order=(1, 1, 0, self.seasonality_mins // self.step_mins),
        )
        res_bayes = model.smooth(params)
        forecast = res_bayes.get_prediction(
            start=df_predict["ds"][0],
            end=df_predict["ds"][forecast_steps - 1],
            dynamic=True,
        )
        df_future = forecast.conf_int(alpha=self.uncertainty)
        df_future.reset_index(inplace=True)
        df_future = df_future.rename(
            columns={"lower y": "yhat_lower", "upper y": "yhat_upper", "index": "ds"}
        )
        return df_future
```



#### Merlion Model  
[Salesforce Merlion](https://github.com/salesforce/Merlion)

## Architecture
![Architecture](../../pic/tsa/tsa_new_.pic.jpg){: .light .w-75 .shadow .rounded-10 w='1212' h='668' }

## Future Work
由于CPU使用率以及对于模型理解能力问题，此解决方案还有许多可以优化的问题。

不同模型结合的问题，目前时序数据模型的实现大都以分解周期性，误差以及趋势等有关，不同模型参数对于模型生成结果有很大影响，本人对于SARIMAX理解十分欠缺，仅仅可以通过定型分析调整部分模型参数，远远做不到定量分析的工作，也没有更多的时间投入在模型优化上面，希望未来能够使用不同的时序模型或者其他大模型的方法优化甚至预测并提前报警。
- 数据可视化工作未能实现。
- 周末节假日等没有进行特殊实现（虽然目前在这方面拟合的还可以，但是应该额外关注特殊时间段的数据）
- 工程实现上还有很多人工完成的任务，由于权限和接口等原因无法完成全自动实现任务。
- 受限于CPU等因素，对于报警步长等参数无法做到十分精准。


本人认为此项目还有很多优化的可能，可以根据未来业务需要开发出很多新功能，很多新发展方向，如：可以横向实现机器学习和算法的开发和可视化平台，也可以纵向实现对于一条曲线，根据比方说近5分钟变化和预测的上下限，提前预警5分钟后可能发生报警问题。希望这些简单的想法可以给未来一些启发。

# References
1. https://towardsdatascience.com/prophet-vs-neuralprophet-fc717ab7a9d8
2. https://github.com/Fengrui-Liu/StreamAD
3. https://github.com/salesforce/Merlion
