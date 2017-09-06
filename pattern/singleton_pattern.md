# 单例模式

## 单例模式的定义

确定某类只有一个实例，而且自行实例化并向整个系统提供这个实例。

## 单例模式类图

![](./res/singlethon.png)

## 一个单例模式的例子 

```
public class Singleton {
    //自行生成一个类的实例。
    private static Singleton mInstance;

    //私有化构造方法。
    private Singleton(){}

    //提供获取一个类的实例的接口
    public static Singleton getInstance(){
        if (mInstance == null){
            mInstance = new Singleton();
        }
        return mInstance;
    }

    public static void main(String[] args){
        Singleton obj1 = Singleton.getInstance();
        Singleton obj2 = Singleton.getInstance();
        if (obj1 == obj2){
            System.out.println("obj1 和 obj2 对象相同");
        }else{
            System.out.println("obj1 和 obj2 对象不同");
        }
    }
}
```

在这个例子中定义了一个单例类Singleton，通过其静态公开方法getInstance()获取其实例。
然后在main方法中测试验证`Singleton.getInstance()`获取到的实例是否相同。

Singleton类有一个特点就是其构造函数是private，这就能够保证我们不能直接通过 `new Singleton()`生成Singleton类的实例。

## 单例模式的其他实现方式

### 懒汉模式

示例中Singleton类，如果是在单线程环境下是能够正常工作的，如果在高并发的环境下，有可能出现问题。在多个线程同时调用`Singleton.getInstance()`生成单例的时候，有可能对生成多个单例。
这种情况下，我们可以利用线程的同步机制来保证只能生成一个单例,因为类的实例是在第一次使用的时候才初始化，所以称为懒汉模式。

```
public class Singleton {
    //自行生成一个类的实例。
    private static Singleton mInstance;

    //私有化构造方法。
    private Singleton(){}

    //提供获取一个类的实例的接口
    public synchronized static Singleton getInstance(){
        if (mInstance == null){
            mInstance = new Singleton();
        }
        return mInstance;
    }
 }
 ```

懒汉模式能够保证在多线程环境下生成唯一的实例，第一次使用的时候才初始化，但是每次调用 `Singleton.getInstance()`都进行同步，造成了不必要的同步开销。

### DCL(Double Check Lock)



```
public class Singleton {
    //自行生成一个类的实例。
    private static Singleton mInstance;

    //私有化构造方法。
    private Singleton(){}

    //提供获取一个类的实例的接口
    public static Singleton getInstance(){
        if (mInstance == null){
            synchronized (Singleton.class){
                mInstance = new Singleton();
            }
        }
        return mInstance;
    }
}
```


## 单例模式的优缺点

### 优点

   因为只生成一个实例所有可以减少的内存的开销，减少对资源的多重占用，可以在常驻内存中，实现全局的资源访问。

### 缺点

   单例类一般没有接口，扩展起来比较困难

## 单例模式的使用场景

 1. 含有大量的静态方法的工具类。
 2. 如果一个类操作文件和数据库等消耗内存的资源可以考虑使用单例模式。
 3. 需要一个公共的访问资源的时候，比如计数器等等。

## 单例模式的注意事项

### 线程不安全

         在懒汉模式中，有可能
         public static Singleton getInstance()
   		{
	   		if(mInstance == null)
	   		{
	   			mInstance = new Singleton();
	   		}
   			return mInstance;
   		}

   		在高并发的程序中有可能出现多个实例。需要在getInstance方法前加上synchronized进行同步

### Jvm的内存回收 。
   	     单例模式的一个类的实例咋长时间不使用的情况下有可能被jvm回收，然后重新生成一个新的实例，比如在计数器等应用情况的下会出现一些问题，需要特殊处理。


