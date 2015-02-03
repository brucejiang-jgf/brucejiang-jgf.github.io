---
layout: post
title: jvm gc roots 
category: jvm 
tags: [jvm-gc]
keywords: jvm , gc 
description: 
---


> The so-called GC (Garbage Collector) roots are objects special for garbage collector. Garbage collector collects those objects that are not GC roots and are not accessible by references from GC roots.

There are several kinds of GC roots. One object can belong to more than one kind of root. The root kinds are:

* Class - class loaded by system class loader. Such classes can never be unloaded. They can hold objects via static fields. Please note that classes loaded by custom class loaders are not roots, unless corresponding instances of java.lang.Class happen to be roots of other kind(s).
        - Class - 由系统类加载器(system class loader)加载的对象，这些类是不能够被回收的，他们可以以静态字段的方式保存持有其它对象。我们需要注意的一点就是，通过用户自定义的类加载器加载的类，除非相应的java.lang.Class实例以其它的某种（或多种）方式成为roots，否则它们并不是roots，.
* Thread - live thread
        - 活着的线程
* Stack Local - local variable or parameter of Java method
        - java方法的local变量或者参数
* JNI Local - local variable or parameter of JNI method
        -  java方法的local变量或者参数
* JNI Global - global JNI reference
        - 全局jni引用
* Monitor Used - objects used as a monitor for synchronization
        - 用于监控的同步对象
* Held by JVM - objects held from garbage collection by JVM for its purposes. Actually the list of such objects depends on JVM implementation. Possible known cases are: the system class loader, a few important exception classes which the JVM knows about, a few pre-allocated objects for exception handling, and custom class loaders when they are in the process of loading classes. Unfortunately, JVM provides absolutely no additional detail for such objects. Thus it is up to the analyst to decide to which case a certain "Held by JVM" belongs.
        - 用于JVM特殊目的由GC保留的对象，但实际上这个与JVM的实现是有关的。可能已知的一些类型是：系统类加载器、一些JVM知道的重要的异常类、一些用于处理异常的预分配对象以及一些自定义的类加载器等。然而，JVM并没有为这些对象提供其它的信息，因此就只有留给分析分员去确定哪些是属于"JVM持有"的了。 
* If an object is a root, it is specially marked in all views showing individual objects. For example, the following picture shows a fragment of paths view:
       - 以下是一张由Java Profiler的标示出哪些是GC roots的示例图：
       - ![](/public/img/gc_roots_in_explorer.png)




