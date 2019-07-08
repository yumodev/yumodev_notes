#Event

## 参考

* [https://github.com/greenrobot/EventBus](https://github.com/greenrobot/EventBus)
* [关于EventBus，从使用到解析（上）](https://www.jianshu.com/p/251a1e1b793a)

## 介绍

EventBus是一个Android端优化的publish/subscribe消息总线，简化了应用程序内各组件间、组件与后台线程间的通信。

作为一个消息总线主要有三个组成部分：

事件（Event）：可以是任意类型的对象。通过事件的发布者将事件进行传递。

事件订阅者（Subscriber）：接收特定的事件。

事件发布者（Publisher）：用于通知 Subscriber 有事件发生。可以在任意线程任意位置发送事件。

## 使用指南

### 在项目的`build.gradle`中的添加依赖

`implementation 'org.greenrobot:eventbus:3.1.1'`


### 构造事件对象


```
public class MessageEvent { 

/* Additional fields if needed */

}

```

### 注册和解除事件订阅


一般在Activity或者Fragment的onCreate()方法中注册事件，在onDestory()中解除注册

```
  @Override
 public void onCreate() {
     super.onCreate();
     /
     EventBus.getDefault().register(this);
 }

 @Override
 public void onDestory() {
     super.onDestory();
 
 }
 ```

### 接收事件

在Event3.0之前onEvent()方法是用爱接收指定事件类型的对象。在EventBus3.0之后，onEvent()方法可以自定义方法名。

```
@Subscribe(threadMode = ThreadMode.MAIN)  
public void onMessageEvent(MessageEvent event) {/* Do something */};
```

### 发送事件


```

EventBus.getDefault().post(new MessageEvent());

```

## Event线程模式

* PostThread

    事件的处理和事件的发送在相同的进程。事件处理时间不宜太长，会堵塞当前线程。
    
* MainThread

    事件的处理会在UI线程中执行，事件处理时间不宜太长，否则会导致ANR
    
* BackgroundThread

    如果是UI线程发送的事件，会在子线程中运行，如果是在子线程中发出的，还是在当前线程中执行。
    
* Async

    事件处理会在单独的线程中执行，主要用于在后台线程中执行耗时操作，每个事件都会开启一个线程。
    
    
## 事件的优先级


