## Android

### Activity

* 什么是Activity？
* 简述Activity的生命周期？
* Activity 为什么要在注册文件中？
* 如何退出Activity？如何安全推出已调用多个的Activity的App
* 后台或者异常推出时，如何保存数据
* Activity在屏幕旋转时候的生命周期？
* Activity的启动模式？
* Activity和task任务栈相关的Intent启动模式有哪些？
* 什么是Activity栈？
* Activity的两种启动方式？
* 在同一个APP中，不同的Activity是否可以放在不同的Task任务栈中？
* 怎么加速启动Activity？

### Service

* Service的两种启动方式。
* Service中可以弹出Toast吗？
* Service中可以弹出Dialog吗？
* Activity和Service如何通信？
* Service如何不被杀死？
* Service是否运行在主线程中？
* 如何开机启动一个Service？
* 什么是IntentService？

### 广播

* 广播的两种注册方式？
* BroadCast的生命周期？

### ContentProvider

* 实现原理？
* ContentObserver接口？
* 怎么实现共享？

### Intent

* 常用的Intent？

### Binder和AIDL

* 简述IPC机制
* Binder是什么？为什么使用Binder？
* 简述Binder的模型。
* AIDL是否可以访问另外一个Service的Static变量。

### NDK

* Android 中NDK和JNI的使用。
* JNI的ENV参数。
* JNI工作时，Java层的类如何找到c++的类。

### Handler

* 说下Handler的机制。
* Hanlder、looper、MessageQue的关系？
* Looper是线程级别还是进程级别的？
* 如何获取Looper？
* HanderThread 的原理。
* Handler 怎么获取Looper对象。
* MessageQueue如何获取Message。
* Handler 处理线程通信还能做什么？
* sThreadLocal的含义。
* 一个线程可以获得另外线程的looper吗

### Fragment

* Fragment和Activity之间如何相互通信？
* Activity和Fragment的区别
* Fragment的生命周期和Activity生命周期的管理。
* 和Commit相似的方法还有什么？

### Context

* Context是什么？

### 架构

* Android的启动过程？
* 四大组件的应用场景？
* 另外三大组件和Activity是不是一个线程？
* Zygnote和System的启动过程？
* Android系统的开机过程？

### Android 虚拟机
* Dalvik 和 ART的不同？
* dalvik和JVM的区别？
* dex和class的区别？
* Android中ClassLoader和Java有什么区别？

#### 进程

* 一个应用可以开启几个进程？
* 一个应用可以创建几个虚拟机？
* 一个进程如何启动另外一个进程的Activity？
* Andorid进程和Dalvik VM的实例？
* 一个应用调用另外一个没有运行应用的Activity该如何实现。以及实现的原理。
* Android进程间通信。

#### 多线程

* 线程中如何刷新UI？
* 线程间如何通信？
* 如何判断当前为主线程？

#### AsyncTask

* AsyncTask的线程池多大？
* AsyncTask 和 AsyncQueryHandler 有什么不同。

### View

* View的绘制过程？
* Invalidate和PostInvalidate的区别？
* 子线程更新UI的几种方式？
* draw、OnDraw、dispatchDraw的区别？
* 三种测量模式、onMeasure获取长度？
* View的类图结构？
* 触摸事件的分发机制？
* SurfaceView和View的区别
* SurfaceView如何实现双缓冲机制

### 列表

* 为什么建议ViewHolder使用static关键字
* ListView的滑动卡顿怎么解决。
* LsitView如何做缓存。
* ListView 如何加载图片，用过的图片加载库。
* 下拉刷新原理。
* ListView的Adapter中的getView 的第一个参数 convertedView 有什么用。
* RecyclerView和ListView的区别。

### 资源

* px dp sp dpi pt 的概念和适配。

### 其他控件

* 解决ViewPager卡顿的现象
* ViewPager 加载Fragment优化的方法。
* ViewPager能否嵌套Activity。
* 如何判断ViewPager向左向右滑动。

### WebView

* WebView 加载JS的安全问题。
* WebView 不使用Webview Js Call  怎样实现数据交互。

### 图片

* 图片合适区分。
* 图片的压缩原理
* 1024*768 图片的大小
* 一个Bitmap究竟占用多大的内存。
* Bitmap能否复用。
* 渐进式显示大图，如何做到。

#### 内存

* 哪些编码会引发内存泄露？
* 什么情况下会引发内存溢出。
* 内存泄露和内存溢出有什么不同？
* 什么是三级缓存
* LRU算法的原理是什么？能够手写出来吗？
* System.gc()方法有什么作用
* LeakCanary的原理？

### 性能优化

* Android动态加载，如何解决方法数越界。
* 电量优化

### 打包和Gradle

* 多渠道打包。
* App安装过程，每次开机是否会重新安装所有的APP。
* 打包流程、keystore是什么。
* 如何防止二次打包。
* ANR的问题。
* APK的瘦身的方法。
* APK如何获取Crash信息。
* ANR日志分析。

### 安全机制

* 手机Root后，如何保证数据安全？
* 简述Android的安全机制？

### 混淆

* 为什么要混淆？
* 怎么实现混淆？
* 怎么让一个类避免混淆？
* 如何防止反编译，以及二次打包？

### 数据库Sqlite

* 是否支持并发访问？

### 架构

* MVC和MVP的区别？

### OKHttp

* SPDY
* 如何对链接进行复用。

### RxJava

* 使用的时候和传统代码有什么区别。


### 插件化

* 插件化原理
* 各个插件怎么实现资源访问。

### 热修复

* Android动态打补丁如何实现。

### Android其他面试题目

* 哪些系统属性可以当做设置唯一设备ID？
* Android中Kill和 Force Stop的区别？
* Android中应用信息页面清除数据都清除了什么数据？
* 聊聊你知道的Android新技术
