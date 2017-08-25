# 2.1 Android IPC简介

IPC是 Inter-Process Communicaiton的缩写，进程间通信或者跨进程通信。
在Andorid中最有特色的进程间通信方式就是Binder。

# 2.2 Android中的多进程模式

## 2.2.1 开启多进程模式

通过在AndroidManifest中指定：android:process属性。
另外一种开启新进程的方法就是在通过JNI在native层去fork一个新的进程。

不指定：android:process。那么进程名字为默认包名。
指定为":remote",那么进程名字为"package_name:remote",输入当前应用的私有进程，其他应用的组件不可以和它跑在同一进程中。而进程名不易":"开头的进程属于全局进程，其他应用通过ShareUID方式可以和它跑在同一个进程中。

Android系统会为每一个应用分配一个唯一的UID，具有相同UID的应用才能共享数据。如果两个应用有相同的ShareUID并且签名相同才可以。在这种情况下，它们可以互相访问对方的私有数据，比如data目录、组件信息。

adb  shell os | grep 'package_name' 查看该应用正在运行的进程。

## 2.2.2 多进程模式的运行机制

Andorid为每一个进程分配了一个独立的虚拟机，不同的虚拟机在内存分配上有不同的地址空间，这就导致在不同的虚拟机中访问同一个类的对象对产生多分副本。
多进程带来的问题：
1、静态成员和单例模式完全失效。
2、线程同步机制完全失效。
3、SharePreferences的可靠性下降。
4、Application会多**次创建。

# 2.3 IPC基础概念介绍。



