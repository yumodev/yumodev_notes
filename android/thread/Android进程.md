[TOC]

### 参考

[Android开发中何时使用多进程](https://github.com/android-cn/android-discuss/issues/7)
[欢迎来到Android多进程时代](https://github.com/hehonghui/android-tech-frontier/tree/master/androidweekly/%E6%AC%A2%E8%BF%8E%E6%9D%A5%E5%88%B0Android%E5%A4%9A%E8%BF%9B%E7%A8%8B%E6%97%B6%E4%BB%A3)

### Android进程。

每个应用程序都是运行在其自身的进程，具有唯一的pid。这允许让这个应用运行在相互隔离的环境中，被其他应用程序干扰。Android启用应用程序时，会从Zygnote中fork一个进程，并创建主线程。

每一个进程分配一个DalvikVM的实例。

`android:process `通过它可以给一个组件设置进程属性。

比如：

```<service
      android:name=".MusicService"
      android:process=":music"
    />
```

#### 多进程的好处

因为每个单独的进程都是管理内存的，所有在低内存环境中，有可能优先杀死APP的主进程，保留另外的进程。

#### 多进程的坏处

每一个进程都拥有自己的Dalvik VM实例，所以你不能简单的在进程间共享数据。。

### 面试

#### 一个应用可以开启几个进程？

可以的。在manifest.xml中将一个组件设置为android:process.就可以设置。
开启多个。


####一个应用可以创建几个虚拟机。

多个。

####一个进程如何启动另外一个进程的Activity

* 隐式Intent
* Intent设置className和component来启动。

#### 一个应用调用另外一个没有运行应用的Activity该如何实现。以及实现的原理。

* Intent

#### Android进程间通信。

* 隐式Intent
* ContentParovider
* 广播
* AIDL

#### Andorid进程和Dalvik VM的实例。

同一个概念，一个进程对应一个虚拟机。

#### 几种进程

* 前台进程
* 可见进程
* 服务进程
* 后台进程
* 空间城。







