---
title: Java复习笔记
author: 不二
mathjax: false
date: 2020-10-28 19:31:52
tags:
- Java
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/程序设计/java.png
categories: 程序设计
---

## JVM

![Java复习笔记_编译流程.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_编译流程.png)

![Java复习笔记_JVM.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_JVM.png)

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

![Java复习笔记_双亲委派.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_双亲委派.png)

采用双亲委派机制可以避免多份同样的字节码的加载。

### 类的加载方式

- 隐式加载：new
- 显式加载：loadClass，forName等

#### loadClass和forName的区别

类的加载过程

![Java复习笔记_类的装载过程.png ](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_类的装载过程.png)

- Class.forName的带的class是已经完成初始化的。如果项目中需要引入Mysql driver就需要使用forName以便执行其中的static块，完成初始化。
- Class.loadClass得到的class时候还没有链接的。利用这个特性spring可以实现延迟加载，加快加载速度。 

### JVM内存模型

![Java复习笔记_JVM内存模型.png ](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_JVM内存模型.png)

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

![Java复习笔记_虚拟机栈帧.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_虚拟机栈帧.png)

###### 局部变量表和操作数栈

局部变量表：包含方法执行过程中的所有变量

操作数栈：入栈、出栈、复制、交换、产生消费变量

![Java复习笔记_add执行.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_add执行.png)

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

![Java复习笔记_引用堆对象.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_引用堆对象.png)

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

### 进程与线程

进程：进程独占内存空间，保存各自运行状态，相互间不干扰且可以互相切换，为并发处理任务提供了可能。

线程：共享进程的内存资源，互相间切换更快捷，支持更细粒度的任务控制，使进程内的子任务的一并发执行。

进程是资源分配的最小单位，线程是CPU调度的最小单位。

所有与进程相关的资源，都被记录在PCB中。

![Java复习笔记_PCB.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_PCB.png)

进程是抢占处理机的调度单位。线程属于某个进程，共享其资源。

线程只由堆栈寄存器、程序计数器和TCB组成。

![Java复习笔记_TCB.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_TCB.png)

区别：

- 线程不能看作独立应用，而进程可看作独立应用
- 进程有独立的地址空间，相互不影响，线程只是进程的不同执行路径。当线程down掉，进程也会down掉
- 线程没有独立的地址空间，多进程的程序比多线程的程序健壮
- 进程的切换比线程的切换开销大

### Java中的进程与线程

- Java对OS提供的功能进行封装，包括线程与进程
- 运行一个程序会产生一个进程，进程包含至少一个线程
- 每个进程对应一个JVM实例，多个线程共享JVM里的堆，每一个线程都有自己私有的栈
- Java采用单线程编程模型，程序会自动创建主线程
- 主线程可以创建子线程，原则上要后于子线程完成执行

使用`Thread.currentThread()`获得当前的线程。

JVM不是只有一个线程 ，比如GC。

### 线程安全的主要原因

- 存在共享数据（临界资源）
- 存在多条线程同时操作这些共享数据

解决的根本方法：

同一时刻有且只有一个线程在操作共享数据，其他线程必须等待该线程处理完数据后再对共享数据进行操作。

### 互斥锁

特性：

- 互斥性：即在同一时间只允许一个线程持有某个对象锁，通过这种特性来实现多线程的协调机制。这样在同一时间只有一个线程对需要同步的代码块（复合操作）进行访问。互斥性即原子性。
- 可见性：必须确保在锁被释放之前，对共享变量所做的修改，对于随后获得该锁的另一个线程是可见的（即在获得锁时应获得最新共享变量的值），否则另一个线程可能是在本地缓存的某个副本上继续操作，从而引起不一致。

### synchronized

synchronzied锁的不是代码，而是对象。

根据获取锁的分类：获取对象锁和获取类锁。

#### 获取对象锁

两种方法：

1. 同步代码块（synchronized(this)，synchronized(类实例对象)），锁是小括号()中的对象
2. 同步非静态方法（synchronized method），锁是当前对象的实例对象

#### 获取类锁

两种方法：

1. 同步代码块（synchronized(类.class)），锁是小括号()中的类对象(Class对象)
2. 同步静态方法（synchronzied static method），锁是当前对象的类对象(Class对象)

#### 总结

- 线程访问对象的同步代码块时，另外的线程可以访问该对象的非同步代码块
- 若锁住的是同一个对象，一个线程在访问对象的同步代码块时，另一个访问对象的同步代码块的线程会被阻塞
- 若锁住的是同一个对象，一个线程在访问对象的同步方法时，另一个访问对象同步方法的线程会被阻塞
- 若锁住的是同一个对象，一个线程在访问对象的同步代码块时，另一个访问对象同步方法的线程会被阻塞，反之亦然
- 同一个类的不同对象的对象锁互不干扰
- 类锁由于也是一种特殊的对象锁，因此表现和上述1,2,3,4一致，而由于一个类只有一把对象锁，所以同一个类的不同对象使用类锁，将会是同步的
- 类锁和对象锁互不干扰

#### 原理

##### 重入

从互斥锁的设计上来说，当一个线程试图操作一个由其他线程持有的对象锁的临界资源时，将会进入阻塞状态。但当一个线程再次请求自己持有对象锁的临界资源的时候，这种情况就叫重入。

##### Java对象头

对象在内存中的布局：

- 对象头
- 实例数据
- 对齐填充

对象头的结构：

![Java复习笔记_对象头结构.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_对象头结构.png)

Mark Word结构：

![Java复习笔记_MarkWord.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_MarkWord.png)

##### Monitor

每个Java对象天生自带了一把看不见的锁。

![Java复习笔记_Montior锁.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_Montior锁.png)

#### synchronized的四种状态

- 无锁
- 偏向锁
- 轻量级锁
- 重量级锁

锁膨胀方向：无锁->偏向锁->轻量级锁->重量级锁

![Java复习笔记_锁汇总.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_锁汇总.png)

##### 偏向锁

减少同一线程获取锁的代价。

大多数情况下，锁不存在竞争，总是由同一线程多次获得。

不适用于锁竞争比较激烈的多线程场合。

核心思想：

如果一个线程呢个获得了锁，那么锁就进入偏向模式。此时Mark Word的结构也就变为偏向锁，当该线程再次请求锁，无需再做任何同步操作，即获得锁的过程只需检查Mark Word的锁标记位为偏向锁以及当前线程的ID等于ThreadID即可，这样就省去了大量有关锁申请的操作。

##### 轻量级锁

轻量级锁是由偏向锁升级来的，偏向锁运行一个线程进入同步块的情况下。当第二个线程加入锁争用的时候，偏向锁会升级成轻量级锁。

使用场景：线程交替执行同步代码块

若存在同一时间多个线程访问同一锁的情况，就会导致轻量级锁膨胀为重量级锁。

###### 加锁过程

1. 在代码进入同步代码块的时候，如果同步对象锁状态为无锁状态（锁标志位为“01”），虚拟机首先在当前线程的栈帧中创建一个名为锁记录(Lock Record)的空间，用于存储锁对象目前的Mark Word拷贝。官方称之为Displaced Mark Word。这时候线程堆栈与对象头的状态如图所示。

   ![Java复习笔记_初始化锁.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Java/程序设计/Java复习笔记/Java复习笔记_初始化锁.png)

2. 拷贝对象头中的Mark Word复制到锁记录中。

3. 拷贝完成后，虚拟机将使用CAS操作尝试将对象的Mark Word更新为指向Lock Record的指针，并将Lock Record里的owner指针指向object mark word。如果成功，则执行步骤4,否则执行步骤5。

4. 如果这个更新成功，那么这个线程就拥有了该对象的锁，并且该对象Mark Word的锁标志位设置为“00”，即表示此对象处于轻量级锁定状态，这时候线程堆栈与对象头的状态如图所示。

   ![Java复习笔记_轻量级锁.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/Java/程序设计/Java复习笔记/Java复习笔记_轻量级锁.png)

5. 如果这个更新失败，虚拟机首先会检查对象的Mark Word是否指向当前线程的栈帧。如果是就说明当前线程已经拥有这个对象的锁，那就可以直接进入同步块继续执行。否则说明多个线程竞争锁，轻量级锁膨胀为重量级锁，锁标志位的状态值设置为“10”，Mark Word中存储的就是指向重量级锁（互斥量）的指针，后面等待锁的线程也就要进入阻塞状态。而当前线程便尝试使用自旋来获取锁。

### 锁的内存语义

当线程释放锁时，Java内存模型会把该线程对应的本地内存中的共享变量刷新到主内存中。

当线程获取锁时，Java内存模型会把该线程对应的本地内存置为无效，从而使得被监视器保护的临界区代码必须从主内存中读取共享变量。



### Thread

#### run方法和start方法

调用run方法的时候会调用主线程执行方法，当调用start会调用非main的线程执行方法。

- 调用start方法会创建一个新的子线程并启动
- run方法只是Thread的一个普通的方法调用

![Java复习笔记_run方法.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_run方法.png)

#### Runnable和Thread

Runnable只是一个接口，里面有一个抽象的run方法。

- Thread是实现了Runnable接口的类，使得run支持多线程
- 因类的单一继承原则，推荐多使用Runnable接口

#### 变化

早期版本中，synchronzied属于重量级锁，依赖于Mutex Lock实现。线程之间的切换需要从用户态转换成核心态，开销较大。

JDK6之后，性能获得提升，引入Adaptive Spinning、Lock Eliminate、Lock Coarsening、Lightweight Locking、Biased Locking...

### 自旋锁

- 许多情况下，共享数据的锁定状态持续时间较短，切换进程不值得
- 通过让线程执行忙循环等待锁的释放，不让出CPU
- 缺点：若锁被其他线程长时间占用，会带来许多性能上的开销

可以使用PerBlockSpin进行更改。

### 自适应自旋锁

- 自旋的次数不再固定
- 由前一次在同一个锁上的自旋时间及锁的拥有这的状态来决定

### 锁消除

JIT编译时，对运行上下文进行扫描，去除不可能存在竞争的锁。

### 锁粗化

为了帮助一连串琐碎的加锁去锁的过程，在外部加锁。

### run方法传参

- 构造函数传参
- 成员变量传参
- 回调函数传参

### 处理线程的返回值

- 主线程等待法，需要自己实现等待的方法。当变量多的时候，会导致代码繁琐，无法做到精准的控制
- 使用Thread类的join方法阻塞当前进程以等待子线程处理完毕。但是粒度粗。
- 通过Callable接口实现：通过FutureTask Or 线程池获取

#### Callable接口

该接口中只有一个抽象方法：call()。

```java
//MyCallable.java
import java.util.concurrent.Callable;

public class MyCallable implements Callable<String> {
    @Override
    public String call() throws Exception{
        String value="test";
        System.out.println("Ready to work");
        Thread.currentThread().sleep(5000);
        System.out.println("task done");
        return  value;
    }
}

//FutureTaskDemo.java
import java.util.concurrent.ExecutionException;
import java.util.concurrent.FutureTask;

public class FutureTaskDemo {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        FutureTask<String> task = new FutureTask<String>(new MyCallable());
        new Thread(task).start();
        if(!task.isDone()){
            System.out.println("task has not finished, please wait!");
        }
        System.out.println("task return: " + task.get());

    }
}

```

FutureTask的isDone方法表示线程执行完成，get方法获取返回值，get方法能设定最长等待时间，直接传参即可。

#### 线程池

```java
//MyCallable.java
import java.util.concurrent.Callable;

public class MyCallable implements Callable<String> {
    @Override
    public String call() throws Exception{
        String value="test";
        System.out.println("Ready to work");
        Thread.currentThread().sleep(5000);
        System.out.println("task done");
        return  value;
    }
}
//ThreadPoolDemo.java
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

public class ThreadPoolDemo {
    public static void main(String[] args) {
        ExecutorService newCachedThreadPool = Executors.newCachedThreadPool();
        Future<String> future = newCachedThreadPool.submit(new MyCallable());
        if(!future.isDone()){
            System.out.println("task has not finished, please wait!");
        }
        try {
            System.out.println(future.get());
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        } finally {
            newCachedThreadPool.shutdown();
        }
    }
}

```

通过`Executors.newCachedThreadPool`创建`ExecutorService`对象。通过对象的submit方法包裹继承Callable接口的自定义类，注意这里的返回值使用Future去接收。最后记得关闭线程池，使用ExecutorService的shutdown方法。

### 线程的状态

六个状态：

- 新建（New）：创建后尚未启动的状态
- 运行（Runnable）：包含Running和Ready
- 无限期等待（Waiting）：不会被分配CPU执行时间，需要显式被唤醒。

  - 没有设置Timeout参数的Object.wait()方法
  - 没有设置Timeout参数的Thread.join()方法
  - LockSupport.park()方法
- 限期等待（Timed Waiting）：在一定时间后由系统自动唤醒

  - Thread.sleep()方法
  - 设置Timeout参数的Object.wait()方法
  - 设置Timeout参数的Thread.join()方法
  - LockSupport.parkNanos()方法
  - LockSupport.parkUtil()方法
- 阻塞（Blocked）：等待获取排它锁
- 结束（Terminated）：已终止线程的状态，线程已经结束执行

![Java复习笔记_线程状态图.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_线程状态图.png)

### sleep和wait

- sleep()是Thread类的方法，wait是Object类中定义的方法
- sleep()方法可以在任何地方使用
- wait()方法只能在synchronized方法或synchronized块中使用
- **Thread.sleep()只会让出CPU,不会导致锁行为的改变**
- **Object.wait()不仅让出CPU,还会释放已经占用的同步资源锁**
- Object.wait()方法能被notify和notifyAll唤醒

### notify和notifyAll

#### 锁池EntryList

假设线程A已经拥有了某个对象（不是类）的锁，而其他线程B、C想要调用这个对象的某个synchronized方法（或者块），由于B、C线程在进入对象的synchronized方法（或者块）之前必须获得该对象锁的拥有权，而恰好该对象的锁目前正被线程A所占用。此时B、C线程就会被阻塞，进入一个地方等待锁的释放，这个地方就是该对象的锁池。

#### 等待池WaitSet

假设线程A调用了某个对象的wait()方法，线程A就会释放该对象的锁。同时线程A就进入到该对象的等待池中，不会去竞争该对象的锁。

#### 区别

- notifyAll会让所有处于等待池的线程全部进入锁池去竞争获取锁的机会
- notify只会随即选取一个处于等待池中的线程进入锁池去争取竞争获取锁的机会

### yield

当调用Thread.yield()函数时，会给线程调度器一个当前线程愿意让出CPU使用的暗示，但是线程调度器可能会忽略这个暗示。**但是不会释放锁资源。**

### 中断线程

已经被抛弃的方法：

- 通过调用stop()停止线程
- 通过调用suspend()方法和resume()方法

目前使用的方法：

调用interrupt()方法，通知线程应该中断了。

- 如果线程处于被阻塞的状态，那么线程将立即推出被阻塞的状态，并抛出一个InterruptedException异常
- 如果线程处于正常活动状态，那么会将该线程的中断标志设置为true.被设置中断标志的线程将继续正常运行，不受影响

## 常见类库

![Java复习笔记_集合框架.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_集合框架.png)

### List和Set

![Java复习笔记_List&Set.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_List&Set.png)

### Map

#### HashMap

**在HashMap中，null可以作为键，这样的键只有一个；可以有一个或多个键所对应的值为null。**

当get()方法返回null值时，即可以表示 HashMap中没有该键，也可以表示该键所对应的值为null。因此，在HashMap中不能由get()方法来判断HashMap中是否存在某个键， 而应该用containsKey()方法来判断。

在Java8以前实现方式为数组+链表。

![Java复习笔记_HashMap.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_HashMap.png)

 当大量数值的hash值相同时，则存储在链表中。链表查找是通过便利进行查找，所以性能恶化时，会从$O(1)$变成$O(n)$。

在Java8之后实现方式变为数组+链表+红黑树。

![Java复习笔记_HashMap_JDK8](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_HashMap_JDK8.png)

当链表超过8时，链表变化成红黑树。当链表小于6时，退化成链表。

put方法的逻辑：

1. 如果HashMap未被初始化过，则初始化
2. 对Key求Hash值，然后再计算下标
3. 如果没有碰撞，直接放入桶中
4. 如果碰撞，以链表的方式链接到后面
5. 如果链表长度超过阈值，就把链表转成红黑树
6. 如果链表长度低于6,就把红黑树转回成链表
7. 如果节点已经存在就替换旧值
8. 如果桶满了（容量16×加载因子0.75），就需要resize（扩容后重排）

默认的负载因子大小为0.75，也就是说，当一个map填满了75%的bucket时候，和其它集合类(如ArrayList等)一样，将会创建原来HashMap大小的两倍的bucket数组，来重新调整map的大小，并将原来的对象放入新的bucket数组中。这个过程叫作rehashing，因为它调用hash方法找到新的bucket位置。

如何有效的减少碰撞：

- 扰动函数：促使元素位置分布均匀，减少碰撞几率
- 使用final对象，并采用合适的equals和hashCode方法

重新调整HashMap大小存在的问题：

当重新调整HashMap大小的时候，确实存在条件竞争，因为如果两个线程都发现HashMap需要重新调整大小了，它们会同时试着调整大小。在调整大小的过程中，存储在链表中的元素的次序会反过来，因为移动到新的bucket位置的时候，HashMap并不会将元素放在链表的尾部，而是放在头部，这是为了避免尾部遍历(tail traversing)。如果条件竞争发生了，那么就死循环了。

获取hash到散列的过程：

![Java复习笔记_HashMapGetHash.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_HashMapGetHash.png)

HashMap 底层数组的长度总是 2 的 n 次方。

当 length 总是 2 的倍数时，h & (length-1) 将是一个非常巧妙的设计：假设 h=5,length=16, 那么 h & length - 1 将得到 5；如果 h=6,length=16, 那么 h & length - 1 将得到 6 ……如果 h=15,length=16, 那么 h & length - 1 将得到 15；但是当 h=16 时 , length=16 时，那么 h & length - 1 将得到 0 了；当 h=17 时 , length=16 时，那么 h & length - 1 将得到 1 了……这样保证计算得到的索引值总是位于 table 数组的索引之内。

**增大负载因子可以减少 Hash 表（就是那个 Entry 数组）所占用的内存空间，但会增加查询数据的时间开销，而查询是最频繁的的操作（HashMap 的 get() 与 put() 方法都要用到查询）；减小负载因子会提高数据查询的性能，但会增加 Hash 表所占用的内存空间。**

扩容问题：

- 多线程环境下，调整大小会存在条件竞争，容易造成死锁
- rehashing是一个比较耗时的过程

HashMap变成线程安全需要用`Collections.synchronizedMap()`包裹。因为synchronizedMap里面有一个final属性的mutex进行控制。

#### HashTable

HashTable是线程安全的，所有方法都被`synchronized`修饰。因为是串行执行，所以效率较低。

可以通过锁细粒度化，将整把锁拆分成多个锁进行优化。

**Hashtable中，key和value都不允许出现null值。**

#### ConccurentHashMap

早期ConcurrentHashMap：通过分化锁Segment实现。

![Java复习笔记_早期ConcurrentHashMap.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_早期ConcurrentHashMap.png)

当前的ConcurrentHashMap：CAS+synchronized使得锁更细化。

![Java复习笔记_ConcurrentHashMap.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_ConcurrentHashMap.png)

### HashSet

HashSet 本身就采用 HashMap 来实现的。但是HashMap是Map接口的常用实现类，HashSet是Set接口的常用实现类。接口规范不同，但是底层的Hash存储机制完全一样。

HashSet的实现其实非常简单，它只是封装了一个HashMap对象来存储所有的集合元素。所有放入HashSet中的集合元素实际上由HashMap的key来保存，而HashMap的value则存储了一个PRESENT，它是一个静态的Object对象。

### String、StringBuffer、StringBuilder

String是字符串常量，而StringBuffer和StringBuilder是字符串变量。由String创建的字符内容是不可改变的，而由StringBuffer和StringBuilder创建的字符内容是可以改变的。

StringBuffer是线程安全的，而StringBuilder是非线程安全的。StringBuilder是从JDK 5开始，为StringBuffer类补充的一个单线程的等价类。**我们在单线程使用时应优先考虑使用StringBuilder**，因为它支持StringBuffer的所有操作，但是因为它不执行同步，不会有线程安全带来额外的系统消耗，所以速度更快。

虽然String、StringBuffer和StringBuilder都是final类，它们生成的对象都是不可变的，而且它们内部也都是靠char数组实现的，但是不同之处在于，String类中定义的char数组是final的，而StringBuffer和StringBuilder都是继承自AbstractStringBuilder类，它们的内部实现都是靠这个父类完成的，而这个父类中定义的char数组只是一个普通是私有变量，可以用append追加。因为AbstractStringBuilder实现了Appendable接口。




## 异常

- What：异常类型回答了什么被抛出
- Where：异常堆栈跟踪回答了在哪被抛出
- Why：异常信息回答了为什么被抛出

**try-catch块影响JVM优化，并且异常对象实例需要保存栈快照等信息，开销较大。**

### Error和Exception

- Error：程序无法处理的系统错误，编译器不做检查
- Exception：程序可以处理的异常，捕获后可能恢复

![Java复习笔记_异常.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_异常.png)

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

![Java复习笔记_Spring_DI.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_Spring_DI.png)

### IOC

IOC支持的功能：

- 依赖注入
- 依赖检查
- 自动装配
- 支持集合
- 指定初始化方法和销毁方法
- 支持回调方法

![Java复习笔记_Spring_IOC.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_Spring_IOC.png)

IOC容器的优势：

- 避免在各处使用new来创建类，并且可以做到统一维护。
- 创建实例的时候不需要了解其中的细节。

![Java复习笔记_Spring_IOC流程.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_Spring_IOC流程.png)

#### IOC容器的核心接口

##### BeanFactory

Spring框架最核心的接口。

- 提供了IOC的配置机制。
- 包含Bean的各种定义，便于实例化Bean。
- 建立Bean之间的依赖关系。
- Bean生命周期的控制。

![Java复习笔记_BeanFactory体系.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_BeanFactory体系.png)

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

![Java复习笔记_Bean创建流程.png](https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/程序设计/Java/Java复习笔记/Java复习笔记_Bean创建流程.png)

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