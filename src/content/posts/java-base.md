---
title: Java基础补充学习
published: 2026-01-26
description: '个人学习笔记，补足对Java语言的细节学习'
image: ''
tags: [Code,Java]
category: 'Learn'
draft: false 
lang: 'zh_CN'
---




## 语言基础

### 基本数据类型

java中**8种基本数据类型**，4种整型、2种浮点类型、1种字符类型char、1种布尔类型boolean



| 类型  | 存储需求 | 取值范围                                               |
| ----- | -------- | ------------------------------------------------------ |
| int   | 4字节    | -2,147,483,648 ~ 2,147,483,647（正好超过20亿）         |
| short | 2字节    | -32,768 ~ 32,767                                       |
| long  | 8字节    | -9,223,372,036,854,775,808 ~ 9,223,372,036,854,775,807 |
| byte  | 1字节    | -128 ~ 127                                             |



| 类型   | 存储需求 | 取值范围                                         |
| ------ | -------- | ------------------------------------------------ |
| float  | 4字节    | 大约 ±3.40282347E+38F（有效位数为6~7位）         |
| double | 8字节    | 大约 ±1.79769313486231570E+308（有效位数为15位） |



### 除0问题

```java
double zero = 0.9;
double num = 0;
double result = zero/num;
System.out.println(result); // 输出 Infinity
```

**为什么会出现Infinity而不是异常？**

1. **整数除法 vs 浮点数除法**：
   - 整数除以0会抛出`ArithmeticException`
   - 浮点数除以0会得到`Infinity`（无穷大）

2. **IEEE 754标准**：Java的浮点数运算遵循IEEE 754标准，该标准规定：
   - 正数除以0得到`Infinity`
   - 负数除以0得到`-Infinity`
   - 0除以0得到`NaN`（Not a Number）

3. **实际应用**：这种设计允许浮点数运算在数学计算中继续执行，而不是因为除零而中断。





### 大数值

如果基本的整数和浮点数不能满足需求，可以使用`java.math`中两个很有用的类`BigInteger`和`BigDecimal`这两个类可以处理包含**任意长度数字序列**的数值。`BigInteger`实现了任意精度的**整数**运算，`BigDecimal`实现了任意精度的**浮点数**运算。

```java
// 使用静态valueOf方法可以将普通数值转换为大数值
BigInteger a = BigInteger.valueOf(100)
```

大数值的计算**无法使用算数运算符**，而是要使用特定方法add、subtract、multiply、divide、mod（加、减、乘、除、取余数）

```java
BigInteger c = a.add(b) // 相当于c = a + b
```





### 数组拷贝

**引用**

```java
int[] luckyNumbers = smallPrimes;
luckyNumbers[5] = 12;
```

`luckyNumbers`和`smallPrimes`引用同一个数组，所以`smallPrimes[5]`也等于12

**值**

如果希望将一个数组的值拷贝到另一个数组去，需要使用`Arrays`的`copyOf`方法

```java
int copiedLuckyNumbers = Arrays.copyOf(luckyNumbers, luckyNumbers.length);
```

第二个参数是创建的数组长度，所以这个方法还可以用来**增加数组大小**

```java
static type copyOf(type[] a, int start, int end)//另一种方法，起始下标（包含）和终止下标（不包含）
```


*   `static type copyOf(type[] a, int length) 6`
*   `static type copyOf(type[] a, int start, int end) 6`

    返回与a类型相同的一个数组，其长度为length或者 end-start，数组元素为a的值。

    **参数：**
    *   `a`        类型为int、long、short、char、byte、boolean、float或double的数组。
    *   `start`    起始下标（包含这个值）。
    *   `end`      终止下标（不包含这个值）。这个值可能大于a.length。在这种情况下，结果为0或false。
    *   `length`   拷贝的数据元素长度。如果length值大于a.length，结果为0或false；否则，数组中只有前面length个数据元素的拷贝值。


*   `static void arraycopy(Object from, int fromIndex, Object to, int toIndex, int count)`

    将第一个数组中的元素拷贝到第二个数组中。

    **参数：**
    *   `from`        任意类型的数组（在第5章中将解释为什么这个参数的类型是Object）。
    *   `fromIndex`   原始数组中待拷贝元素的起始下标。
    *   `to`          与from同类型的数组。
    *   `toIndex`     目标数组放置拷贝元素的起始下标。
    *   `count`       拷贝的元素数量。





### Java与C++数组区别

1.  **Java 数组的本质是“堆对象” + “引用”**
    *   当在 Java 中写 `int[] a = new int[100];` 时，创建的是一个在堆上分配的、包含 100 个整数的数组对象。
    *   变量 `a` 本身并不是数组本身，而是一个**指向**这个堆上数组对象的**引用**，这和 C++ 中的指针概念非常相似。

2.  **与 C++ 栈数组不同**
    *   `int a[100]; // C++` 这行代码会在**栈**上直接分配一块连续的内存空间来存储 100 个整数，变量 `a` 就是这块内存的起始地址。
    *   Java 没有这种直接在栈上分配固定大小数组的机制。Java 的 `int[] a = new int[100];` 和 C++ 的 `int a[100];` 在内存分配位置和变量本质上有根本区别。

3.  **与 C++ 堆指针等价**
    *   `int* a = new int[100]; // C++` 这行代码在堆上分配了 100 个整数，并将指向这块内存的指针赋值给变量 `a`。
    *   这和 Java 的 `int[] a = new int[100];` 是等价的：两者都是在堆上分配数组，变量 `a` 都是指向该数组的引用/指针。

4.  **Java 数组的安全性设计**
    *   Java 的 `[ ]` 运算符被设计成自动进行**边界检查**。当访问 `a[i]` 时，JVM 会检查 `i` 是否在有效范围内（0 到 `a.length-1`），如果越界会抛出 `ArrayIndexOutOfBoundsException`。
    *   Java **没有指针运算**。不能像在 C++ 中那样对数组名 `a` 进行加减操作（比如 `a+1`）来获得下一个元素的地址或直接访问。你只能通过下标 `a[i]` 来访问元素，这保证了内存安全，避免了因指针错误导致的程序崩溃或安全漏洞。





## 对象与类

### 类与类之间的关系

在类之间，最常见的关系有：依赖（“uses-a”）、聚合（“has-a”）、继承（“is-a”）

- 依赖（dependence），即 “uses-a” 关系，是一种最明显的、最常见的关系。例如，Order 类使用 Account 类是因为 Order 对象需要访问 Account 对象查看信用状态。但是 Item 类不依赖于 Account 类，这是因为 Item 对象与客户账户无关。因此，如果**一个类的方法操纵另一个类的对象**，我们就说一个类依赖于另一个类。应该尽可能地将**相互依赖的类减至最少**。如果类 A 不知道 B 的存在，它就不会关心 B 的任何改变（这意味着 B 的改变不会导致 A 产生任何 bug）。用软件工程的术语来说，就是让类之间的**耦合度最小**。

- 聚合（aggregation），即 “has-a” 关系，是一种具体且易于理解的关系。例如，一个 Order 对象包含一些 Item 对象。聚合关系意味着**类 A 的对象包含类 B 的对象**。
- 继承（inheritance），即 “is-a” 关系，是一种用于表示特殊与一般关系的。**类 A 扩展类 B**，类 A 不但包含从类 B 继承的方法，还会拥有一些额外的功能





### 对象与对象变量

一定要认识到：一个对象变量并没有实际**包**含一个对象，而仅仅**引用**一个对象。在Java中，任何对象变量的值都是对存储在另外一个地方的一个对象的引用。new操作符的返回值也是一个引用。

```java
Date deadline = new Date();
```

有两个部分。表达式 `new Date()` 构造了一个 Date 类型的对象，并且它的值是对新创建对象的**引用**。这个引用存储在变量 `deadline` 中。

可以显式地将对象变量设置为 `null`，表明这个对象变量目前没有引用任何对象。

```java
deadline = null;
. . .
if (deadline != null)
    System.out.println(deadline);
```

如果将一个方法应用于一个值为 `null` 的对象上，那么就会产生运行错误。

```java
birthday = null;
String s = birthday.toString(); // runtime error!
```

变量不会自动地初始化为 `null`，而必须通过调用 `new` 或将它们设置为 `null` 进行初始化。









### 隐式参数与显式参数

```java
public void raiseSalary(double byPercent)
{
    double raise = salary * byPercent / 100;
    salary += raise;
}

```

raiseSalary方法有两个参数。第一个参数被称为**隐式（implicit）参数**，是出现在方法名前的 **Employee类对象**。第二个参数位于方法名后面**括号中的数值**，这是一个**显式（explicit）参数**。

已经看到，显式参数是明显地列在方法声明中的显示参数，例如double byPercent。隐式参数没有出现在方法声明中。

在每一个方法中，关键字this表示隐式参数。如果需要的话，可以用下列方式编写 raiseSalary方法：

```java
public void raiseSalary(double byPercent)
{
    double raise = this.salary * byPercent / 100;
    this.salary += raise;
}
```

| 参数类型                | 说明                                     | 示例               |
| ----------------------- | ---------------------------------------- | ------------------ |
| **隐式参数 (implicit)** | 调用方法的对象本身，用 `this` 关键字表示 | `this.salary`      |
| **显式参数 (explicit)** | 方法括号中声明的参数                     | `double byPercent` |







### Java 可变对象访问器方法笔记

#### **核心警告**

**不要编写返回引用可变对象的访问器方法**

#### **问题示例**

```java
class Employee {
    // 错误示范：直接返回可变对象的引用
    public Date getHireDay() {
        return hireDay;
    }
    
    private Date hireDay;
}
```

#### 为什么这样会破坏封装性？

```java
Employee harry = ...;
Date d = harry.getHireDay();
double tenYearsInMilliSeconds = 10 * 365.25 * 24 * 60 * 60 * 1000;
d.setTime(d.getTime() - (long) tenYearsInMilliSeconds);
// 这样就能给 Harry 增加 10 年工龄！
```

**出错原因**：`d` 和 `harry.hireDay` 引用的是**同一个对象**。对 `d` 调用更改器方法，就可以**自动地改变**这个雇员对象的私有状态！

#### **解决方案：使用克隆（clone）**

如果需要返回一个可变对象的引用，**应该首先对它进行克隆**。

对象克隆是指存放在另一个位置上的对象副本。

```java
class Employee {
    // 正确示范：返回克隆后的对象
    public Date getHireDay() {
        return (Date) hireDay.clone();
    }
}
```

#### **经验总结**

> **如果需要返回一个可变数据域的拷贝，就应该使用克隆。**

#### **要点回顾**

| 要点         | 说明                                       |
| ------------ | ------------------------------------------ |
| **问题**     | 访问器方法直接返回可变对象引用会破坏封装性 |
| **原因**     | 外部代码可以通过返回的引用修改对象内部状态 |
| **解决方案** | 使用 `clone()` 方法返回对象副本            |
| **适用场景** | 返回 `Date`、数组、集合等可变对象时        |





### 基于类的访问权限 - 为什么可以访问另一个对象的私有字段？

**一个方法可以访问所属类的所有对象的私有数据**。

#### 疑惑点

- `private` 修饰的字段是"私有的"，按常理来说，私有数据应该只有对象自己能访问？

- 但实际上，Java 的访问控制是**基于类**的，而不是**基于对象**的。

- **如果没有这个特性**，每个equal和compareTo函数都需要对对象实现getter方法，不仅麻烦还会破坏封装，得不偿失

####  代码示例解析

```java
class Employee {
    private String name;  // 私有字段
    
    // ...
    
    boolean equals(Employee other) {
        return name.equals(other.name);  // 这里访问了 other 的私有字段！
    }
}
```

典型调用：
```java
if (harry.equals(boss)) ...
```

#### 形象比喻

可以把这个理解为"**家族内部不设防**"：

- `Employee` 类就像一个"家族"
- 同一个家族的成员（`harry`、`boss`）之间可以互相知道对方的"秘密"（私有字段）
- 但外人（其他类）不能知道

#### 总结

| 概念             | 说明                                               |
| ---------------- | -------------------------------------------------- |
| **访问控制级别** | Java 的 `private` 是**类级别**的，不是对象级别的   |
| **实际效果**     | 同一个类的方法可以访问**该类任意对象**的私有成员   |
| **常见应用**     | `equals()`、`compareTo()` 等需要比较两个对象的方法 |















### final修饰可变类可能出现的问题

#### final 的"陷阱"

**`final` 修饰符最适合用于：**

| 类型         | 说明                                      |
| ------------ | ----------------------------------------- |
| 基本数据类型 | `int`, `double`, `boolean` 等             |
| 不可变类     | 如 `String`（每个方法都不会改变对象本身） |

**对于可变对象，`final` 可能造成误解！**

```java
private final Date hiredate;
```

**这里的 `final` 只保证：**

- ✅ `hiredate` 这个**引用**不能指向其他对象

**但它不保证：**
- ❌ `hiredate` 所指向的 **Date 对象内容**不能被修改

```java
// 这是允许的！
hiredate.setTime(System.currentTimeMillis());  // 对象内容被改变了！
```

#### 形象比喻

把 `final` 想象成**门牌号固定**：
- 房子的门牌号（引用）不能换
- 但房子里面的家具（对象内容）可以随便搬动







### Java参数调用采用的不是引用调用

```java
public static void swap(Employee x, Employee y) {
    Employee temp = x;
    x = y;
    y = temp;
}

```

- 调用 swap(emp1, emp2) 时，传递的是引用的副本
- x 和 y 是 emp1 和 emp2 引用的拷贝
- 在方法内部交换 x 和 y，只是交换了这两个局部变量的指向
- 原始的 emp1 和 emp2 完全不受影响

>  在C++中，参数传递支持引用传递，x 和 y 不是副本，而是原始变量的别名，修改 x 和 y 就是直接修改原始变量







