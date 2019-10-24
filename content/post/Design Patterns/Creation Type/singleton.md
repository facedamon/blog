---
title: "[设计模式 | 创建模式 | 单例模式]"
date: 2019-10-15T15:10:56+08:00
#lastmod: 2019-08-30T01:37:56+08:00
toc: true
draft: false
tags: ["设计模式"]
categories: ["设计模式/创建模式"]
author: "java-my-life"
---

> 转载自https://www.cnblogs.com/java-my-life/

---------------------------------------------

- **单例类只能有一个实例**
- 单例类必须自己创建自己的唯一实例
- 单例类必须给所有其它对象提供这一实例

----------

- `恶汉式单例`：EagerSingleton.java.在这个类被加载时，静态变量instance会被初始化，此时类的私有构造器会被调用。这时候，单例类的唯一实例就被创建出来了。恶汉式其实是一种比较形象的称谓。既然饿，那么在创建对象实例的时候就比较着急。**典型的空间换时间**，当类装载的时候就会创建实例，不管你用不用，先创建出来，然后每次调用的时候就不需要判断节省运行时间。

- `懒汉式单例`：LazySingleton.java.对静态工厂方法使用了同步化，已处理多线程环境。**典型的时间还空间**，就是每次获取实例都会进行判断，看是否需要创建实例，浪费判断的时间。由于懒汉式的实现是线程安全的，这样会降低整个访问的速度，而且每次都要判断，那么有没有更好的实现方式呢？

- `双重检查加锁`：Singleton.java.即实现了线程安全就能够使性能不受很大的影响。那么什么是双重检查加锁呢？指的是：并不是每次进入getInstance方法都需要同步，而是先不同步，进入方法后，先检查实例是否存在，如果不存在才进行下面的同步块，这是第一重检查，进入同步块后，再次检查实例是否存在，如果不存在，就在同步的情况下创建一个实例，这是第二重检查。这样一来，就只需要同步一次从而减少了多次在同步情况下进行判断所浪费的时间。在实现时会用关键字`volatile`，它的意思是:被volatile修饰的变量的值，将不会被本地线程缓存，**所有对该变量的读写都是直接操作共享内存**，从而确保多个线程能正确的处理该变量。

- **双重检查加锁为什么要使用volatile?**
1. 如果不适用volatile关键词看起来似乎很完美，但这是一个错误的优化，在线程执行到第一次检查位置时读取到instance不为null，此时**instance引用的对象有可能还没有完成初始化**
2. 问题根源：在于第15行代码(instance = new Singleton();)创建一个对象可以分解为如下三个伪代码：
```
    2.1 memory = allocate();//分配对象的内存空间
    2.2 ctorInsatnce(memory);//初始化对象
    2.3 instance = memory;//设置instance指向刚分配的内存地址
```
3. 上面三行伪代码中的2和3之间，可能会被`指令重排序`。当声明对象的引用为volatile后，问题根源的三行伪代码中的2和3之间的重排序，在多线程环境中将会被禁止。

- **不禁止指令重排序Lazy initialization holder class模式：**
1. `类级内部类`
1.1 有static修饰的成员式内部类。如果没有static修饰的成员式内部类被称为对象级内部类。
1.2 类级内部类相当于其外部类的static成分，它的对象与外部对象不存在依赖关系，因此可直接创建，而对于对象级内部类的实例，是绑定在外部对象实例中的
2. `多线程缺省同步锁的知识`: 在多线程开发中，为了解决并发问题，主要是通过使用synchronized来加互斥锁进行同步控制。但是在某些情况中，JVM已经隐含地为您执行了同步，这些通过不不用自己进行同步控制:
2.1 由static初始化器(static field or static {}中的初始化器)初始化数据时
2.2 访问final field时
2.3 在创建线程之前创建对象时
2.4 线程可以看见它将要处理的对象时

- 在这个类级内部类里面去创建对象实例。这样一来，只要不使用这个类级内部类，那就不会创建对象实例，从而同时实现延迟加载和线程安全。触发时机：当getInstance()方法第一次被调用的时候，它第一次读取SIngletonHolder,instance，导致SingletonHolder类得到初始化；而这个类在装载并被初始化的时候会初始化它的静态域，从而创建Singleton的实例，由于是静态的域,因此只会在创建虚拟机装载类的时候初始化一次，并由虚拟机来保证它的线程安全。

- **`枚举`**：单元素的枚举类型已经成为实现Singleton的最佳方法。用枚举来实现单例非常简单，只需要编写一个包含单个元素的枚举类型即可。使用枚举来实现单实例控制会更加简洁，而且无偿地提供了序列化机制，并由JVM从根本上提供保障，绝对防止多次实例化，是更简洁、高效、安全的实现单例的方式

> 案例

- `EagerSingleton`
```
public class EagerSingleton{
    private static EagerSingleton instance = new EagerSingleton();
    
    private EagerSingleton(){}
    
    public static EagerSingleton getInstance(){
        return instance;
    }
```
- `LazySingleton`
```
public class LazySingleton{
    private static LazySingleton instance = null;
    private LazySingleton(){}
    
    public static sychronized LazySingelton getInstance(){
        if(instace == null){
            instance = new LazySingleton();
        }
        return instance;
    }
}
```
- `DoubleSync`
```
public class Singleton(){
    private volatile static Singleton instance = null;
    private Singleton(){}
    
    public static Singleton getInstance(){
        /**
        *先检查实例是否存在，如果不存在才进入下面的同步块
        */
        if(null == instance){
            synchronized (Singelton.class){
                /**
                *再次检查实例是否存在，如果不存在才真正的创建实例
                */
                if(null == instance){
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

- `InnerClass`
```
public class Singleton(){
    private Singleton(){}
    
    /**
    * 类级内部类，也就是静态的成员式内部类，该内部类的实例与外部类的实例没有绑定关系，而且只有被调用到时才会装载，从而实现延迟加载
    */
    private static class SingletonHolder{
        private static Singleton instance = new Singleton();//JVM来保证线程安全
    }
    
    public static Singleton getInstance(){
        return SingletonHolder.instance;
    }
}
```
- `Enum`
```
public enum Singleton{
    uniqueInstance;
    
    public void singletonOperation(){
        //TODO
    }
}
```