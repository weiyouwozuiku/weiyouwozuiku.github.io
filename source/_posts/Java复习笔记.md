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



## 常识

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