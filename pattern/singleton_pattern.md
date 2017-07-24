# 单例模式

### 单例模式的定义

确定某类只有一个实例，而且自行实例化并向整个系统提供这个实例。

### 单例模式的几种写法

#### 饿汉模式
  
   
```
public class Singleton{
        //自行生成一个类的实例。
   		private static final Singleton mInstance = new Singleton();

		//私有化构造方法。
   		private Singleton(){};

		//提供获取一个类的实例的接口
   		public static Singleton getInstance(){
   			return mInstance;
   		}
   }
```


####    懒汉模式
  
``` 
public class Singleton{
        //自行生成一个类的实例。
   		private static Singleton mInstance = null;

		//私有化构造方法。
   		private Singleton(){};

		//提供获取一个类的实例的接口
   		public synchronized static Singleton getInstance()
   		{
	   		if(mInstance == null)
	   		{
	   			mInstance = new Singleton();
	   		}
   			return mInstance;
   		}

   		public static void function(){

   		}
   }

```
### 单例模式的优缺点
   
#### 优点

   因为只生成一个实例所有可以减少的内存的开销，减少对资源的多重占用，可以在常驻内存中，实现全局的资源访问。

#### 缺点

   单例类一般没有接口，扩展起来比较困难

### 单例模式的使用场景

 1. 含有大量的静态方法的工具类。
 2. 如果一个类操作文件和数据库等消耗内存的资源可以考虑使用单例模式。
 3. 需要一个公共的访问资源的时候，比如计数器等等。 
      
### 单例模式的注意事项

#### 线程不安全 

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
   	 
#### Jvm的内存回收 。
   	     单例模式的一个类的实例咋长时间不使用的情况下有可能被jvm回收，然后重新生成一个新的实例，比如在计数器等应用情况的下会出现一些问题，需要特殊处理。

