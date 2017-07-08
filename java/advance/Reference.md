# ref 

* [Android开发优化-使用软引用和弱引用](http://blog.csdn.net/arui319/article/details/8489451)
* [译文-理解Java中的弱引用](http://droidyue.com/blog/2014/10/12/understanding-weakreference-in-java/index.html)

# 引用分为
* 强应用（StrongReference）
* 软引用（SoftReference）
* 弱引用（WeakReference）
* 虚引用（PhantomReference）

#强引用

一个对象具有强引用，那么垃圾回收器绝对不会回收它。
当对象显示的设置为null或者超出对象的生命周期范围，那么GC就会认为该对象不存在引用，就会回收该对象。

#软引用

软引用用来描述一些还有用，但并非必需的对象。对于软引用关联着的对象，在系统将要发生内存溢出之前，回回收这些 内存，如果回收之后还是没有足够的内存，才会发生内存溢出异常。

String str = "adc";
SoftReference<String> softRef = new SoftReference<String>(Str)

SoftReference类提供的get方法会返回Java对象的强引用，一旦回收该Java对象之后返回的是null，所以在获取软引用对象的代码中，一定要判断是否为null，以免出现NullPointerException异常导致应用崩溃。

# 弱引用

只具有弱引用的对象拥有更短暂的生命周期。在GC线程扫描的过程中，如果发现了只具有弱引用的对象，不管当前的内存空间是否足够，都会回收它的内存。

#虚引用

虚引用也被成为幽灵引用或者幻影引用，它是最弱的一种引用。一个对象只有虚引用，那么它就和任何没有引用没有区别，GC在任何时候都可能回收它。虚引用主要用于跟踪对象被垃圾回收器回收的活动。虚引用必须和引用队列（ReferenceQueue）一起使用。当GC准备回收一个对象时，如果发现其有虚引用，就会在回收对象之前把这个虚引用加入到与之关联的引用队列中。

# 软引用和弱引用的选择

##选择软引用
* 仅仅是想避免OutOfMemory异常的发生，可以使用软引用。
* 如果该对象的使用频率比较高可以使用软引用

## 选择弱引用
* 如果需要经常回收占用较大内存的对象，应该选择弱引用。
* 如果该对象的使用频率较低可以使用弱引用。



