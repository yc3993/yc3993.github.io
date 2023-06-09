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

#### Tips
1. assertNotNull谨慎使用，有可能是empty

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
优秀的规范标准:
- coherent
: 一个spec只应该完成连贯的事情，具有原子性（这点没太理解）

- informative
: 返回值应具有意义，一面的例子返回值如果是空不能判断字典中指定元素是不存在还是本来就是空
```java
static V put(Map<K,V> map, K key, V val)
requires:
val may be null, and map may contain null values
effects:
inserts (key, val) into the mapping, overriding any existing mapping for key, and returns old value for key, unless none, in which case it returns null
```
- strong
: 对编码者足够有力的保证

- weak
: 对使用者要有一定的宽松选择

- abstract
: 返回值尽量使用抽象类型，如List而非ArrayList
```java
static ArrayList<T> reverse(ArrayList<T> list)
  effects: returns a new list which is the reversal of list,
           i.e. newList[i] = list[n-i-1] for all 0 ≤ i < n, where n = list.size()
```

## Mutability & Immutability
1. 可变类型相对于不可变类型大部分情况下可以节约资源（减少复制次数）
2. 可变类型会增加错误风险

## Avoiding Debugging
#### Make bugs impossible
多使用不可变量，如final，string这些变量维护代码安全性

#### Localize bugs
对于方法的前提条件可以加exception
```java
/**
 * @param x  requires x >= 0
 * @return approximation to square root of x
 */
public double sqrt(double x) { 
    if (! (x >= 0)) throw new IllegalArgumentException("required x >= 0, but was: " + x);
    ...
}
```

#### Modularity & encapsulation
1. 模块化
: 不同模块之前隔离开来，debug的时候可以更快找到问题
2. 封装
: 模块负责自身的内部行为，并且系统其他部分中的错误不会破坏其完整性。将变量范围控制在class或者method内。

## Abstract Data Types
- ADT注重于操作
- 一个规范的ADT应包含创建者，生产者，观察者和变换者
- 好的 ADT 是简单，连贯，适当且独立于表示的
- 通过为每个操作生成测试来测试 ADT，但在同一测试中同时使用创建者，生产者，变换者和观察者

## Recursion
#### 何时使用递归而不是迭代
1. 问题具有递归的性质：有些问题本身就可以自然地分解为更小的子问题，而这些子问题与原问题的解决方法相同，只是规模更小。在这种情况下，递归通常是一个自然而直观的选择。比如树的遍历、图的深度优先搜索等。

2. 问题的解决方法依赖于前一步的解：有些问题的解决方法需要利用前一步或前几步的解。递归提供了一种自然的方式来处理这种依赖关系，因为每次递归调用都可以访问上一步的解。比如斐波那契数列的计算，每个数都是前两个数之和，可以使用递归来定义。

3. 数据结构的递归定义：如果要处理的数据结构本身具有递归定义，递归算法通常是最自然的选择。比如文件系统、链表、树等数据结构可以很自然地使用递归算法进行操作。

但是递归算法可能会导致内存消耗增加的主要原因是每次递归调用都会在内存中创建一个新的函数调用帧（也称为栈帧）来保存当前的函数执行状态和局部变量。随着递归的进行，每个递归调用都会在栈上分配一部分内存，而这些内存只有在递归的最深层完成后才会被释放。

当递归深度较大时，会产生大量的函数调用帧堆积在内存中，导致内存消耗增加。如果递归深度超过系统或编程语言的栈大小限制，就会发生栈溢出错误。

所以多数情况可以考虑动态规划解决递归的内存消耗问题。

## Equality
#### 不可变量等价关系
```java
public class Duration {
    ...   
    // Problematic definition of equals()
    public boolean equals(Duration that) {
        return this.getLength() == that.getLength();        
    }
}
```
> 错误原因：没有override，引用新对象时会导致使用原equals方法 

**正确做法**

```java
@Override
public boolean equals(Object that) {
    return that instanceof Duration && this.sameValue((Duration)that);
}

// returns true iff this and that represent the same abstract value
private boolean sameValue(Duration that) {
    return this.getLength() == that.getLength();
}
```
> instanceof
: 用来测试一个实例是否属于特定的类型

**Java String Example:**
```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        return (anObject instanceof String aString)
                && (!COMPACT_STRINGS || this.coder == aString.coder)
                && StringLatin1.equals(value, aString.value);
}
```

#### 可变类型相等
直接比较会出现问题，Set中含有List，修改List会使得HashCode不相同
```java
public void WhyObservationalEqualityHurts() {
        List<String> list = new ArrayList<>();
        list.add("a");
        Set<List<String>> set = new HashSet<List<String>>();
        set.add(list);
        System.out.println("set.contains(list)=" + set.contains(list));
        list.add("goodbye");
        System.out.println("set: " + set);
        System.out.println("set.contains(list)=" + set.contains(list));
// set.contains(list)=true
// set: [[a, goodbye]]
// set.contains(list)=false
    }
```
所以可变类型equal一般基于引用比较

## Java Streaming
#### Streaming优点
- 减少中间变量temp
- 迭代器思想，处理过的数据不消耗内存
- 并行运算

#### Example
```java
package words;

import java.io.File;
import java.io.IOException;
import java.io.UncheckedIOException;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;
import java.util.stream.Stream;

/**
 * Words example: Java implementation with map/filter/reduce.
 */
public class Words2 {
    
    /**
     * Find all files in the filesystem subtree rooted at folder.
     * @param folder root of subtree, requires folder.isDirectory() == true
     * @return stream of all ordinary files (not folders) that have folder as
     *         their ancestor
     */
    static Stream<File> allFilesIn(File folder) {
        File[] children = folder.listFiles();
        Stream<File> descendants = Arrays.stream(children)
                                         .filter(File::isDirectory)
                                         .flatMap(Words2::allFilesIn);
        return Stream.concat(descendants,
                             Arrays.stream(children).filter(File::isFile));
    }
    
    /**
     * Make a filename suffix testing predicate.
     * @param suffix string to test
     * @return a predicate that returns true iff the filename ends with suffix.
     */
    static Predicate<File> endsWith(String suffix) {
        return f -> f.getPath().endsWith(suffix);
    }
    
    /**
     * Print the words in Java files in the project.
     * @param args unused
     * @throws IOException if error while reading files or folders
     */
    public static void main(String[] args) throws IOException {
        Stream<File> files = allFilesIn(new File("."))
                              .filter(endsWith(".java"));
        Stream<Path> paths = files.map(File::toPath);
        Stream<List<String>> fileContents = paths.map(path -> {
            try {
                return Files.readAllLines(path);
            } catch (IOException ioe) {
                throw new UncheckedIOException(ioe);
            }
        });
        Stream<String> lines = fileContents.flatMap(List::stream);
        Stream<String> words = lines.flatMap(line -> Arrays.stream(line.split("\\W+"))
                                                     .filter(s -> s.length() > 0));
        words.forEach(System.out::println);
    }
}
```

#### 并行操作
```Stream<Path> paths = files.map(File::toPath);```  
map操作可以并行运算   
```Stream<Path> paths = files.parallel().map(File::toPath);```
