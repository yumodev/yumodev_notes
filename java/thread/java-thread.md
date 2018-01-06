<h1 align="center" >线程基础-线程属性</h1>

## 获取当前线程

Thread类提供了静态方法`currentThread()`获取当前线程。

```
Thread.currentThread()//获取当前线程
```
 
## 获取线程id-getId()

通过`getId()`获取线程的id，该id可以唯一标识一个线程。 

线程的id是由Thread类自动生成的，不能动态的赋值,
下面是从Thread类中关于线程id的源码



```
public Thread() {
    this.init((ThreadGroup)null, (Runnable)null, "Thread-" + nextThreadNum(), 0L);
}
    
//新建线程的时候调用该方法    
private void init(ThreadGroup var1, Runnable var2, String var3, long var4, AccessControlContext var6) {
        ......
        //线程id是在线程定义的时候，自动生成的。
        this.tid = nextThreadID();
    }
}
 
 //将当前线程序列号加1，当做新的线程id
 private static synchronized long nextThreadID() {
    return ++threadSeqNumber;
}
   
 //返回当前的线程id   
 public long getId() {
    return this.tid;
}
```

## 获取和设置线程名字

线程的名字，可以通过 `setName() `和 `getName() `方法设置和获取线程的名字。
也可以在Thread的构造函数中设置其名字。如果程序没有主动给线程设置名字，那么
JVM也为线程设置一个默认的名字


```
 public final synchronized void setName(String var1) {
        this.checkAccess();
        if(var1 == null) {
            throw new NullPointerException("name cannot be null");
        } else {
            this.name = var1;
            if(this.threadStatus != 0) {
                this.setNativeName(var1);
            }

        }
    }

    public final String getName() {
        return this.name;
    }

```

### 获取线程状态

