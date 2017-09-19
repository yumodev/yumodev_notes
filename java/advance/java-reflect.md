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

## 创建一个测试类

为了演示反射的使用，我创建一个名字为Book的类，表示一本图书，有两个成员变量保存书的名字和价格，可以通过构造方法、静态工厂方法创建类的实例，同时又一个静态变量表示创建类实例的数量。


```java
package com.yumodev.java.entry;

/**
 * Created by yumodev
 * Book类,目前用于反射测试
 */
public class Book {
    /**
     * 书的名字
     */
    public String name = "";
    /**
     * 书的价格
     */
    private double price = 0.0;
    /**
     * 创建书的数量，静态变量
     */
    private static int count;

    /**
     * 默认构造方法
     */
    public Book(){
        count++;
    }

    /**
     * 构造方法
     * @param name
     * @param price
     */
    public Book(String name, double price) {
        this.name = name;
        this.price = price;
        count++;
    }

    /**
     * 通过静态工厂的创建Book实例
     * @param name
     * @param price
     * @return
     */
    public static Book newBook(String name, double price){
        Book book = new Book();
        book.name = name;
        book.price = price;
        count++;
        return book;
    }

    @Override
    public String toString() {
        return super.toString()+" "+name+" 价格："+price;
    }

    /**
     * 获取价格
     * @return
     */
    public double getPrice() {
        return price;
    }

    /**
     * 设置新的价格
     * @param price
     */
    public void setPrice(double price) {
        this.price = price;
    }

    /**
     * 获取图书的数量。
     * @return
     */
    public static int getCount(){
        return count;
    }
}

```

## Class类的使用

### 获取类对象

* 通过类的名字直接调用


```java
Class cls = Book.class
``` 

* 通过类的实例创建类的对象


```Java
Book book = new Book();
Class cls = book.getClass();
```

* 通过类的静态方法forName()创建类对象


```Java
Class.forName("com.yumodev.java.entry.Book")
```
        

### 获取类对象基础属性

通过`Class cls = Book.class`获取一个Class对象，通过这个Class对象来熟悉其一些属性的获取

* 获取类的名称包括包名：

```
cls.getName();//输出：com.yumodev.java.entry.Book
```

* 获取类的包名：

```
cls.getPackage();//输出：com.yumodev.java.entry
```

* 获取类的名字，不包含包名

```
cls.getSimpleName();//输出：Book
```

* 获取类的父类

```
cls.getSuperclass();//输出：java.lang.Object
```

### 类的签名

类的签名的获取有两种方式，一种利用Class类的方法，这种方法比较简单，只能用于获取类对象的签名，还有一种是使用反射框架下`java.lang.reflect.Modifier`类进行获取签名，它不仅可以获取类的签名，还可以获取成员变量、方法、构造方法的签名。Modifier类的主要方法如下：

方法名 | 含义
--- | --- 
static boolean isAbstract(int mod) | 是否为抽象类或者方法
static boolean isFinal(int mod) | 是否为Final类型的类、方法、成员变量
static boolean isInterface(int mod) | 判断类是否为接口
static boolean isNative(int mod) | 判断方法是否为本地方法
static boolean isPrivate(int mod) | 判断类、方法、成员变量是否为私有访问权限
static boolean isProtected(int mod) |  判断类、方法、成员变量是否为保护访问权限
static boolean isPublic(int mod) |  判断类、方法、成员变量是否为公开方法
static boolean isStatic(int mod) | 判断类、方法、成员变量是否为静态的。
static boolean isSynchronized(int mod) | 判断方法是否为同步方法。
static boolean isTransient(int mod) | 判断成员变量是否有transient声明
static boolean isVolatile(int mod) | 判断成员变量是否有volatile声明



这是一个获取类对象签名的演示：

```java
int modifiers = cls.getModifiers();
Modifier.isAbstract(modifiers);//是否为抽象对象

```

## 类构造方法

### 遍历类的构造方法

通过Class类的`getConstructors()`方法可以获取其所有的构造方法

```
Class cls = Book.class;
Constructor[] constructors = cls.getConstructors();
for (Constructor constructor : constructors){
   System.out.println(constructor.toString());        
}
```

### 获取类对象的构造方法

通过Class类的 `Constructor<T> getConstructor(Class... var1)`来获取指定参数的构造方法。其参数为可变参数, 当要获取构造方法不存在的时候，会抛出`NoSuchMethodException `异常

下面的例子是获取Book类的`public Book(String name, double price) `构造方法.


```Java
Class cls = Book.class;
try {
    Constructor constructor = cls.getConstructor(String.class, double.class);
} catch (NoSuchMethodException e) {
    e.printStackTrace();
} 
```

### 通过构造方法生成类的实例

通过`java.lang.reflect.Constructor`类的`public T newInstance(Object... var1)`方法可以创建一个类的实例。


``` Java
Class cls = Book.class;
try {
    Constructor constructor = cls.getConstructor(String.class, double.class);
    Book book = (Book) constructor.newInstance("《Java入门详解》", 990f);
    System.out.println("通过构造方法创建实例成功了:"+book.toString());
} catch (NoSuchMethodException e) {
    e.printStackTrace();
} catch (IllegalAccessException e) {
    e.printStackTrace();
} catch (InstantiationException e) {
    e.printStackTrace();
} catch (InvocationTargetException e) {
    e.printStackTrace();
}
```

## 类的方法

### 遍历类的方法

通过Class类的`getMethods()`方法可以获取其所有的方法

```java
Class cls = Book.class;
Methond[] methods = cls.getMethods();
for (Method method : methods){
   System.out.println(method.toString());        
}
```

### 查询类的方法

java.lang.reflect.Method类表示类的一个方法。
通过Class中的`public Method getMethod(String var1, Class... var2)`获取类中的一个方法。其中参数`var1`表示方法的名字, var2表示方法的参数列表，如果没有参数可以设置为null。用法如下：


```
Class cls = Book.class;
cls.getMethod("setPrice", double.class);//获取void setPrice(double)方法
cls.getMethod("getPrice", null);//获取double getPrice()方法。参数传入为null编译有警告，可以采用下面的方式实现
cls.getMethod("getPrice", new Class[0]);
```

### 执行类的方法

通过Method类的`public Object invoke(Object var1, Object... var2) `方法，可以执行一个类的方法。invoke()的方法依次传入类的实例、方法的参数列表的值。下面通过反射调用Book类的setPrice()修改图书的价格为例演示，如何反射获取方法并执行。


```
Book book = Book.newBook("《Book》", 10f);
Class cls = book.getClass();
try {
    Method method = cls.getMethod("setPrice", double.class);
    method.invoke(book, 20f);
    System.out.println(book.getPrice());
} catch (NoSuchMethodException e) {
    e.printStackTrace();
} catch (InvocationTargetException e) {
    e.printStackTrace();
} catch (IllegalAccessException e) {
    e.printStackTrace();
}
```


### 执行静态方法

上面的例子演示如何反射执行类的一个普通的方法，如果是静态方法该如何执行呢。在回头看下Method类的invoke()方法的声明`public Object invoke(Object var1, Object... var2)`,invoke()方法的第一次参数是传入类的实例的，如果传入null，就表示调用的该类对象的一个静态方法。下面的例子演示了反射调用Book类的newBook()方法生成一个实例。


```
Class cls = Book.class;
try {
    Method method = cls.getMethod("newBook", String.class, double.class);
    Book book = (Book) method.invoke(null, "《Book》", 20f);
    System.out.println(book.toString());
} catch (NoSuchMethodException e) {
    e.printStackTrace();
} catch (InvocationTargetException e) {
    e.printStackTrace();
} catch (IllegalAccessException e) {
    e.printStackTrace();
}
```
	
## 类的成员变量

### 遍历类的成员变量

通过Class类的`getDeclaredFields()`方法可以获取其所有的字段，不包括其父类的成员变量。

```java
Class cls = Book.class;
Field[] field = cls.getDeclaredFields();
for (Field method : fields){
   System.out.println(field.toString());        
}
```

### 获取一个类的成员变量

Class类提供了两个方法用于获取类的成员变量。
1. getField(): 获取类的public类型的成员变量
2. getDeclaredField():可以获取类的所有成员变量


```java
Class cls = Book.class;
Field name = cls.getField("name");
Field price = cls.getDeclaredField("price");
Field count = cls.getDeclaredField("count");
```

### 获取和设置类的成员变量的值

Field类表示一个成员变量，其封装很多方法，用于获取和设置成员变量的值。其主要方法如下：
1. `public void set(Object var1, Object var2)` 
2. `public Object get(Object var1)`


```java
Book book = Book.newBook("《Book》", 10f);
Class cls = book.getClass();
try {
    Field name = cls.getField("name");
    name.set(book, "new book");
    System.out.println(book.getPrice());
} catch (NoSuchFieldException e) {
    e.printStackTrace();
} catch (IllegalAccessException e) {
    e.printStackTrace();
}
```

### 修改字段的访问权限

在本文中的Book类中有一个静态私有的`count`成员变量，如何通过反射修改其值呢。
首先因为是私有变量，所有只能通过Class类的getDeclaredField()方法获取该成员变量。
其次访问和修改私有变量前，需要设置该字段的访问权限。

Field、Method、Constructor类都继承自AccessibleObject。在AccessibleObject类中
有一个成员变量来设置类的成员变量、构造方法、方法的访问属性。

下面的例子演示的如果操作Book的静态私有`count`成员变量


```java
Book book = Book.newBook("《Book》", 10f);
Class cls = book.getClass();
try {
    Field count = cls.getDeclaredField("count");
    //修改器访问属性
    count.setAccessible(true);
    count.set(null, 2);
} catch (NoSuchFieldException e) {
    e.printStackTrace();
} catch (IllegalAccessException e) {
    e.printStackTrace();
}
```

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


