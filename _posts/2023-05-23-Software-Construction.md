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

## Testing  
#### Why
- 检查代码，保证正确性
- 很多时候会觉得测试环节没有必要，因为自己写的测试用例反正也会在代码中实现。其实更重要的原因是为CI或者其他人在之后修改代码的时候做出校验。在实习期间出现过类似情况，由于没办法完全理解之前同事的工作内容，导致业务逻辑改变的时候无法肯定能否修改其中一些变量而出现错误。如果能够在测试用例中显示相关逻辑，保证所有的method和class都能有相对应的测试用例为期上线作保证，那可以节约时间也能确保安全。

#### Test-first programming   
规范的代码编写流程:  
**write the spec and the tests before you even write any code**
1. Spec: Write a specification for the function
2. Test: Write tests that exercise the specification
3. Implement: Write the implementation

> 先确定代码功能与测试用例再实现功能  
{: .prompt-info }

#### How  
如何使测试用例显得不多余，不与代码重复除了Test-first先完成测试用例再写代码以外还需要一定的思想。  
针对于输入参数，根据功能将参数分成不同的subdomains，对于每种subdomains编写测试用例。  

1. abs:  
```java
/**
 * ...
 * @param a  the argument whose absolute value is to be determined
 * @return the absolute value of the argument.
 */
public static int abs(int a)
```
Partitions 
: a >= 0; a < 0

2. max:
Partitions
: a < b; a > b; a = b

    > 根据代码功能划分  
    {: .prompt-info }

3. multiply : BigInteger × BigInteger → BigInteger  
- a and b are both positive
- a and b are both negative
- a is positive, b is negative
- a is negative, b is positive
 - a or b is 0, because the result is always 0
 - a or b is 1, the identity value for multiplication

    > 划分应额外注意边界条件 
    {: .prompt-info }  

    对于每一个a和b，应分成 6 * 6 = 36 种分区，形成36个测试用例
    ![](../../pic/multiply-partition.png)

    若出现过多测试用例，可将a，b分别划分分区，变成 6 + 6， 再增加考虑两者关系的测试用例
    ```java
    // partition on a:
    //   a = 0
    //   a = 1
    //   a is small integer > 1
    //   a is small integer < 0
    //   a is large positive integer
    //   a is large negative integer
    //      (where "small" fits in long, and "large" doesn't)
    // partition on b:
    //   b = 0
    //   b = 1
    //   b is small integer > 1
    //   b is small integer < 0
    //   b is large positive integer
    //   b is large negative integer

    // partition on signs of a and b:
    //    a and b are both positive
    //    a and b are both negative
    //    a positive and b negative
    //    a negative and b positive
    //    one or both are 0
    ```

#### 编写规范  
```java
public class JavaGeneralTest {
    /**
     * Testing strategy
     * <p>
     * partition:
     *   a < b
     *   a > b
     *   a = b
     */

    // covers a < b
    @Test
    public void testALessThanB() {
        assertEquals(2, Math.max(1, 2));
    }
}
```
> stategy + partition + covers for every test case
{: .prompt-tip } 

#### Unit and integration testing
Using @Mock to isolate different dependencies

#### Automated regression testing
Automated
: 之前提到的测试用例最大的帮助不是立即对于你写的代码做出维护和保证，而是在CI或者之后的修改中做出核验

Regression
: 修复错误后，把错误样例输入自动化测试中。因为该错误可能很容易再犯

#### Summary
1. Test-first programming
: 最重要的思想！！！
2. Unit-testing each module
: Mock掉所有的dependency，包括但不限于curl, other funcs, api, database, etc
3. Partitioning
: Unit and multi-partition
4. Coding standard
: Stategy + partition + covers for every test case

## Code Review, Coding style  
#### Don’t repeat yourself (DRY)  
复制代码是十分危险的，安全性和可读性都会大大降低，对于需要复用的方法可以通过接口封装成各类器使用，也可直接在本类中将复杂逻辑分成不同的方法，绝对避免重复使用类似逻辑代码。

#### Comments Standard  
- 每一个method和class上方都应通过 ```/** + return ``` 添加comment
- 所有引用也应增加详细说明
- 对于单行晦涩代码也应添加comment，加在右侧和上方均可

#### Avoid magic numbers
- 不应出现除了0，1以外的直接数字
- 复用数字应用static final修饰

#### Name of variables
- 一个变量只应有一个用途
- 每一个变量名称十分重要，不应使用temp，data这种没有意义的变量名称

#### Don’t use global variables
```public static String longestWord;```
: 不应使用static变量，**global variable**十分不安全并且浪费维护资源

#### Avoid special-case code
有些时候使用if-else能够提高时间复杂度，但是会影响可读性与安全性，除非能在指数级别上提高时间复杂度，否则不建议使用

#### Summary
1. DRY
2. Comments where needed
3. Fail fast
4. Avoid magic numbers
5. One purpose for each variable
6. Use good names
7. Use whitespace to help the reader
8. Don’t use global variables
9. Methods should return results, not print them
10. Avoid special-case code

> 所有图方便快速的想法都很危险，代码不应图简便，实现逻辑仅仅只是最简单的一步
{: .prompt-danger } 

## Version Control
[Pro Git](https://git-scm.com/book/zh/v2)

## Specifications(技术规范)
#### Javadoc
之前已经对于注解的格式有了一定的说明，但是还是有些地方有所不足。  
ex:
```java
static int find(int[] arr, int val) {
    for (int i = 0; i < arr.length; i++) {
        if (arr[i] == val) return i;
    }
    return -1;
}

static int find(int[] arr, int val) {
    for (int i = 0, j = arr.length-1; i <= j; i++, j--) {
        if (arr[i] == val) return i;
        if (arr[j] == val) return j;
    }
    return -1;
}
```
前置条件
: 两段代码想要完成的目标是一样的但是由于数组中可能出现多个相同的val，导致有可能结果一前一后。  
应改为: 

```java
static int find(int[] arr, int val)
  requires: val occurs exactly once in arr
  effects : returns index i such that arr[i] = val
```

<h4>Specification(method and class)</h4>
 - a method signature, giving the name, parameter types, return type, and exceptions thrown
 - a requires clause, describing additional restrictions on the parameters
 - an effects clause, describing the return value, exceptions, and other effects of the method

```java
/**
 * Find a value in an array.
 * @param arr array to search, requires that val occurs exactly once
 *            in arr
 * @param val value to search for
 * @return index i such that arr[i] = val
 */
static int find(int[] arr, int val)
```
> method任务介绍，@param：先决条件，@return：后置影响
{: .prompt-tip } 

#### 规范涵盖内容
- 参数和返回值
- 不应该谈论局部变量或方法类的私有字段

#### 测试规范
- 不要做超出规范的假设
- 一个好的单元测试仅关注单个规范

#### Exceptions

## Designing Specifications
