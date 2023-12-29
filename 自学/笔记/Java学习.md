# Java学习

## 入门基础

### Java简介

Java介于编译型语言和解释型语言之间，编译为抽象的CPU指令（字节码）。因此对于不同平台需要开发不同的虚拟机对指令进行加载并执行。

- JDK和JRE的关系

<img src="C:\Users\19234\AppData\Roaming\Typora\typora-user-images\image-20230807140802831.png" alt="image-20230807140802831" style="zoom:50%;" />

JDK可执行文件：

- java：这个可执行程序其实就是JVM，运行Java程序，就是启动JVM，然后让JVM执行指定的编译后的代码；
- javac：这是Java的编译器，它用于把Java源码文件（以`.java`后缀结尾）编译为Java字节码文件（以`.class`后缀结尾）；
- jar：用于把一组`.class`文件打包成一个`.jar`文件，便于发布；
- javadoc：用于从Java源码中自动提取注释并生成文档；
- jdb：Java调试器，用于开发阶段的运行调试。

### 程序基础

- 基本类型

浮点数：是`近似`的表达，在比较浮点数是否相等时，需判断两个浮点数之差的绝对值是否小于一个很小的数

布尔类型：布尔运算存在`短路运算`，即如果一个布尔运算的表达式能提前确定结果，则后续的计算不再执行，直接返回结果。

- 引用类型

字符串：字符串的不可变是指字符串内容不可变，但变量可以指向不同的内容

数组

注：null 不等同于空字符串 “”

## 面向对象

一个Java源文件可以包含多个类的定义，但只能定义一个public类，且public类名必须与文件名一致

### 构造方法

在Java中，创建对象实例的时候，按照如下顺序进行初始化：

1. 先初始化字段，例如，`int age = 10;`表示字段初始化为`10`
2. 执行构造方法的代码进行初始化

一个构造方法可以通过this()调用其他构造方法

### 继承

子类无法访问父类private属性的字段，但可以访问protected属性的

- super

1. 表示父类。子类引用父类的字段时，可以用`super.fieldName`，等同于this.fieldName, fieldName
2. super()：子类中如果没有明确地调用父类的构造方法，编译器会帮我们自动加一句`super()`。如果父类中没有定义无参数的构造方法则会报错

解决：super(fieldName1,fieldName2,...)；父类中定义无参数构造

- final

1. 父类方法不想被重写，用final定义
2. 一个类不希望任何其他类继承自它，那么可以把这个类本身标记为final
3. final修饰的field必须在创建对象时初始化，随后不可修改。

- 向上转型

子类类型安全地变为父类类型

- 向下转型

父类类型强制转型为子类类型

```java
Person p1 = new Student(); // upcasting, ok
Student s1 = (Student) p1; // ok
```

在向下转型前可以利用instanceof先判断

### 多态

Java的实例方法调用是基于运行时的`实际类型`的动态调用，而非变量的声明类型。

```java
public class Main {
    public static void main(String[] args) {
        Person p = new Student();
        p.run(); // 打印Student.run
    }
}
class Person {
    public void run() {
        System.out.println("Person.run");
    }
}

class Student extends Person {
    @Override
    public void run() {
        System.out.println("Student.run");
    }
}
```

多态是指，针对某个类型的方法调用，其真正执行的方法取决于运行时期`实际类型`的方法。

多态允许添加更多类型的子类实现功能扩展，却不需要修改基于父类的代码

Object的几个重要的方法，必要时可重写：

- `toString()`：把instance输出为`String`；
- `equals()`：判断两个instance是否逻辑相等；
- `hashCode()`：计算一个instance的哈希值。

### 抽象类

抽象类无法被实例化，只能被继承

面向抽象编程：尽量引用**高层类型**，避免引用实际子类型

### 接口

不能有字段（可以有`静态字段`的，并且静态字段必须为`final`类型，可省略public static final），方法省略关键词public abstract

一个类只能继承自另一个类，不能从多个类继承。但是，一个类可以实现`多个`interface

default方法：在接口中完成定义，目的在于新增方法时，不用修改所有子类，只需在父类中完成修改即可

|            | abstract class       | interface                   |
| ---------- | -------------------- | --------------------------- |
| 继承       | 只能extends一个class | 可以implements多个interface |
| 字段       | 可以定义实例字段     | 不能定义实例字段            |
| 抽象方法   | 可以定义抽象方法     | 可以定义抽象方法            |
| 非抽象方法 | 可以定义非抽象方法   | 可以定义default方法         |

- 接口继承

interface继承自interface使用extends

### 静态字段和静态方法

- 静态字段

静态字段只有一个共享“空间”，所有实例都会共享该字段

实例对象并没有静态字段，推荐使用`类型.静态字段`的方式访问静态字段

- 静态方法

调用静态方法则不需要实例变量，通过类名就可以调用

注：因为静态方法属于class而不属于实例，因此，静态方法内部，无法访问`this`变量，也无法访问实例字段，它只能访问静态字段。

### 包

在定义class的时候，我们需要在第一行声明这个class属于哪个包

```java
package ming; // 申明包名ming

public class Person {
}
```

- 包的作用域

一个类总是属于某个包，类名只是一个简写，真正的完整类名是`包名.类名`

在同一个包内，用`public`、`protected`、`private`修饰的字段和方法可以被访问

编写class的时候，编译器会自动帮我们做两个import动作：

1. 默认自动`import`当前`package`的其他`class`；
2. 默认自动`import java.lang.*`

- 内部类

内部类不能单独存在，必须依附于外部类存在

通过调用`Outer`实例的`new`来创建`Inner`实例：

```java
Outer.Inner inner = outer.new Inner();
```

由于在内部，内类可以修改外部类的`private`字段

- 静态内部类

不依附于`Outer`的实例，而是一个完全独立的类，可以访问`Outer`的`private`静态字段和静态方法

- 匿名类

匿名类指的是在声明一个类的同时实例化它，使代码更加简洁精练。

```java
Hero h = new Hero(){
            //当场实现attack方法
            public void attack() {
                System.out.println("新的进攻手段");
            }
        };
        h.attack();
```

```java
// 相当于生成了子类(但匿名了！)此处的方法重写，都是多态
	test.test(new Person() {
        @Override
        void speak() {
            System.out.println("与子类有关的匿名类！");
        }
    });
```

### classpath、jar、模块

- classpath

`classpath`是JVM用到的一个环境变量，它用来指示JVM如何搜索`class`。

- jar包

`jar包`可以把`package`组织的目录层级，以及各个目录下的所有文件（包括`.class`文件和其他文件）都打成一个jar文件（相当于一个目录）

- 模块

比起jar包，解决了jar包之间的依赖问题，可以看做指出依赖关系的jar包的集合

模块的描述文件module-info.java（`hello.world`是模块的名称）：

```java
module hello.world {
	requires java.base; // 可不写，任何模块都会自动引入java.base
	requires java.xml;
}
```

java.xml之所以能够被访问，是因为在java.xml的module-info.java中，

```java
module java.xml {
    exports java.xml;
    exports javax.xml.catalog;
    exports javax.xml.datatype;
    ...
}
```

只有被声明的`导出`的包，外部代码才被允许访问

## Java核心类

### 字符串和编码

`String`内部是通过一个`char[]`数组表示的，字符串具有`不可变性`（由于内部的`private final char[]`字段以及没有修改char[]的方法）

- 字符串比较

使用`equals()`

Java编译器在编译期，会自动把所有相同的字符串当作一个对象放入常量池

- 常用方法

```java
// 是否包含子串:
"Hello".contains("ll"); // true

"Hello".indexOf("l"); // 2

"Hello".lastIndexOf("l"); // 3

"Hello".startsWith("He"); // true

"Hello".endsWith("lo"); // true

//提取子串
"Hello".substring(2); // "llo"
"Hello".substring(2, 4); "ll"
    
//移除字符串首尾空白字符。空白字符包括空格，\t，\r，\n
"  \tHello\r\n ".trim(); // "Hello"

//strip()方法也可以移除字符串首尾空白字符，类似中文的空格字符\u3000也会被移除
"\u3000Hello\u3000".strip(); // "Hello"
" Hello ".stripLeading(); // "Hello "
" Hello ".stripTrailing(); // " Hello"

//判断字符串是否为空和空白字符串
"".isEmpty(); // true，因为字符串长度为0
"  \n".isBlank(); // true，因为只包含空白字符

//替换子串（也可以使用正则表达式）
s.replace('l', 'w'); //所有字符'l'被替换为'w'

//分割字符串，传入的是正则表达式
String[] ss = s.split("\\,"); 

//拼接字符串，使用静态方法join()，它用指定的字符串连接字符串数组
String[] arr = {"A", "B", "C"};
String s = String.join("***", arr); // "A***B***C"

//格式化字符串，字符串提供了formatted()方法和format()静态方法
String s = "Hi %s, your score is %d!";
System.out.println(s.formatted("Alice", 80));

System.out.println(String.format("Hi %s, your score is %.2f!", "Bob", 59.5));

//类型转换，要把任意基本类型或引用类型转换为字符串，使用静态方法valueOf()
String.valueOf(123); // "123"
String.valueOf(true); // "true"

//把字符串转换为其他类型
int n1 = Integer.parseInt("123"); // 123
boolean b1 = Boolean.parseBoolean("true"); // true

//String和char[]类型可以互相转换
char[] cs = "Hello".toCharArray(); // String -> char[]
String s = new String(cs); // char[] -> String
```

- 字符编码

`Unicode`编码：2字节，英文浪费空间

`UTF-8`编码：`1~4字节`的变长编码，依靠高字节位来确定一个字符究竟是几个字节

注：Java的`String`和`char`在内存中总是以Unicode编码表示。

### StringBuilder

为了能高效拼接字符串，Java标准库提供了`StringBuilder`，它是一个可变对象，可以预分配缓冲区，在新增字符时，不会创建新的临时对象

- 链式操作

sb.append().append().append()...

进行链式操作的关键是，定义的`append()`方法会返回`this`，这样，就可以不断调用自身的其他方法

- StringJoiner

分隔符拼接数组，也可以加前后缀

- String.join()

在不需要指定“开头”和“结尾”的时候，用`String.join()`更方便（这个方法在内部使用了StringJoiner）

### 包装类

包装类都是引用数据类型

- 数据转换

```java
int i = 100;
Integer n = Integer.valueOf(i);
int x = n.intValue();
```

- 自动装（拆）箱

编译器在编译阶段完成了基本类和包装类之间的相互转换

```java
Integer n = 100; // 编译器自动使用Integer.valueOf(int)
int x = n; // 编译器自动使用Integer.intValue()
```

装箱和拆箱会影响执行效率，且拆箱时可能发生`NullPointerException`（Integer指向null）

- 不变类

所有的包装类都是不变类，必须使用`equals()`比较：

```java
public final class Integer {
    private final int value;
}
```

**特点：**

1. 定义class时使用`final`，无法派生子类；
2. 每个字段使用`final`，保证创建实例后无法修改任何字段。

- 静态工厂方法

把能创建“新”对象的静态方法称为静态工厂方法。

创建实例时，有两种方法：

**方法1：**`Integer n = new Integer(100);`

**方法2：**`Integer n = Integer.valueOf(100);`

方法2更好，因为方法1总是创建新的`Integer`实例，而方法2是静态工厂方法，它尽可能地返回缓存的实例以节省内存。

- 静态变量

```java
// boolean只有两个值true/false，其包装类型只需要引用Boolean提供的静态字段:
Boolean t = Boolean.TRUE;
Boolean f = Boolean.FALSE;
// int可表示的最大/最小值:
int max = Integer.MAX_VALUE; // 2147483647
int min = Integer.MIN_VALUE; // -2147483648
```

- 类型转换

通过包装类型可以轻松获取各种基本类型：

```java
// 向上转型为Number:
Number num = new Integer(999);
// 获取byte, int, long, float, double:
byte b = num.byteValue();
int n = num.intValue();
```

### 枚举类

```java
enum Weekday {
    SUN, MON, TUE, WED, THU, FRI, SAT;
}
Weekday day = Weekday.SUN;
```

不同类型的枚举不能互相比较或者赋值，因为类型不符。

注：enum虽然是引用类型，但每个常量只对应一个实例，所以比较可以用==表示（更高效）

`enum`是一个`class`，每个枚举的值都是`class`实例，因此，这些实例有一些方法：

- name()

```java
String s = Weekday.SUN.name(); // "SUN"
```

- ordinal()

返回定义的常量的顺序，从0开始计数

### 记录类

使用record修饰的类，可以一行实现`不变类`的编写：

- 可以编写Compact Constructor（没有小括号，只有大括号）对参数进行验证；
- 可以定义静态方法

### BigInteger

用来表示任意大小的整数。原理是`BigInteger`内部用一个`int[]`数组来模拟一个非常大的整数

对`BigInteger`做运算的时候，只能使用提供的实例方法

### BigDecimal

表示一个任意大小且精度完全准确的浮点数

- 比较BigDecimal

总是使用`compareTo()`比较两个BigDecimal的值，不要使用equals()，因为使用equals()法不但要求两个BigDecimal的值相等，还要求它们的scale()相等

### 常用工具类

- Math：数学计算
- Random：生成伪随机数
- SecureRandom：生成安全的随机数

## 异常处理

### Java的异常

Java内置了一套异常处理机制，总是使用异常来表示错误。异常是一种`class`，它本身带有类型信息

<img src="C:\Users\19234\AppData\Roaming\Typora\typora-user-images\image-20230821180255968.png" alt="image-20230821180255968" style="zoom: 50%;" />

`Error`表示严重的错误，程序对此一般无能为力；而`Exception`则是运行时的错误，它可以被捕获并处理。

Java规定（编译器规定）：

- 必须捕获的异常，包括`Exception`及其子类，但不包括`RuntimeException`及其子类，这种类型的异常称为Checked Exception。
- 不需要捕获的异常，包括`Error`及其子类，`RuntimeException`及其子类。

捕获异常：

1. 使用`try...catch`语句
2. 在方法定义时在后面直接加throws Exception...

方法声明的Checked Exception，也可以在`更高`的调用层捕获

- 多catch语句

JVM在捕获到异常后，会从上到下匹配`catch`语句，匹配到某个`catch`后，执行`catch`代码块，然后不再继续匹配。

存在多个`catch`的时候，`catch`的顺序非常重要：子类必须写在前面。

一个`catch`语句也可以匹配多个非继承关系的异常，用|合并

- finally语句

`finally`语句块保证有无错误都会执行，且总是`最后执行`

执行顺序：try --> catch中非throw 语句 --> finally --> catch中的throw语句

### 空指针

try... catch `捕捉 `”空指针“ 位置

private String name = ""; 初始化成员对象以`避免`空指针

## 反射

### 信息获取

每加载一种`class`，JVM就为其创建一个`Class`类型的实例（这里的`Class`类型是一个名叫`Class`的`class`）

```java
public final class Class {
    private Class() {}
}
```

通过`Class`实例获取`class`信息的方法称为反射。反射是为了解决在运行期，对某个实例一无所知的情况下可以调用其方法。

- 如何获取一个`class`的`Class`实例？有三个方法：

方法一：直接通过一个`class`的静态变量`class`获取：

```java
Class cls = String.class;
```

方法二：如果我们有一个实例变量，可以通过该实例变量提供的`getClass()`方法获取：

```java
String s = "Hello";
Class cls = s.getClass();
```

方法三：如果知道一个`class`的完整类名，可以通过静态方法`Class.forName()`获取：

```java
Class cls = Class.forName("java.lang.String");
```

- 通过`Class.newInstance()`可以创建类实例，它的局限是：只能调用`public`的无参数构造方法。

```java
// 获取String的Class实例:
Class cls = String.class;
// 创建一个String实例:
String s = (String) cls.newInstance();
```

JVM总是动态加载`class`，可以在运行期根据条件来控制加载class

- 通过反射获取字段的信息

```java
Field f = String.class.getDeclaredField("value");
f.getName(); // "value"
f.getType(); // class [B 表示byte[]类型
```

添加 f.setAccessible(true);语句可以访问其他类的`private字段`

- 反射也可获取方法信息，并调用方法（遵循多态原则）

### 动态代理

没有实现类但是在运行期动态创建了一个接口对象的方式，称为`动态代码`。

JDK提供的动态创建接口对象的方式，就叫`动态代理`。

在运行期动态创建一个`interface`实例的方法如下：

1. 定义一个`InvocationHandler`实例，它负责实现接口的方法调用；
2. 通过Proxy.newProxyInstance()创建interface实例，它需要3个参数：
   - 使用的`ClassLoader`，通常就是接口类的`ClassLoader`；
   - 需要实现的接口数组，至少需要传入一个接口进去；
   - 用来处理接口方法调用的`InvocationHandler`实例。
3. 将返回的`Object`强制转型为接口。

- ClassLoader 和 getClass的区别？

ClassLoader的任务是将类的二进制字节流读入到JVM中，然后将其规则化变成一个JVM能识别的class对象

getClass是获取类的相关属性，从而进行调用

### 注解

Java的注解可以分为三类：

- 第一类是由编译器使用的注解，例如：`@Override`，这类注解不会被编译进入`.class`文件，它们在编译后就被编译器扔掉了。

- 第二类是由工具处理`.class`文件使用的注解，比如有些工具会在加载class的时候，对class做动态修改，实现一些特殊的功能。这类注解会被编译进入`.class`文件，但加载结束后并不会存在于内存中。

- 第三类是在程序运行期能够读取的注解，它们在加载后一直存在于JVM中，这也是`最常用`的注解。

#### 定义注解

使用`@interface`语法来定义注解（`Annotation`）

```java
// 定义注解
public @interface MyAnnotation {
    String value(); // 定义一个元素，名称为 value，类型为 String
    int count() default 0; // 定义一个元素，名称为 count，类型为 int，默认值为 0
}

// 使用注解，传入相应的值
@MyAnnotation(value = "Hello", count = 5)
public class MyClass {
    // ...
}
```

interface和@interface的区别？

interface定义的是接口，而@interface定义的是注解。

#### 元注解

作用：修饰其他注解

- @Target：定义`注解`能够被应用于源码的哪些位置

- @Retention：定义注解的生命周期（SOURSE编译时，CLASS类加载时，`RUNTIME运行时`）

#### 注解处理

使用反射API读取Annotation：

- Class.getAnnotation(Class)
- Field.getAnnotation(Class)
- Method.getAnnotation(Class)
- Constructor.getAnnotation(Class)

方法注解的接受需要二维数组（参数有多个，每个参数可以有多个注解）

## 泛型

### 使用泛型

- 泛型类
- 泛型接口
- 泛型方法

**静态泛型方法：**由于静态方法的加载先于类的实例化，于是静态方法中并没有加载真正的参数类型，所以静态方法的泛型和类的泛型没有关系，可写为：

```java
public static <K> Pair<K> create(K first, K last) {
        return new Pair<K>(first, last);
    }
```

第一个<T>为方法的泛型，第二个<T>是返回值的泛型

可以同时使用多个**泛型类型**

### 擦拭法

泛型的实现原理：

- 类编译时，编译器把类型`<T>`视为`Object`；
- 实例化时，编译器根据`<T>`实现安全的强制转型。

- 局限：

1. 泛型类型只能是**包装类**
2. 无法取得带泛型的`Class`（不同实例返回的都是同一个Class）
3. 不能实例化`T`类型

要实例化`T`类型，我们必须借助额外的`Class<T>`参数：

```java
public class Pair<T> {
    private T first;
    private T last;
    public Pair(Class<T> clazz) {
        first = clazz.newInstance();
        last = clazz.newInstance();
    }
}
```

要注意不恰当的覆写方法：

```java
public class Pair<T> {
    public boolean equals(T t) {
        return this == t;
    }
}
```

定义的`equals(T t)`方法实际上会被擦拭成`equals(Object t)`，而这个方法是继承自`Object`的，编译器会阻止一个实际上会变成覆写的泛型方法定义。

### 泛型类的继承

子类必须知道父类的泛型类型，即需要获取父类的泛型类型，才能继承

### extends通配符

**上界通配符`extends`：**

Pair<? extends Number> 使得方法接收所有泛型类型为`Number`或`Number`子类的`Pair`类型

Pair<Integer>`不是`Pair<Number>的子类

**局限：**

无法传入任何方法参数（除了null，但会抛出空指针的问题）--> 编译器无法保证`setFirst`方法接收的参数类型与`Pair`对象的类型参数相匹配

```java
static int add(Pair<? extends Number> p) {
        Number first = p.getFirst();
        Number last = p.getLast();
        p.setFirst(new Integer(first.intValue() + 100));
        p.setLast(new Integer(last.intValue() + 100));
        return p.getFirst().intValue() + p.getFirst().intValue();
    }
class Pair<T> {
    ...
}
```

**使用extends限定T类型**

在定义泛型类型`Pair<T>`的时候，也可以使用`extends`通配符来限定`T`的类型：

```java
public class Pair<T extends Number> { ... }
```

### super通配符

参数可以接受其父类

```java
void set(Pair<? super Integer> p, Integer first, Integer last) {
    ...
}
```

## 集合

Java的`java.util`包主要提供了以下三种类型的集合：

- `List`：一种有序列表的集合，包括ArrayList和LinkedList等
- `Set`：一种保证没有重复元素的集合
- `Map`：一种通过键值（key-value）查找的映射表集合

Java集合设计的三大特点：

- 接口和实现类分离，体现面向抽象编程的思想
- 支持泛型
- 使用迭代器来实现访问集合

### List

ArrayList对数据的操作类似于数组，但是对操作进行了封装

LinkedList对数据的操作类似于链表

- 集合访问：迭代器
- 判断元素是否相等：equals方法（如果是自己写的类需要正确`覆写`equals方法）

### Map

#### HashMap

`空间换时间`，查找效率非常高

- 正确使用Map必须保证（如何通过key找到value？）

1. 正确性：equals方法判断是否是同一个对象
2. 搜索效率：调用key的hashCode()，将不同的key映射到不同的存储空间（不同的对象返回相同的hashCode()对造成存储冲突，降低效率）

#### EnumMap

如果作为key的对象是`enum`类型，可以使用EnumMap，在内部以紧凑的数组存储value

优点：效率高，且没有额外的空间浪费

#### TreeMap

一般的Map都是`无序的`，而SortedMap会对Key进行`排序`，TreeMap是其实现类

因此，TreeMap中的key必须实现Comparable接口，如果key本身不支持，需要在实例化时实现Comparable接口，重写compare方法（return -1,0,1)

### Set

只存储不重复的key，同样需要实现equals()和hashCode() --->Set的底层是Map

同样有HashSet、TreeSet

### Queue

|                    | throw Exception | 返回false或null    |
| :----------------- | :-------------- | ------------------ |
| 添加元素到队尾     | add(E e)        | boolean offer(E e) |
| 取队首元素并删除   | E remove()      | E poll()           |
| 取队首元素但不删除 | E element()     | E peek()           |

#### PriorityQueue

对`PriorityQueue`调用`remove()`或`poll()`方法，返回的总是优先级最高的元素，所以放入的元素必须实现Comparable接口，否则需要提供Comparator对象来判断元素顺序

#### Dequeue

双端队列：

- 既可以添加到队尾，也可以添加到队首；
- 既可以从队首获取，又可以从队尾获取。

### Stack

Java中没有Stack接口，但是可以通过Deque当做Stack使用

### Collections

是jdk提供的工具类，提供了一系列的静态方法，操作各种集合



