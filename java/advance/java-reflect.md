<h1 align="center" >反射入门详解 </h1>

## Java反射机制概述

Java中的反射机制允许程序在JVM的运行期动态加载类，创建类对象，调用类的成员变量，执行类的成员方法。

通过Java的反射机制可以下面的功能

1. 在运行时判断任意一个对象所属的类；
2. 在运行时构造任意一个类的对象；
3. 在运行时判断任意一个类所具有的成员变量和方法；
4. 在运行时修改任意一个对象的成员变量；
5. 在运行时调用任意一个对象的方法；
6. Java的反射机制可以临时修改类、类的成员变量、类的方法的访问属性。

Java的反射机制主要用于框架开发方面，是一个高级工程师的必备技能。

## Java中的反射框架

类 | 功能
--- | --- 
java.lang.Class | 实例表示正在运行的 Java 应用程序中的类和接口
java.lang.reflect.Constructor |  提供关于类的单个构造方法的信息以及对它的访问权限
java.lang.reflect.Field | 提供有关类或接口的单个字段的信息，以及对它的动态访问权限
java.lang.reflect.Method | 提供关于类或接口上单独某个方法（以及如何访问该方法）的信息

## Class类的使用

Class类


使用 Java 反射机制可以在运行时期检查 Java 类的信息，检查 Java 类的信息往往是你在使用 Java 反射机制的时候所做的第一件事情，通过获取类的信息你可以获取以下相关的内容：

我定义一个类的时候需要使用Class关机字。比如 Class A{};我们定义了一个名字为A的类对象，那么我们就可以理解为Class 就是类对象A的类型。这个想法有点抽象。
Class类有两个重要的方法。
getName(); 获取这个对象的名字。
forName(); 这个是静态方法。通过一个类的名字，获取到这个类的类型说明，我也不知道该怎么描述，只要不要理解成一个类对象的实例就行。
newInstance(); 通过这个成员方法可以获得一个类的心的实例。
        
 	
### 通过反射操作一个类的字段。
    		
		这里我们先看一个Class的一个方法。
		Field getDeclaredField（String; //给定一个字段名称，获取到该字段的实例。
		Field类是表示一个Class中的一个字段。它主要有三个方法。
		getName(); 获取字段的名称。
		get(Object): 获取制定对象上该字段的值。
		set(Object obj, Object value); 将指定对象变量上此 Field 对象表示的字段设置为指定的新值。
		
###  通过反射操作一个类的方法

	我们可以通过反射机制在运行当中动态的获取一个实例方法，来操纵这个方法。需要用到相关类的方法如下
	Class类中的方法
	Method getMethod(String name, Class<?>... parameterTypes)   返回一个 Method 对象，它反映此 Class 对象所表示的类或接口的指定公共成员方法。
	Method 顾名思义表示的一个类型方法类型。它有两个常用的方法。
	getName(); 获取这个方法的名称。
	Object	invoke(Object obj, Object... args)  对带有指定参数的指定对象调用由此 Method 对象表示的底层方法。

## 参考

* [知乎-学习java应该如何理解反射](https://www.zhihu.com/question/24304289)
* [Java反射机制](http://wiki.jikexueyuan.com/project/java-reflection/)
* [Java学习之反射机制及应用场景](http://www.cnblogs.com/whoislcj/p/6038511.html)
* [玩转Java反射](http://blog.csdn.net/android_ku_ku/article/details/52336734)
* [Java 基础与提高干货系列—Java 反射机制 ](http://tengj.top/2016/04/28/javareflect/)
* [十分钟理解Java反射](http://www.jianshu.com/p/2efb2d1cbbc8)
* [在Android中使用反射到底有多慢？](https://mp.weixin.qq.com/s?__biz=MzA4NTQwNDcyMA==&mid=402508341&idx=1&sn=960682c2cafea9702d55a879c6246c5b#rd)
* [深入解析Java反射（1） - 基础](http://www.sczyh30.com/posts/Java/java-reflection-1/)
* [深入解析Java反射（2） - invoke方法](http://www.sczyh30.com/posts/Java/java-reflection-2/)


