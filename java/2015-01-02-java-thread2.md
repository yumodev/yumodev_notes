---
layout: post
title: 线程基础-手动停止线程
category: java
tags: [java]
keywords: java string
description: 
---

      线程启动后，run()方法运行结束后，就标志着线程结束并正常退出了。可是在一些情况下，比如软件关闭时，需要停止一些尚在运行的进程。手动停止线程有三种方式  
      1、 Thread自带的stop()方法
      2、 建立标志位来停止线程。
      3、 Thread的Interrupts  方法。
      
###       利用Stop()方法停止线程
      很不幸该方法已经被弃用了。但是该方法还是 的的确确可以让线程立刻停止的。可是问题就是处在立刻停止上面了。就是Stop()让该线程义无反顾的停止了运行，没有丝毫的犹豫，这就带了很多的问题，让线程里的其他成员和开启线程的类非常的不舒服，比如我希望在任何时候，在线程结束以前都打印一条日志，如果用Stop()方法，就不可能打印那条日志了。如果我们打开一个File，一个Socket，我们希望在线程结束的时候，能够调用一下close方法。这也做不到了。所以还是尽可能的少用Stop()方法吧。
           
###     通过标志位来停止一个线程。
      在我以往的开发工作中，我一向是利用利用标志位来手动的停止一个线程的。它的原理就是在run() 方法里面进行一个条件判断，满足条件就接着运行该线程，否则话就进行一些收尾工作，来停止该线程的运行。下面我建立一个不停的打印自己名字的线程，然后 通过一个标志位来结束这个线程。
         
         class Runnable2 implements Runnable{
		
			//初始设置标志位为false
			private boolean mStop = false;
			
			
			/**
			 *  设置停止标志位为true。在run()方法里检测到这个值以后，就会停止线程的运行了。
			 * yumo
			 * void
			 * 2015-1-3
			 */
			public void Stop(){
				mStop = true;
			}
	
			@Override
			public void run() {
				// TODO Auto-generated method stub
				while(true)
				{
					if(mStop) 
					{
						System.out.println("检测到标志位改变，要我停止运行。好吧，现在就跳出循环，准备谢幕");
						break;
					}
					
					System.out.println( Thread.currentThread().getName());
					
					try {
						//让该线程，睡眠50毫秒
						Thread.sleep(50);
					} catch (InterruptedException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
				}
				
				System.out.println("线程终于结束了");
			}
			
		}
			
		在上面我们建立了Runnable2 类继承了Runnable接口。现在我们建立一个线程运行起来。
		
		Runnable2 runnable =  new Runnable2();
		Thread thread2 = new Thread(runnable, "Runnable2");
		thread2.start();
		
		经过打印结果我们发现这是一个死循环，如果不手动停止该线程，它将会一直运行下去。
		
		下面就是利用标志位停止线程发生威力的时候了。
	        Runnable2 runnable =  new Runnable2();
		Thread thread2 = new Thread(runnable, "Runnable2");
		thread2.start();
		
		try {
			Thread.sleep(2000);
		} catch (InterruptedException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		runnable.Stop();
		
		最后的输出结果如下：
			............
			Runnable2
			Runnable2
			Runnable2
			Runnable2
			Runnable2
			检测到标志位改变，要我停止运行。好吧，现在就跳出循环，准备谢幕
			线程终于结束了
		
		最后我们看到线程成功的结束了。
		
### 	利用interrupt() 来中断线程。

      单单利用标志位结束一个线程，在某些情况下是非常完美的解决方案，而是线程进行了长时间的睡眠，调用了wait，joint时，就麻烦了，非常有可能进行长时间的阻塞，然后就不能检查标志位来停止一个线程了。关于这点，javaAPI提供一个方法 interrupt(),当线程调用该方法时，如果改线程内部调用了wait,sleep,joint 等等，处于阻塞状态时，就会抛出一个InterruptedException 异常，然后我们就有会在捕获这个异常后进行处理，并判断是否要退出线程，等等。ingerrupt 本身并不能中断一个线程，它能中断的阻塞，修改中断标志位等等。
      

