---
title: Java复习笔记
author: 不二
mathjax: false
date: 2020-10-28 19:31:52
tags:
- Java
img:
categories: 程序设计
---

## JVM

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_%E7%BC%96%E8%AF%91%E6%B5%81%E7%A8%8B.png)

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_JVM.png)

- Class Loader：依据特定格式，加载class文件到内存。
- Execution Engine：对命令进行解析。
- Native Interface：融合不同开发语言的原生库为Java所用。
- Runtime Data Area：JVM内存空间结构模型。

### ClassLoader

ClassLoader在Java中具有非常重要的作用，它主要工作在Class装载的加载阶段，其主要作用是从系统外部获取Class二进制数据流。它是Java的核心组件，所有Class都由ClassLoader进行加载，ClassLoader负责通过将Class文件中的二进制数据流装载进系统，然后交给Java虚拟机进行连接、初始化等操作。

#### BootStrapClassLoader

C++编写，加载核心库java.*

#### ExtClassLoader

Java编写，加载扩展库javax.*

#### AppClassLoader

Java编写，加载程序所在目录

#### 自定义ClassLoader

Java编写，关键函数如下：

```java
protected Class<?> findClass(String name) throws ClassNotFoundException{ 
    throw new ClassNotFoundException(name); 
}
protected final Class<?> defineClass(byte[] b,int off,int len)throws ClassFormatError{
    return defineClass(null,b,off,len,null);
}
```

例子：

```java
//MyClassLoader.java
import java.io.ByteArrayOutputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;

public class MyClassLoader extends ClassLoader {
    private String path;
    private String classLoaderName;

    public MyClassLoader(String path, String classLoaderName) {
        this.path = path;
        this.classLoaderName = classLoaderName;
    }

    //用于寻找类文件
    @Override
    public Class findClass(String name) {
        byte[] b = loadClassData(name);
        return defineClass(name, b, 0, b.length);
    }

    //用于加载类文件
    private byte[] loadClassData(String name) {
        name = path + name + ".class";
        InputStream in = null;
        ByteArrayOutputStream out = null;
        try {
            in = new FileInputStream(new File(name));
            out = new ByteArrayOutputStream();
            int i = 0;
            while ((i = in.read()) != -1) {
                out.write(i);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                out.close();
                in.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return out.toByteArray();
    }
}

//ClassLoaderChecker.java
public class ClassLoaderChecker {
    public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException {
        MyClassLoader m = new MyClassLoader("/Users/baidu/Desktop/", "myClassLoader");
        Class c = m.loadClass("Wali");
        System.out.println(c.getClassLoader());
        System.out.println(c.getClassLoader().getParent());
        System.out.println(c.getClassLoader().getParent().getParent());
        System.out.println(c.getClassLoader().getParent().getParent().getParent());
        c.newInstance();
    }
}

```

#### 双亲委派机制

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_%E5%8F%8C%E4%BA%B2%E5%A7%94%E6%B4%BE.png)

采用双亲委派机制可以避免多份同样的字节码的加载。

### 类的加载方式

- 隐式加载：new
- 显式加载：loadClass，forName等

#### loadClass和forName的区别

类的加载过程

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_%E7%B1%BB%E7%9A%84%E8%A3%85%E8%BD%BD%E8%BF%87%E7%A8%8B.png)

- Class.forName的带的class是已经完成初始化的。如果项目中需要引入Mysql driver就需要使用forName以便执行其中的static块，完成初始化。
- Class.loadClass得到的class时候还没有链接的。利用这个特性spring可以实现延迟加载，加快加载速度。 

### JVM内存模型

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_JVM%E5%86%85%E5%AD%98%E6%A8%A1%E5%9E%8B.png)

- 线程私有：程序计数器、虚拟机栈、本地方法栈
- 线程共享：MetaSpace、Java堆

#### 程序计数器

- 当前线程所执行的字节码号指示器（逻辑，而非物理）
- 和线程是一对一的关系及“线程私有”
- 改变计数器的值来选取下一条需要执行的字节码指令
- 对Java方法计数，如果是native方法则计数器值为Undefined
- 不会发生内存泄漏

#### 虚拟机栈

- Java方法执行的内存模型
- 包含多个栈帧

##### 栈帧

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_%E8%99%9A%E6%8B%9F%E6%9C%BA%E6%A0%88%E5%B8%A7.png)

###### 局部变量表和操作数栈

局部变量表：包含方法执行过程中的所有变量

操作数栈：入栈、出栈、复制、交换、产生消费变量

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_add%E6%89%A7%E8%A1%8C.png)

递归会引发java.lang.StackOverflowError异常就是因为递归过深，栈帧数超过虚拟机栈深度。

#### 本地方法栈

与虚拟机栈相似，主要作用于标注了native的方法。

#### 元空间与永久代

元空间（MateSpace）与永久代（PermGen）是都是用来存储Class信息，都是方法区的实现，只是实现方法不同。**JDK1.8之后用元空间取代了永久代，元空间使用本地内存，而永久代使用的是jvm内存。**

MetaSpace相比PermGen的优势

- 字符串常量池存在永久代中，容易出现性能问题和内存溢出
- 类和方法的信息大小难以确定，给永久代的大小指定带来了困难
- 永久代会为GC带来不必要的复杂性
- 方便HotSpot与其他JVM如Jrockit的集成

#### Java堆

- 对象实例的分配区域
- GC管理的主要区域

#### 内存模型中堆和栈的区别

此处涉及到内存分配策略。

- 静态存储：编译时确定每个数据目标在运行时的存储空间需求。要求代码中不能有可变数据结构也不允许有嵌套和递归的出现。
- 栈式存储：数据区需求在编译时未知，运行时模块入口前确定。
- 堆式存储：编译时或运行时模块入口都无法确定，动态分配。

联系：引用对象、数组时，栈里定义变量保存堆中目标的首地址。如下图。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_%E5%BC%95%E7%94%A8%E5%A0%86%E5%AF%B9%E8%B1%A1.png)

区别：

- 管理方式：栈自动释放，编译器就能管理，堆需要GC。
- 空间大小：栈比堆小。
- 碎片相关：栈产生的碎片远小于堆。
- 分配方式：栈支持静态和动态分配，不需要考虑内存回收问题。而堆仅支持动态分配，即使有GC还是要考虑垃圾回收释放的问题。
- 效率：栈的效率比堆高。

### JVM三大调参优化参数

- -Xss：规定了每个线程虚拟机栈（堆栈）的大小，一般256k,影响并发线程数的大小
- -Xms：堆的初始值
- -Xmx：堆能达到的最大值

一般将Xms和Xmx设置成一样，因为当堆不够用而发生扩容时，会发生内存抖动，影响程序运行时的稳定性。

## 反射

在运行状态中，对于任意一个类。都能够知道这个类的所有属性和方法；对于任意一个对象。都能够调用它的任意方法和属性；这种动态获取信息以及调用对象方法的功能称为java的反射机制。

```java
//robot.java
public class Robot {
    private String name;
    public void sayHi(String helloSentence){
        System.out.println(helloSentence + " " + name);
    }
    private String throwHello(String tag){
        return "Hello " + tag;
    }
    static {
        System.out.println("Hello Robot");
    }
}

//ReflectSample.java
import java.lang.reflect.Field;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;

public class ReflectSample {
    public static void main(String[] args) throws ClassNotFoundException, IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchMethodException, NoSuchFieldException {
        Class rc = Class.forName("com.interview.javabasic.reflect.Robot");
        Robot r = (Robot) rc.newInstance();
        System.out.println("Class name is " + rc.getName());
        //获取方法时getDeclaredMethod()函数第二个参数是传参的类型，不能获得继承或接口的方法
        Method getHello = rc.getDeclaredMethod("throwHello", String.class);
        //允许访问权限
        getHello.setAccessible(true);
        Object str = getHello.invoke(r, "Bob");
        System.out.println("getHello result is " + str);
        //getMethod()只能获取public的方法或者是继承或接口的方法
        Method sayHi = rc.getMethod("sayHi", String.class);
        sayHi.invoke(r, "Welcome");
        //设定成员变量的值
        Field name = rc.getDeclaredField("name");
        //允许访问权限
        name.setAccessible(true);
        name.set(r, "Alice");
        sayHi.invoke(r, "Welcome");
        System.out.println(System.getProperty("java.ext.dirs"));
        System.out.println(System.getProperty("java.class.path"));

    }
}

```



## GC



## 多线程与并发



## 常见类库

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6.png)

### List和Set

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_List&Set.png)

### Map

#### HashMap

在Java8以前实现方式为数组+链表。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_HashMap.png)

 当大量数值的hash值相同时，则存储在链表中。链表查找是通过便利进行查找，所以性能恶化时，会从$O(1)$变成$O(n)$。

在Java8之后实现方式变为数组+链表+红黑树。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_HashMap_JDK8.png)

当链表超过8时，链表变化成红黑树。当链表小于6时，退化成链表。

put方法的逻辑：

1. 如果HashMao未被初始化过，则初始化
2. 对Key求Hash值，然后再计算下标
3. 如果没有碰撞，直接放入桶中
4. 如果碰撞，以链表的方式链接到后面
5. 如果链表长度超过阈值，就把链表转成红黑树
6. 如果链表长度低于6,就把红黑树转回成链表
7. 如果节点已经存在就替换旧值
8. 如果桶满了（容量16×加载因子0.75），就需要resize（扩容后重排）

如何有效的减少碰撞：

- 扰动函数：促使元素位置分布均匀，减少碰撞几率
- 使用final对象，并采用合适的equals和hashCode方法

获取hash到散列的过程：

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_HashMapGetHash.png)

扩容问题：

- 多线程环境下，调整大小会存在条件竞争，容易造成死锁
- rehashing是一个比较耗时的过程

HashMap变成线程安全需要用`Collections.synchronizedMap()`包裹。因为synchronizedMap里面有一个final属性的mutex进行控制。

#### HashTable

HashTable是线程安全的，所有方法都被`synchronized`修饰。因为是串行执行，所以效率较低。

可以通过锁细粒度化，将整把锁拆分成多个锁进行优化。

#### ConccurentHashMap

早期ConcurrentHashMap：通过分化锁Segment实现。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_%E6%97%A9%E6%9C%9FConcurrentHashMap.png)

当前的ConcurrentHashMap：CAS+synchronized使得锁更细化。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_ConcurrentHashMap.png)

## 异常

- What：异常类型回答了什么被抛出
- Where：异常堆栈跟踪回答了在哪被抛出
- Why：异常信息回答了为什么被抛出

**try-catch块影响JVM优化，并且异常对象实例需要保存栈快照等信息，开销较大。**

### Error和Exception

- Error：程序无法处理的系统错误，编译器不做检查
- Exception：程序可以处理的异常，捕获后可能恢复

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_%E5%BC%82%E5%B8%B8.png)

- RuntimeException：不可预知，程序应当自行避免
- 非RuntimeException：可预知，从编译器校验的异常

### 常见的Error和Exception

#### RuntimeException

- NullPointerException——空指针引用异常
- ClassCastException——类型强制转换异常
- IllegalArgumentException——传递非法参数异常
- IndexOutOfBoundsException——下标越界异常
- NumberFormatException——数字格式异常

#### 非RuntimeException

- ClassNotFoundException——找不到指定的class的异常
- IOException——IO操作异常

#### Error

- NoClassDefFoundError——找不到class定义异常
- StackOverflowError——深递归导致栈被耗尽而抛出的异常
- OutofMemoryError——内存溢出异常

### 异常处理机制

- 抛出异常：创建异常对象，交由运行时系统处理
- 捕获异常：寻找合适的异常处理器处理异常，否则终止执行



## Spring

### 依赖注入

把底层类作为参数传递给上层类，实现上层对下层的“控制”。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_Spring_DI.png)

### IOC

IOC支持的功能：

- 依赖注入
- 依赖检查
- 自动装配
- 支持集合
- 指定初始化方法和销毁方法
- 支持回调方法

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_Spring_IOC.png)

IOC容器的优势：

- 避免在各处使用new来创建类，并且可以做到统一维护。
- 创建实例的时候不需要了解其中的细节。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_Spring_IOC%E6%B5%81%E7%A8%8B.png)

#### IOC容器的核心接口

##### BeanFactory

Spring框架最核心的接口。

- 提供了IOC的配置机制。
- 包含Bean的各种定义，便于实例化Bean。
- 建立Bean之间的依赖关系。
- Bean生命周期的控制。

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_BeanFactory%E4%BD%93%E7%B3%BB.png)

##### ApplicationContext

ApplicationContext是BeanFactory的子接口之一。 

ApplicationContext的功能（继承多个接口）

- BeanFactory：能够管理、装配Bean。
- ResourcePatternResolver：能够加载资源文件。
- MessageSource：能够实现国际化等功能。
- ApplicationEventPublisher：能够注册监听器，实现监听机制。

##### 比较

- Beanfactory是Spring框架的基础设施，面向Spring。
- ApplicationContext面向使用Spring框架的开发者。

BeanDefinition：主要用来描述Bean定义。

BeanDefinitionRegistry：提供向IOC容器注册BeanDefinition对象的方法。

### Bean的作用域

- singleton：Spring的默认作用域，容器里拥有唯一的Bean实例。适合无状态Bean。
- prototype：针对每个getBean请求，容器都会创建一个Bean实例。适合有状态的Bean。
- request：会为每个HTTP请求创建一个Bean实例。
- session：会为每个session创建一个Bean实例。
- golbalSession：会为每个全局HTTP Session创建一个Bean,该最用于仅对Portlet有效。

### Bean的生命周期

###### 创建过程

![](https://cdn.jsdelivr.net/gh/weiyouwozuiku/buerlog_img/BlogImage/Java%E5%A4%8D%E4%B9%A0%E7%AC%94%E8%AE%B0_Bean%E5%88%9B%E5%BB%BA%E6%B5%81%E7%A8%8B.png)

###### 销毁过程

- 若实现了DisposableBean接口，则会调用destory方法。
- 若配置了destory-method属性，则会调用其配置的销毁方法。

### AOP

关注点分离：不同的问题交给不同的部分去解决。

- 面向切面编程AOP正是此种技术的实现
- 通用化功能代码的实现，对应的就是所谓的切面（Aspect）
- 业务功能代码和切面代码分开后，架构将变得高内聚低耦合
- 确保功能的完整性：切面最终需要被合并到业务中（Weave）

#### 三种织入方式

- 编译时织入：需要特殊的Java编译器，如AspectJ
- 类加载时织入：需要特殊的Java编译器，如AspectJ和AspectWerkz
- 运行时织入：Spring采用的方式，通过动态代理的方式，实现简单

#### 主要名词概念

- Aspect：通用功能的代码实现
- Target：被织入Aspect的对象
- Join point：可以作为切入点的机会，所有方法都可以作为切入点
- Pointcut：Aspect世纪被应用在的Join Point,支持正则
- Advice：类里的方法以及这个方法如何织入到目标方法的方式
- Waving：AOP的实现过程

#### Advice的种类

- 前置通知（Before）
- 后置通知（AfterReturning）
- 异常通知（AfterThrowing）
- 最终通知（After）
- 环绕通知（Around）

#### AOP的实现  

AOP的实现通过JdkProxy和Cglib。

- 由AopProxyFactory根据AdvisedSupport对象的配置来决定
- 默认策略如果目标类是接口，则用JDKProxy实现，否则后者
- JDKProxy的核心：InvocationHandler接口类和Proxy类
- Cglib：以继承的方式动态生成目标类的代理
- JDKProxy：通过Java的内部反射机制实现，反射机制在生成类的过程中比较高效
- Cglib：借助ASM实现，ASM是一种能够操作字节码的框架，ASM在生成类之后的执行过程中比较高效，可以使用缓存的方式，改善ASM生成类过程中缓慢的问题

### ACID

### 隔离级别

### 事务传播

## Tip 

### static、final、static final的区别

#### final

可以修饰属性、方法、类、局部变量。

初始化可以在编译期或者运行期，**初始化后不能改变。**

与具体对象有关，在运行期初始化的final属性，不同对象可以有不同值。

final类无法被继承，没有子类，final类中的方法默认是final的。

final方法不能被子类的方法覆盖，但是可以被继承。

**final成员变量表示常量，只能被赋值一次。其后不能改变。**

不能用于修饰构造方法。

private类型的方法默认是final类型的。

- 对于基本数据类型：是常量，创建后不能修改。
- 对于对象句柄：final将句柄变成常数（在声明时，必须将句柄初始化到一个具体的对象，而且不能再将句柄指向另一个对象。**但是对象本身是可以修改的**）
- 对于数组：数组属于对象，数组本身也可以改。同对象。
- 对于方法参数中的final句柄：在该方法内部不能改变参数句柄指向的实际东西，不能给形参句柄再赋值。

#### static

可以修饰属性、方法、代码段、内部类（静态或嵌套内部类）

初始化在编译期，初始化后能改变。

static与具体对象无关。

不可以修饰局部变量。

**被static修饰的成员变量和成员方法独立于该类的任何对象。**被所有类的实例共享。只要这个类被加载，Java虚拟机就能根据类名在运行时数据区的方法区内找到它们。可以在任意对象创建之前访问，无需引用任何对象。

#### final static

**static修饰的属性强调它们只有一个，final修饰的属性强调是一个常数**。

### 不同JDK版本之间的intern()方法的区别

```java
String s = new String("hello");
s.intern();
```

JDK6：当调用intern方法时，如果字符串常量池先前已创建出该字符串对象，则返回池中的该字符串的引用。否则，将此字符串对象添加到字符串常量池中，并且返回该字符串对象的引用。

JDK6+：当调用intern方式时，如果字符串常量池先前已创建出该字符串对象，则返回池中的该字符串的引用。否则，如果该字符串对象已经存在于Java堆中，则将堆中对此对象的引用添加到字符串常量池中，并且返回该引用。如果堆中不存在，则在池中创建该字符串并返回其引用。

### getBean方法的代码逻辑

1. 转换beanName
2. 从缓存中加载实例
3. 实例化Bean
4. 检测parentBeanFactory
5. 初始化依赖的Bean
6. 创建Bean 