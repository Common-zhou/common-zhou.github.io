---
layout: post
title:  "VarHandle句柄"
categories: 多线程
tags: 多线程
excerpt: VarHandle句柄(java9引入)
auth: zhou
---
* content
{:toc}


# 1.什么是VarHandle

变量句柄（VarHandle）是对于一个变量的强类型引用，或者是一组参数化定义的变量族，包括了静态字段、非静态字段、数组元素等，VarHandle支持不同访问模型下对于变量的访问，包括简单的read/write访问，volatile read/write访问，以及CAS访问。

VarHandle相比于传统的对于变量的并发操作具有巨大的优势，在JDK9引入了VarHandle之后，JUC包中对于变量的访问基本上都使用VarHandle，比如AQS中的CLH队列中使用到的变量等。

# 2.VarHandle的作用与优势

在开始讨论VarHandle之前，我们先来回忆一下并发操作里面的三大特性：原子性、可见性、有序性。

volatile变量可以保证可见性、有序性（防止指令重拍），加锁或者原子操作、CAS等可以保证原子性，只有同时满足这三个特性才能够保证对于一个变量的并发操作是合乎预期的。

加锁的话需要对线程进行同步，而线程上下文切换之间带来的开销是很大的，所以这里不予考虑，考虑一下几种方式：

使用Atomic包下的原子类进行间接管理，但增加了开销，也可能导致额外的问题如ABA问题；
使用原子性的FieldUpdaters，利用反射机制，开销也会增大；
使用sun.misc.Unsafe提供的JVM内置函数，但直接操作JVM可能会损害安全性和可移植性；
针对以上的问题，VarHandle就是用来替代上述方式的一种方案，它提供了一系列标准的内存屏障操作，用于更细粒度的控制指令排序，在安全性、可用性、性能等方面都要优于现有的AIP，且基本上能够和任何类型的变量相关联。

# 3.如何获取VarHandle

```java

public class VarHandleTest {
    private String plainStr;    //普通变量
    private static String staticStr;    //静态变量
    private String reflectStr;    //通过反射生成句柄的变量
    private String[] arrayStr = new String[10];    //数组变量
 
    private static final VarHandle plainVar;    //普通变量句柄
    private static final VarHandle staticVar;    //静态变量句柄
    private static final VarHandle reflectVar;    //反射字段句柄
    private static final VarHandle arrayVar;    //数组句柄
 
    static {
        try {
            MethodHandles.Lookup lookup = MethodHandles.lookup();
            plainVar = lookup.findVarHandle(VarHandleTest.class, "plainStr", String.class);
            staticVar = lookup.findStaticVarHandle(VarHandleTest.class, "staticStr", String.class);
            reflectVar = lookup.unreflectVarHandle(VarHandleTest.class.getDeclaredField("reflectStr"));
            arrayVar = MethodHandles.arrayElementVarHandle(String[].class);
        } catch (ReflectiveOperationException e) {
            throw new ExceptionInInitializerError(e);
        }
    }
 
}
```

来分析一下上述创建VarHandle的代码：

1、通过MethodHandles类里面的lookup()函数创建一个Lookup类，这个Lookup类是MethodHandles的内部类，作用就是用于创建方法句柄和变量句柄的一个工厂类，源码如下：

```java
public static Lookup lookup() {
    return new Lookup(Reflection.getCallerClass());
}
```
2、通过Lookup里面的工厂方法生成不同类型的VarHandle，拿生成普通变量的findVarHandle方法来看：

```java
public VarHandle findVarHandle(Class<?> recv, String name, Class<?> type) 
        throws NoSuchFieldException, IllegalAccessException {
    MemberName getField = resolveOrFail(REF_getField, recv, name, type);
    MemberName putField = resolveOrFail(REF_putField, recv, name, type);
    return getFieldVarHandle(REF_getField, REF_putField, recv, getField, putField);
}
```
代码很简单，就是根据传入的参数来生成字段的访问对象MemberName，MemberName是用来描述一个方法或字段引用的数据结构，再根据MemberName生成句柄，熟悉JVM的同学看到REF_getField应该能够联想到JVM字节码，这个就是对应着访问字段的字节码。

对于findStaticVarHandle、unreflectVarHandle方法的实现其实也很类似，大致就是将REF_getField改为REF_getStatic的过程。

# 4.VarHandle的访问

一开始我们就讲过，VarHandle支持不同访问模型下对于变量的访问，包括简单的read/write访问，volatile read/write访问，以及CAS访问等，那么分别来看一下VarHandle是如何支持这些访问模型下对于变量的访问的。

1、简单的read/write访问

```java
plainVar.set(this, "I am a plain string");    //实例变量的普通write操作
staticVar.set("I am a static string");    //    静态变量的普通write操作
reflectVar.set(this, "I am a string created by reflection");    //反射字段的普通write操作
arrayVar.set(arrayStr, 0, "I am a string array element");    //数组变量的普通write操作
 
String plainString = (String) plainVar.get(this);    //实例变量的普通read操作
String staticString = (String) staticVar.get();    //    静态变量的普通read操作
String reflectString = (String) staticVar.get(this);    //反射字段的普通read操作
String arrayStrElem = (String) arrayVar.get(arrayStr, 0);    //数组变量的普通read操作， 第二个参数是指数组下标，即第0个元素
```
2、volatile read/write访问

对于不同类型的变量的访问方法跟上面其实大同小异，下面就以普通变量来举例：

```java
volatileVar.setVolatile(this, "I am volatile string");    //volatile write
String volatileString = (String) volatileVar.get(this);    //volatile read
```
3、CAS访问

    String casString = (String) casVar.get(this);    //先读取当前值作为cas的预期值
    casVar.compareAndSet(this, casString, "I am a new cas string");    //第二个参数为预期值，第三个参数为修改值

如果和预想的值不一样 不会修改

