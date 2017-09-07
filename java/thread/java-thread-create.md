<h1 align="center" > 线程基础 -线程的创建与执行 </h1>

## 如何创建并执行一个线程

Java中通过java.lang.Thread类来启动创建一个线程。
创建一个线程可以通过两种方法，但是最后执行都是调用Thread类的start()方法来启动一个线程。 start()会自动调用run() 执行线程的操作。
        
### 声明Thread实例创建线程
 
这是最基本的创建一个线程的方法，new一个Thread实例，然后调用start()方法来运行这个线程,
线程的具体执行的内容放到run()方法里面,一般使用内部类的方式来创建一个线程，也可以通过创建一个Thread类的子类来创建线程。


* 内部类的方式
	  
```  
/**
 * new Thread类的实例创建，然后调用start()，启动一个线程。
 */
new Thread(){
    @Override
    public void run() {
        System.out.println("当前线程名字为："+Thread.currentThread().getName());
    }
}.start();

```	

* 创建Thread的子类


```
/**
 * 定义Thread的子类来创建一个线程
 */
static class MyThread extends Thread {
    public MyThread() {
        setName("MyThread");
    }

    @Override
    public void run() {
        System.out.println("当前线程名字为："+Thread.currentThread().getName());
    }
}

//运行线程
(new MyThread()).start();

```	
		 
### 通过Runnable接口创建一个线程。
     
Thread类中有一个构造方法 `public Thread(Runnable var1)` ，我们可以通过这个构造方法，传入一个Runnable类的实例来创建一个线程。下面是Runnable类创建线程的一个内部类实现的方式。

```
 /**
 * 使用java.lang.Runnable来创建并启动一个线程
 */
new Thread(new Runnable(){
    @Override
    public void run() {
        System.out.println("当前线程名字为："+Thread.currentThread().getName());
    }
}).start();
```

下面我们看下Runnable接口的定义：

```
package java.lang;
public interface Runnable {
    void run();
}
```

Runnable接口只有个run()方法，线程的实现只能放到这个方法里面。我们知道调用Thread实例的start()开启线程后，会进入Thread实例的run()方法，那么线程又是如何和Runnable接口关联的呢，下面进入Thread类的源码查看究竟。

首先我们进入线程的构造方法的实现：

```
 public Thread(Runnable var1) {
    this.init((ThreadGroup)null, var1, "Thread-" + nextThreadNum(), 0L);
}
```

进入Thread类的init方法。Thread类的init方法有好几个重载方法，最终进入下面这个方法


```
private void init(ThreadGroup var1, Runnable var2, String var3, long var4, AccessControlContext var6) {
    if(var3 == null) {
        throw new NullPointerException("name cannot be null");
    } else {
        this.name = var3;
        ....
        this.target = var2;//Runnable的实例赋值了Thread实例的target变量中。
        this.setPriority(this.priority);
        if(var7.inheritableThreadLocals != null) {
            this.inheritableThreadLocals = ThreadLocal.createInheritedMap(var7.inheritableThreadLocals);
        }

        this.stackSize = var4;
        this.tid = nextThreadID();
        }
    }
```

通过上面的方法可以清楚的看到传入Thread的实例的Runnable实例被赋值target变量。那么target变量是什么时候调用的呢。现在进入Thread类的run方法一看究竟


```
public void run() {
    if(this.target != null) {
        this.target.run();
    }
}
```

至此我们看到一个清晰的线程运行流程。
1. Runnable实例保存到Thread实例的成员变量target中。
2. 调用Thread实例的start()方法启动实例。
3. 线程启动运行后，进入其run()方法。
4. 如果target不为空，就调用其run()方法


### 通过ThreadFactory来创建线程实例。

先看一下ThreadFactory接口的定义：

```
package java.util.concurrent;

public interface ThreadFactory {
    Thread newThread(Runnable var1);
}

```

ThreadFactory接口只有一个方法 `Thread newThread(Runnable var1);`.
所以通过实现ThreadFactory接口创建线程，本质上是和`new Thread(Runnable var1)`一样的。
但是使用`new Thread(Runnable var1)`创建线程，可复用差，而通过不同的ThreadFactory实现
可以创建不同的线程，是工厂方法模式的一个具体实现。

```
 /**
 * 通过ThreadFactory类创建一个实例。
 */
ThreadFactory factory = new ThreadFactory() {
    @Override
    public Thread newThread(Runnable runnable) {
        return new Thread(runnable);
    }
};

factory.newThread(new Runnable() {
    @Override
    public void run() {
        System.out.println("当前线程名字为："+Thread.currentThread().getName());
    }
}).start();
```

可以通过`Executors.defaultThreadFactory()`获取一个默认的线程工厂。


```
 /**
 * 通过Executors.defaultThreadFactory()获取一个ThreadFactory类，然后创建一个实例。
 */
Executors.defaultThreadFactory().newThread(new Runnable() {
    @Override
    public void run() {
        System.out.println("当前线程名字为："+Thread.currentThread().getName());
    }
}).start();
```


### 使用ExecutorService运行线程

还可以利用线程池创建一个线程，下面是一个实现例子。

```
ExecutorService executorService = Executors.newSingleThreadExecutor();
executorService.execute(new Runnable() {
    @Override
    public void run() {
        System.out.println("当前线程名字为："+Thread.currentThread().getName());

    }
});
executorService.shutdown();
```

## 参考

* 《图解多线程设计模式》
* 《Java编程思想》

