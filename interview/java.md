[TOC]
### 基础

#### 其他基础

* foreach和for循环的效率对比。
* Java1.8 的新特性。

#### 面向对象

* 类和对象的区别
* 多态的表现形式。
* Override和Overload的区别。

#### static

* 是否可以覆盖一个static方法。

#### final

* final 修饰类方法。

#### 内部类

* 普通内部类和匿名内部类的初始化的区别
* 匿名内部类有没有构造器函数。
* 匿名内部类引用局部变量的限制。
* 权限访问修饰符对内部类的修饰。
* 匿名内部类中为什么可以用new。
* 内部类为什么能够访问外部类的私有变量。


#### 异常

* finally, finalize的区别。

### 并发

#### 基础

* 进程和线程的区别
* Thread和Runnable的区别。
* 线程如何分配资源
* Volatile关键字如何保证原子性。
* Volatile和Atomic变量有什么不同。
* ThreadLocal的特点和使用。
* Callable和Runnable的区别。

#### 线程池

* Java 线程池原理。
* Java 自带的四大线程池。
* 如何获取线程池呢线程数量。

#### 同步

* 思索怎么产生的，怎么解决。
* Synchronized的原理，以及它是如何获得锁的。
* 自旋锁和Synchronized的区别。
* 锁的等级。

#### 应用

* 多线程断点续传下载如何实现续传。


### IO

#### IO基础

* IO包的设计模式-装饰器模式。

#### NIO

* NIO线程池。

### 设计模式

#### 设计模式

* 熟悉什么设计模式。

#### 单例模式

* 写一个双重锁的单例模式。
* 如何写出线程安全的单例模式。
* 单例模式的作用是在线程还是在进程。

#### 工厂模式

* factory的模式的优势。

#### 适配器模式

* Adapter是什么模式。

#### 观察者模式

* Observer模式是什么模式。

#### 其他模式

* 画出策略模式的UML图
* Decorator模式是对象级别还是类级别的。
* 画出代理模式的类图。


### 集合

#### 基础

* set、list、map、collection接口。

#### Array、ArrayList、LinkedList

* Array和ArrayList的区别
* ArrayList和LinkedList的区别，增删操作那个效率比较好。
* ArrayList和Vector的区别。
* ArrayList 是如何扩增内存的。

#### HashMap、HashTable

* HashMap和HashTable的区别
* hash值是怎么计算的。
* 怎么解决冲突和负载因子的问题。
* HashMap和HashTable的应用场景。
* HashMap的扩容机制。
* CocurrentHashMap的桶分割原理。
* HashMap是否是有序的？是否有有序的Map。
* TreeMap和LinkedHashMap如何保证顺序的。
* HashMap 能否用null作为键值。


### 类、classloader、反射、注解

#### class

* class的加载原理
* 一个类(相同包名)，JVM能编译出多个Class实例吗？
* 有哪些类加载器，他们的类名是什么。
* 每个类加载器都加载哪些类。
* 什么是双亲委派模型。
* instance的理解。
* Java类加载器的5个步骤。


###内存

#### GC

* GC 的原理
* GC root的判定
* GC策略都有什么分类。
* 各种垃圾收集器--CMS。
*  一个对象什么时候才会被GC。
*  Java对象如何展开自救。

#### 内存模型。

* 导致栈溢出的情况。
* 虚拟机内存调优。
* 堆和栈的区别。
* 内存回收为什么会造成卡顿。
* Java内存回收机制：新生代，老年代，gc机制。
* Java那个内存分区造成了内存溢出。

#### 引用

* 弱引用和软引用引用同一个对象，是否会被回收。
* 软引用和弱引用的区别。


### String

* String、StringBuffer、StringBuilder的区别


### 网络

* HttpClient、HttpUrlConnection的区别。

### 其他

* cache和buffer的区别
* Java状态机。
