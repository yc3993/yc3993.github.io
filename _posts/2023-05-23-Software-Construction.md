---
title: Software Construction
categories: [Tech, MIT]
tags: [Courses]
render_with_liquid: true
---

## Goals for good coding
- **Safe from bugs**
- **Easy to understand**
- **Ready for change**

## Static Checking  

#### Why  

学习这门课的原因在于如何写出engineering的代码是十分重要的。本人和许多转码的学生一样，可能刚开始从DS入门，以方法论的方式和追求高效的算法为目标，来学习CS的内容。但是随着实习和项目的深入，算法在数学层面带来的优势远没有团队协作，快速增删查改带来的优势大，这就意味着学习如何写出hacky的代码尤为重要。

相信很多同学也是从Python，Flask入手编写app。Python强大的范性带来很大量的的便利，让我们可以不用注重格式等繁琐的规范而写出高效的代码，但是这些规范也使得Java的代码利于复用，重写和理解等操作。这门课在介绍设计模式的时候通过比较语言之间的差异突出规范的重要性并很好的理解优秀的设计模式带来的优势，很多地方值得体会一下。

#### Java to Python  
1. 静态检查方法  
    - Dynamic checking(java): 
        1. 检查参数合法性
        2. 基本函数: ```Integer.valueOf("hello")```
        3. 参数过大过小
        4. 空指针
    - Static checking(python)

2. 数组与集合
    - Array: 定长  
        ```int[] a = new int[100];```
    - List: 接口  
        ```List<Integer> list = new ArrayList<Integer>();```
        List接口只针对对象不能是int
3. Methods  
    方法在java中必须在class内:

    ```java
    public class Hailstone {
        /**
        * Compute a hailstone sequence.
        * @param n  starting number for sequence; assumes n > 0.
        * @return hailstone sequence starting with n and ending with 1.
        */
        public static List<Integer> hailstoneSequence(int n) {
            List<Integer> list = new ArrayList<Integer>();
            while (n != 1) {
                list.add(n);
                if (n % 2 == 0) {
                    n = n / 2;
                } else {
                    n = 3 * n + 1;
                }
            }
            list.add(n);
            return list;
        }
    }
    ```
    > 注意：  
        1. Java写类注释的规范：/** + enter    
        2. public和private使用   
        3. static: method is a function that doesn’t take a self parameter  
        静态方法属于类方法，不属于类对象。可以直接通过类名调用，无需创建类的实例。
        ```Hailstone.hailstoneSequence(83)```
    {: .prompt-tip }

4. final  
: final用于之后不再改变的变量，节约空间并约束之后的修改

5. 注释  
: 除了以上注释形式之外，还应将所有假定（如n为正整数等）条件注释在代码上。  
: // * @param n  starting number for sequence; assumes n > 0.

#### Engineering Code
- **Safe from bugs**
- **Easy to understand**
- **Ready for change**


## Basic Java