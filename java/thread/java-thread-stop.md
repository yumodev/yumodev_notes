<h1 align="center" >线程基础-如何优雅的终止线程</h1>

## 为何要终止线程的运行
      
线程的实现是在Thread.run()方法里面运行的，当线程Thread.run()运行结束的时候，线程就终止了。但是如果线程正在运行当中，程序的上下文发生了改变，比如切换了用户等等，这个时候我们就有了需要主动去终止一个线程的运行的需求。

## 一个不停运行的线程

定义一个StopThread线程类，通过readFromServer()方法从服务器上不停的取数据，然后通过 saveContent()将数据保存到本地，为了演示方便并没有真正的实现两个方法
而是在readFromServer()方法中每隔一秒生成一个字符串，然后通过saveContent()方法打印出来。

```
public class ThreadStop {
    static class StopThread extends Thread{

        @Override
        public void run() {
            Random random = new Random(100);
            while (true){
                String content = readFromServer(random);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                saveContent(content);
            }
        }

        /**
         * 从服务器读取数据
         * @return
         */
        private String readFromServer(Random random){
            return "content:" + random.nextInt(1000);
        }

        /**
         * 将文件写入本地
         * @param content
         */
        private void saveContent(String content) {
            System.out.println("saveContent:"+content);
        }
    }

    public static void testStopThread(){
        StopThread thread = new StopThread();
        thread.start();
    }

    public static void main(String[] args) {
        testStopThread();
    }
}
```

因为StopThread线程启动后就是一直运行，如果我们需要主动停止该线程的运行，该如何实现呢。
     
## 通过标志位来停止一个线程。

如果在线程中保存一个布尔类型的标志位，当需要主动停止线程时，就主动设置标志位的值。当线程检测到标志位为true的时候，就自己结束线程的运行。


```
private volatile  boolean mStop = false;

/**
* 终止线程
*/
public void stopThread(){
  mStop = true;
}

/**
* 检测是否设置的终止标志
* @return
*/
public boolean isStopThread(){
  return mStop;
}
``` 

然后在run()方法中添加检测标志位


```
@Override
   public void run() {
       Random random = new Random(100);
       while (true){
           /**
            * 如果检测到用户主动设置了停止标志，
            * 那就停止运行。
            */
           if (isStopThread()){
               break;
           }
           String content = readFromServer(random);
           try {
               Thread.sleep(1000);
           } catch (InterruptedException e) {
               e.printStackTrace();
           }

           saveContent(content);
       }
   }

```  

然后在testStopThread()方法中启动线程后，等待5秒钟后，调用StopThread实例的stopThread()设置终止标志位

```
public static void testStopThread(){
   StopThread thread = new StopThread();
   thread.start();

   try {
       Thread.sleep(5000);
   } catch (InterruptedException e) {
       e.printStackTrace();
   }

   thread.isStopThread();
}
```

这样修改完成后，重新运行程序，输出如下：

```
saveContent:content:915
saveContent:content:250
saveContent:content:874
saveContent:content:988
saveContent:content:291

Process finished with exit code 0
```
从结果上看现实StopThread线程在5秒钟终止运行了。

但是仅仅设置终止标示位就一定可以终止线程的运行吗？
比如把StopThread类run方法的线程等待时间修改为24个小时，那么StopThread进程每隔24个小时才会去检测一次标志位，才能够停止线程的运行。

有比如将线程等待除了sleep方法外，还有wait()放，所以假如线程进入了wait方法等待notify通知后才会重新运行检测标志位，如果一直没有接收到notify通知，那么便永远不会检测标志位，线程也无法终止运行。

```
try {
      wait();
  } catch (InterruptedException e) {
      e.printStackTrace();
  }
```
		
## 	利用线程中断机制终止线程。

可以利用标志位告诉线程需要终止，当线程检测到标志位的时候，就主动去停止运行，但是在很多时候线程进行了调用了sleep进入长时间的睡眠,或者调用了wait()、joint()进入长时间等待，这个时候线程就不能及时检测标志位，也不能及时停止线程的运行。这个时候就需要使用线程的中断机制来
解决问题。Thread类中提供了下面有关线程中断机制的方法

方法 | 含义
--- | ---
 public void interrupt() | 中断当前线程。
 public boolean isInterrupted() | 检测是否已经发生了中断。
 public static boolean interrupted() | 检测是否已经发生了中断，并清除中断状态。如果发生了中断返回true，接着访问就是false了。
 
 现在把上面的程序加入中断机制，在stopThread()方法中触发中断机制，然后Thread.sleep方法就会抛出InterruptedException，在异常处理中检测中断标志位，来决定线程是继续运行还是停止，最后的完整代码如下：
 
 
```
 /**
 * yumodev
 * 终止线程
 */
public class ThreadStop {
   static class StopThread extends Thread{

   private volatile  boolean mStop = false;

   /**
    * 设置中断标志位，并触发中断机制
    */
   public void stopThread(){
       mStop = true;
       //设置标志位后，立即中断线程。
       interrupt();
   }

   /**
    * 检测是否设置的终止标志
    * @return
    */
   public boolean isStopThread(){
       return mStop;
   }

   @Override
   public void run() {
       Random random = new Random(100);
       while (true){
           /**
            * 如果检测到用户主动设置了停止标志，
            * 那就停止运行。
            */
           if (isStopThread()){
               break;
           }

           String content = readFromServer(random);
           try {
               Thread.sleep(5000);
           } catch (InterruptedException e) {
               e.printStackTrace();
               //触发了线程中断
               if (isStopThread()){
                   break;
               }
           }

           saveContent(content);
       }
   }

   /**
    * 从服务器读取数据
    * @return
    */
   private String readFromServer(Random random){
       return "content:" + random.nextInt(1000);
   }

   /**
    * 将文件写入本地
    * @param content
    */
   private void saveContent(String content) {
       System.out.println("saveContent:"+content);
   }
}

public static void testStopThread(){
   StopThread thread = new StopThread();
   thread.start();

   try {
       Thread.sleep(20000);
   } catch (InterruptedException e) {
       e.printStackTrace();
   }

   thread.stopThread();
}

public static void main(String[] args) {
   testStopThread();
	}
}
```
## 参考

* 《图解Java多线程设计模式》
* [详细分析Java中断机制](http://www.infoq.com/cn/articles/java-interrupt-mechanism)
      

