---
title: Time Series Analysis Platform
categories: [Tech, Intership]
tags: [Project]
render_with_liquid: false
---

# 业务问题  
对于时序数据而言，在不同时间阶段设定不同的报警阈值有较高的研究价值。比如对于一个接口而言，如果始终设定超过上下限的阈值而触发K8s报警显然是不合理的，不仅随着业务扩展接口调用次数增多而导致整体趋势上升，而且对于金融交易业务而言，时间影响十分巨大。如何为K8s不同的指标设定不同的报警阈值十分重要。

# 数据展示
![hourly_data](../../pic/tsa/houly_data.jpg)
<div style="text-align:center">hourly transaction data trend in two days</div>

# 方案选择
### Prophet Model  
![fitting_result](../../pic/tsa/prophet_fitting.jpg)  
[Facebook Prophet Reference](https://towardsdatascience.com/prophet-vs-neuralprophet-fc717ab7a9d8)

### StreamAD
[StreamAD](https://github.com/Fengrui-Liu/StreamAD)

### SARIMAX模型
ARIMA是一类时序数据模型，可以根据自身的过去值（即自身的滞后和滞后的预测误差）“解释”给定的时间序列，因此可以使用方程式预测未来价值。ARIMA模型通过获取之前一段时间的数据和滑动窗口取平均值的操作来预测未来的数据。SARIMAX模型是ARIMAX模型增加了seasonal季节性的参数，在ARIMAX的基础上使得模型可以预测周期性的数据。相较于Prophet模型，SARIMAX模型对于模型的灵敏度更高，更适合异常值预测而非趋势预测。

### Merlion Model  
[Salesforce Merlion](https://github.com/salesforce/Merlion)

# Architecture
![Architecture](../../pic/tsa/tsa_archi.jpg)

# Future Work
由于CPU使用率以及对于模型理解能力问题，此解决方案还有许多可以优化的问题。

不同模型结合的问题，目前时序数据模型的实现大都以分解周期性，误差以及趋势等有关，不同模型参数对于模型生成结果有很大影响，本人对于SARIMAX理解十分欠缺，仅仅可以通过定型分析调整部分模型参数，远远做不到定量分析的工作，也没有更多的时间投入在模型优化上面，希望未来能够使用不同的时序模型或者其他大模型的方法优化甚至预测并提前报警。
数据可视化工作未能实现。
周末节假日等没有进行特殊实现（虽然目前在这方面拟合的还可以，但是应该额外关注特殊时间段的数据）
工程实现上还有很多人工完成的任务，由于权限和接口等原因无法完成全自动实现任务。
受限于CPU等因素，对于报警步长等参数无法做到十分精准。
本人认为此项目还有很多优化的可能，可以根据未来业务需要开发出很多新功能，很多新发展方向，如：可以横向实现机器学习和算法的开发和可视化平台，也可以纵向实现对于一条曲线，根据比方说近5分钟变化和预测的上下限，提前预警5分钟后可能发生报警问题。希望这些简单的想法可以给未来一些启发。

# References
1. https://towardsdatascience.com/prophet-vs-neuralprophet-fc717ab7a9d8
2. https://github.com/Fengrui-Liu/StreamAD
3. https://github.com/salesforce/Merlion
