[TOC]

### 问题

#### ANR的三种类型。

* KeyDispatchTimeout 5秒钟
* BroadcastTimeout 10秒钟
* ServiceTimeout 20秒钟

#### ANR 产生的原因

* 在主线程中进程高耗时操作
* 磁盘读写
* 大量的创建对象

#### 如何避免ANR和解决ANR问题

* 将高耗时操作放到内存中去。

#### ANR 日志分析

* 分析log。
* adb pull data/anr/trace.txt ./mytraces.txt

#### ANR工具分析

### Force Close

####  出现原因

* Error
* OOM
* StackOverFlowError
* Runtime异常。

#### 解决问题

* 查看log分析问题，解决异常。
* 使用Thread.UncaughtExceptionHandler捕获异常，进行处理。

### 避免ANR异常
   ANR:Application Not Responding 应用无响应。
   ANR有三种类型：
   1、KeyDispatchTimeout(5秒)--按键和触摸事件在特定的时间内无响应。
   2、BroadcastTimeout(10秒)--BroadcastReceiver在特定时间内无法处理完成。
   3、ServiceTimeout(20秒)Service 在特定时间内无法处理完成。

   只有第一种会显示对话框但是都会在log中输出错误信息，详细的信息会输出到/data/anr/traces.txt 文件中。

   如何避免ANR异常
   1、UI线程尽量只做跟UI相关的工作。
   2、耗时的工作比如数据库操作，IO，网络，以及其他耗时的操作都放到线程里面。
   3、利用Handler和AsyncTask处理UI线程和非UI线程之间的交互。







