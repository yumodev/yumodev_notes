<h1 align='center'>线程知识点</h1>

### 线程基础概念
#### 什么是线程

#### 进程和线程的区别

#### 创建线程的3种方式

#### 什么是线程的中断

线程中断并不是真正的中断线程，而是设置一个中断标志。
通过Interrupt() 方法将中断标志设置为true。
通过isInterrupt（）方法来检查是否中断标志的值。
此外Thread类还有一个静态方法interrupted() 获取标志位的值，但是它获取完后，会将标志位重置为false，所有如果仅仅是为了获取标志位的值，就不应当采用这个方法。

#### Thread线程的一些属性值

  1、获取线程名称 Thread.currentThread().getName()
  2、获取线程状态
  
#### volatile 关键字的含义

#### 线程的睡眠
       调用Tread类的静态方法Thread.Sleep()可以将当前正在运行的线程休眠制定的时间，当制定的时间结束后，该线程重新进入等待状态，准备执行。在线程休眠期间，如果其已经获得的对象的锁并不会释放掉。
       另外了用TimeUnit的sleep方法也可以实现睡眠。比如TimeUnit.SECONDS.sleep(1);
#### 线程等待
       通过通过Thread类的Join方法，它会阻塞等待该Thread执行完毕后。
#### 守护线程

       守护线程是一种优先级特别低的线程，通常在一个进程里面没有其他的线程运行时才会运营守护线程。当守护线程时一个进程的唯一的运行时时，JVM会结束守护线程并终止程序。
     
       守护线程不能用来做重要的任务，因为它不知道什么时候才会运行起来，通常是给其他的普通线程提供一些不太重要的服务，或者一些无限循环的等待服务请求活着执行线程任务。
    
       通过setDaemon（）和 isDaemon来设置和查询线程等待。当一个线程被设置为守护线程的时候，它就不允许修改为非守护线程，并且setDaemon只能在start()之前调用。
####   在线程中处理不受控制的异常

      由于Run方面不能抛出异常，而我们又需要捕获一些未知的异常的时候，我们需要通过UnCoughtExceptionHandler接口处理未知的异常。
      处理机制，首先，它寻找这个未捕捉对象的的异常Hadnler，如果没有找到，那么JVM就会在线程对象的ThreadGroup里选在非异常捕捉的handler，如果还没有找到，那么JVM就回去寻找非补货异常的Handler。如果没有Hnadler存在，那么JVM会把异常的Stack trace写入控制台并结束任务。

       class ExceptionHandler implements UncaughtExceptionHandler{

          @Override
          public void uncaughtException(Thread thread, Throwable ex) {
            // TODO Auto-generated method stub
            System.out.print(thread.getName() + " " + ex.getClass().getName() + " " + ex.getMessage());
          }
        }

        在线程start以前给其设置未处理一样的基础器。
        threadException.setUncaughtExceptionHandler(new ExceptionHandler());

#### 本地线程变量

      ThreadLocal将线程间共享的变量 存储在本地。提供了，get，set,remove,initivalValue方法来获取，设置，删除，初始化本地变量。

      如果创建一个runnable对象，多个线程共用一个该Runnable对象的时候，那么这些线程就将共用该Runable对象的全部属性。如果不想让一个属性被多个线程共享，就必须使用本地线程变量机制。
      
#### 线程组

     通过ThreadGroup 将线程分组，然后就可以统一对这一组线程进行操作了，比如中断组内的所有线程。
     常用方法：ThreadGroup threadGroup = new ThreadGroup("线程组名称");
     创建一个线程，并加入线程组： new Thread(threadGroup,new Runbale());

     activeCount:获取线程组数量。
     list():打印线程组信息
     enumerate(Thread[] threads)将线程组的线程复制到Threads数组内
     
      13、程序的退出
        当一个进程的所有线程结束，才会推出程序；如果main函数执行完毕，那些未运行完的线程还会继续运行，直到运行完成。如果在一个线程中调用System.exit()结束程序，那么所有的线程都将会停止。

    13、造成思索的
    
#### 线程安全
    
   允许被多个线程同时执行的代码，称作线程安全的代码。如果一个资源的创建、使用、销毁都是在同一个线程内完成，而且永远不会脱离该线程的控制，那么对该资源的使用就是线程安全的。
   
   
### 并发多线程

#### 竟态条件和临界区

当多个线程竞争统一资源时，如果对资源的顺序敏感，就是存在竟态条件。导致竟态条件发生的代码区称作临界区。
当多个线程同时访问同一个资源，并且其中的一个或者多个线程对这个资源进行了写操作，才会产生竟态条件。多线程同时同一个资源不会产生竟态条件。














