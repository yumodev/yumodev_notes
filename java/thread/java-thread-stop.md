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
		
### 	利用interrupt() 来中断线程。

      单单利用标志位结束一个线程，在某些情况下是非常完美的解决方案，而是线程进行了长时间的睡眠，调用了wait，joint时，就麻烦了，非常有可能进行长时间的阻塞，然后就不能检查标志位来停止一个线程了。关于这点，javaAPI提供一个方法 interrupt(),当线程调用该方法时，如果改线程内部调用了wait,sleep,joint 等等，处于阻塞状态时，就会抛出一个InterruptedException 异常，然后我们就有会在捕获这个异常后进行处理，并判断是否要退出线程，等等。ingerrupt 本身并不能中断一个线程，它能中断的阻塞，修改中断标志位等等。
      
      ###       利用stop()方法停止线程
      很不幸该方法已经被弃用了。但是该方法还是 的的确确可以让线程立刻停止的。可是问题就是处在立刻停止上面了。就是Stop()让该线程义无反顾的停止了运行，没有丝毫的犹豫，这就带了很多的问题，让线程里的其他成员和开启线程的类非常的不舒服，比如我希望在任何时候，在线程结束以前都打印一条日志，如果用Stop()方法，就不可能打印那条日志了。如果我们打开一个File，一个Socket，我们希望在线程结束的时候，能够调用一下close方法。这也做不到了。所以还是尽可能的少用Stop()方法吧。
      

