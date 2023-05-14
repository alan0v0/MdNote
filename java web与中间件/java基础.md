

Java基础、Java IO（序列化、BIO、NIO、AIO）、Java并发（同步控制、线程池、ThreadLocal、volatile）、Java虚拟机（内存、垃圾回收、类加载、JMM）、Spring（基础+源码，IoC、AOP、事务）、MyBatis（基础+源码）、MySQL（数据库引擎、事务管理、索引、锁）、计算机网络（TCP&UDP、HTTP）、操作系统（线程&进程）、数据结构（基础+JDK源码，List、Hash）、算法（基础+刷题）、设计模式等。

## 编译解释型语言

1. 编译型 vs 解释型

2. 静态 vs 动态

   主要区别是 是否需要指定变量数据类型

3. 强类型 vs 弱类型

   强类型语言不允许隐式类型转换（待确认）
## 基本数据类型

> 小米视频面试中被问到，我立刻说有8种，但是列举过程了最后才想起来char，彻底忘了byte类型

**byte（字节型）**、**char（字符型）**、short（短整型）、int（整型）、long（长整型）、float（单精度浮点型）、double（双精度浮点型）、boolean（布尔型）

![1568947543246](C:\Users\ECUST\AppData\Roaming\Typora\typora-user-images\1568947543246.png)

## 修饰符/关键字

### protected 
https://blog.51cto.com/zhangjunhd/19287
protected 可以修饰方法和变量，protected 包含default的访问域。

* 基类的 protected 成员是包内可见的，并且对子类可见；
* 若子类与基类不在同一包中，那么在子类中，子类实例可以访问其从基类继承而来的protected方法，而不能访问**基类实例**的protected方法。

**什么情况下用protected 修饰符？**

### final 关键字

1. 修饰类

2. 修饰方法，不允许被重写

3. 修改变量（成员变量 局部变量）

   基本数据类型、对象引用

4. 方法形参

### native 关键字



### instanceof



## 字符串

1. UTF-16代码单元  码点code point
1. String StringBuilder StringBuffer
* String类，不可变，没有提供用于修改字符串的方法，每次对 String 变量进行改变的时候其实都等同于生成了一个新的 String 对象，然后将指针指向新的 String 对象。好处是天生线程安全，共享带来的高效率远胜于提取、拼接字符串带来的低效率。
* StringBuffer 线程安全，提供方法对内部字符序列直接修改，主要方法是`append()` `insert()`
* StringBulder API与StringBuffer一致，方法未同步，单线程下性能高

## Object类

### 实例方法

```java
String toString()
boolean equals(Object obj)
int hashCode()

//线程同步有关 synchronized
void wait()
void wait(long timeout)
void wait(long timeout, int nanos) //纳秒
void notify()
void notifyAll()

Class<?> getClass()
protected void finalize()
protected Object clone()
```



### equals() 与 hashCode() 

1. 若重写equals，必须重写hashCode()

如果两个对象实例相等，即equals()返回true。那么hashCode()必须返回值相等。

## 内部类 匿名类 lambda表达式

1. JVM什么时候加载内部类
参考链接：[内部类加载及单例模式](https://blog.csdn.net/zmx729618/article/details/69227762)
加载一个类时，**其内部类不会同时被加载**。一个类（包括常规类）被加载，当且仅当其某个静态成员（静态域、构造器、静态方法等）被调用时发生。
2. 内部类修饰符
* public 外部可通过 OuterClassName.InnerClassName  obj 方式获取内部类对象引用 

  外部可以通过以下方法生成内部类实例 OuterClassName.InnerClassName inner=OuterClassName.new n内部类构造器() 生成内部类对象

* private 外部无法直接接收内部类对象引用，可以用该私有内部类所实现的接口接收内部类对象引用

* static 表明内部类未使用外部类实例变量
3. 局部内部类
* 在方法中声明的类，不使用public private控制符修饰，作用域被固定在方法内。
* 局部内部类，可以访问方法局部变量，和实例变量，局部内部类中引用的外部局部变量必须为final。 

## 移位操作符
原码：由符号位（负数为1）和数值位组成
负数的反码:符号位保持不变、数值位按位取反
负数的补码等于它的反码末位加1, 即[X]补＝[X]反＋1
[－0]原＝10000000B  [－0]补＝00000000B
-0的反码是1111 1111， 补码+1，符号位参与了运算
```
>> 是带符号右移，若左操作数是正数，则高位补“0”，若左操作数是负数，则高位补“1”.
<< 将左操作数向左边移动，并且在低位补0.
>>> 是无符号右移，无论左操作数是正数还是负数，在高位都补“0”
```

## 枚举类

https://zhuanlan.zhihu.com/p/43871734

## 反射
---
1. 反射机制
Java语言允许通过程序化的方式间接对类进行操作。类文件由类装载器装载后，JVM为其生成一个对应的java.lang.Class对象，该Class对象描述类的结构信息，通过Class对象可获取构造函数、成员变量、方法的反射对象，通过调用反射对象可实现对类对象进行间接操作，获取类对象提供的服务。
2. 代码
获取Class对象有三种方式:
	1. objectName.getClass( )
	2. className.class      类名.class
	3. Class.forName("ClassAbsolutePath")

## 注解
----
>参考资料
>[javazejian博客](https://blog.csdn.net/javazejian/article/details/71860633#%E5%A3%B0%E6%98%8E%E6%B3%A8%E8%A7%A3%E4%B8%8E%E5%85%83%E6%B3%A8%E8%A7%A3)

1. java语言元注解，用于约束自定义的注解

| 元注解      | 作用                                                         |
| ----------- | ------------------------------------------------------------ |
| @Target     | 指定可标注的元素类型                                         |
| @Retention  | 约束该注解的生命周期                                         |
| @Inherited  | 属于标记注解（无属性），若一个注解@A被@Inherited注解，被@A注解的类A具有子类B，则子类B继承@A注解 |
| @Documented | 是否在JavaDoc文档中出现                                      |
| @Repeatable | JDK1.8 作用未知                                              |

```java
@Target({ElementType.TYPE})  
@Retention(RetentionPolicy.RUNTIME)  
@Documented  
@Component  
public @interface Controller {  
    @AliasFor(  
        annotation = Component.class  
  )  
    String value() default "";  
}
```

2. 注解通过定义方法的形式定义字段，字段数据类型只能为8个基本类型、String、Class、Enum、Annotation及其数组。

## 代理
---
1. 什么是代理
jdk代理
cgLib代理
3. demo例程



## I/O 序列化

IO框架

序列化

序列化、BIO、NIO、AIO

## JDBC

## 数据库连接池

**1. 基本原理：**在内部对象池中，维护一定数量的数据库连接，并对外暴露数据库连接的获取和返回方法。

如外部使用者可通过getConnection方法获取数据库连接，使用完毕后再通过releaseConnection方法将连接返回，注意此时的连接并没有关闭，而是由连接池管理器回收，并为下一次使用做好准备。

（1）建立数据库连接池对象（服务器启动）。

（2）按照事先指定的参数创建初始数量的数据库连接（即：空闲连接数）。

（3）对于一个数据库访问请求，直接从连接池中得到一个连接。如果数据库连接池对象中没有空闲的连接，且连接数没有达到最大（即：最大活跃连接数），创建一个新的数据库连接。

（4）存取数据库。

（5）关闭数据库，释放所有数据库连接（此时的关闭数据库连接，并非真正关闭，而是将其放入空闲队列中。如实际空闲连接数大于初始空闲连接数则释放连接）。

（6）释放数据库连接池对象（服务器停止、维护期间，释放数据库连接池对象，并释放所有连接）。

**2.作用**（性能 + 安全）

   **①资源重用**

​      由于数据库连接得到重用，避免了频繁创建、释放连接引起的大量性能开销。在减少系统消耗的基础上，增进了系统环境的平稳性（减少内存碎片以级数据库临时进程、线程的数量）

   **②更快的系统响应速度**

​      数据库连接池在初始化过程中，往往已经创建了若干数据库连接置于池内备用。此时连接池的初始化操作均已完成。对于业务请求处理而言，直接利用现有可用连接，避免了数据库连接初始化和释放过程的时间开销，从而缩减了系统整体响应时间。

   **③新的资源分配手段**

​      对于多应用共享同一数据库的系统而言，可在应用层通过数据库连接的配置，实现数据库连接技术。

   **④统一的连接管理，避免数据库连接泄露**

​     在较为完备的数据库连接池实现中，可根据预先的连接占用超时设定，强制收回被占用的连接，从而避免了常规数据库连接操作中可能出现的资源泄露

3. **常见连接池**

DBCP （Database Connection Pool）是一个依赖Jakarta commons-pool对象池机制的数据库连接池，Tomcat的数据源使用的就是DBCP

C3P0是一个开放源代码的JDBC连接池

HikariCP（spring boot 2.0 默认采用的数据库连接池）



# RunTime类

```java
        Runtime.getRuntime().addShutdownHook(new Thread("streams-shutdown-hook") {
            @Override
            public void run() {
                kafkaStreams.close();
                latch.countDown();
            }
        });
```

