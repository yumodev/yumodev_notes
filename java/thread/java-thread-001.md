<h1 align="center" > 线程基础 -线程的创建与执行 </h1>
### 什么是线程
        先在的操作系统基本上都是基于多线程的了，在实际的开发中，多线程是无处不在的。一个java程序运行起来，就会默认打开一个主线程。那什么是线程呢。
        线程依附于一个进程，是操作系统可以调度的最小单位。一个进程中的线程之间共用进程的内存和公共数据。
        使用多线程可以实现并发编程，提高程序的性能。

### 如何创建并执行。一个线程
        创建一个线程可以通过两种方法，但是最后执行都是调用Thread类的start()方法来启动一个线程。 start()会自动调用run() 执行线程的操作。
        
 1.  通过扩展一个Thread对象，来创建一个线程。
 
	     /**
		 * yumo
		 * 通过继承Thread  创建一个子线程类。
		 */
		class Thread1 extends Thread{
			//线程启动后自动调用run方法运行
			public void run(){
				System.out.println("thread1");
			}
		}
		
		 我们可以调用Thread的start()启动一个线程。
		
		 Thread1 thread = new Thread1();
		 thread.start();
		 
		 最后的运行结果为：
		 
		 thread1
		 
   2.  通过继承Runnable接口创建一个线程。
        
		     /**
			 * yumo
			 * 通过继承Runnable接口实现Runnable对象，重写其中的run()方法
			 */
			class Runnable1 implements Runnable {
		
				@Override
				public void run() {
					// TODO Auto-generated method stub
					System.out.println("runnable1");
				}
			}
		
              建立并执行该线程。
              
              Thread thread1 = new Thread(new Runnable1());
              thread1.start();
              
              输出结果为：	
              
              runnable1

