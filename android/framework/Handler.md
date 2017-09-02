# 什么是Handler

### Looper

#### 创建Looper

Looper只有一个私有的构造方法，所以不能通过new声明一个实例。
Looper通过两个静态方法：prepare(boolean)和prepareMainLooper()
主线程通过prepareMainLooper创建一个Looper，并放到sThreadLocal和sMainLooper中。
其他线程通过prepare()线程创建一个Looper，放到sThreadLocal。
Looper 有一个mThread对象保存和他相关联的looper。
在创建Looper的时候，就创建了一个MessageQueue。

#### 获取Looper。

主线程获取Looper。getMainLooper()
其他线程可以通过myLooper()  方法。


#### sThreadLocal的含义。

线程本地变量。sThreadLocal 为每个线程保存一个Looper。

#### Looper是线程级别还是进程级别的。

是线程级别的。因为每个Looper绑定一个线程，是在线程中创建的。

### MessageQueue

每个Looper关联一个MessageQueue。MessageQueue 是消息队列。

#### MessageQueue如何获取Message。

#### IdleHandler

### Message

Message类标识一个具体message。它有几个基本的属性：
Handler : 代表处理这个消息的handler。
what：一个消息的类型。
when：消息的处理时间。
callback：消息的回调，可以为空的。


#### 如何创建和发送一个消息

Message.obtain()或者Handler .obtainMessage()都可以。



### Handler

主要属性：
Looper:消息循环。
MessageQueue:消息队列。
Callback:回调。

主要方法：
dispatchMessage：分发方法
handlerMessage:  处理消息
obtainMessage()：生成一个消息。
post()、sendMessageAtTime：发送消息。
removeMessage():移除一个消息。

#### 消息时间问题。

每个消息都必须制定一个处理时间，如果为0，就是立即执行的。


### 子线程中使用Handler

   1、子线程中创建Handler，需要先调用Loop.prepare();

### Message、Handler,MessageQueue,Looper
  Message :handler 接收和处理的消息

  MessageQueue:消息队列，以先进先出的方式管理Message。创建Looper对象的时候，会在它的构造器中创建MessageQueue对象。

  Looper：每个线程只能拥有一个looper。它的loop方法负责和读取MessageQueue的消息。把都到的消息发动到Handler进行处理。

  Handler:发送消息和接收消息。
     Handler正常工作必须在当前进程中存在一个Looper对象。
     . UI线程中系统已经初始化了一个Looper对象。因此程序直接创建Handler即可，然后就可通过Handler来发送消息和处理消息。
     . 非UI线程。程序员必须手动创建一个Looper对象，并调用looper.prePare()方法。prepare方法保证每个线程最多只有一个Looper对象。然后调用Looper的静态方法loop()启动。loop方法就是使用一个死循环不断的取出MessageQueue中的消息，并将取出的消息分给该消息对应的Handler进行处理。
     
  




