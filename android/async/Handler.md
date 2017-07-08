# 什么是Handler

# Handler的原理

# Handler的优缺点

# 子线程中使用Handler
   1、子线程中创建Handler，需要先调用Loop.prepare();

# Message、Handler,MessageQueue,Looper
  Message :handler 接收和处理的消息

  MessageQueue:消息队列，以先进先出的方式管理Message。创建Looper对象的时候，会在它的构造器中创建MessageQueue对象。

  Looper：每个线程只能拥有一个looper。它的loop方法负责和读取MessageQueue的消息。把都到的消息发动到Handler进行处理。

  Handler:发送消息和接收消息。
     Handler正常工作必须在当前进程中存在一个Looper对象。
     . UI线程中系统已经初始化了一个Looper对象。因此程序直接创建Handler即可，然后就可通过Handler来发送消息和处理消息。
     . 非UI线程。程序员必须手动创建一个Looper对象，并调用looper.prePare()方法。prepare方法保证每个线程最多只有一个Looper对象。然后调用Looper的静态方法loop()启动。loop方法就是使用一个死循环不断的取出MessageQueue中的消息，并将取出的消息分给该消息对应的Handler进行处理。

