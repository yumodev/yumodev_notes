1、内存优化
   1、bitmap设置图片的大小。
   2、销毁bitmap对象。
   3、尽量避免static成员变量资源消耗过多的实例，比如context。
   4、使用Application的context。
   5、及时关闭资源，比如在查询数据库时需要及时关闭cursor。
   6、对经常食用的图片使用软引用保存。
2、避免内存泄漏，内存溢出
   1、图片溢出，单张图片需要压缩处理
   2、显示大量图片的溢出处理，利用图片的缓存处理。
   3、在一个进程中的内存由两个部分组成，java使用内存，c使用内存。而bitmap的生成是通过malloc进行内存分配的，占用的是c的内存。
   4、代码缺陷导致的内存溢出：对象被强引用；被JNI的指针饮用着。
   5、Cursor对象没有关闭，也会导致内存溢出
   6、一些广播和监听器注册了，却没有取消注册。
   7、构造适配器的时候，没有使用缓存的convertView
   8、Bitmap对象不再使用时没有及时调用recycle释放内存。

3、避免ANR异常
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

4、优化APP性能
   1、采用多线程，缓存数据，延迟加载，提前加载的手中，解决性能瓶颈。
   2、合理配置数据库混存类型和优化Sql语句加快读取速度，使用事务加快写入速度。
   3、不要将视图控件声明为static，会导致Activity退出时其对象本身无法被销毁，在城内存溢出。
   4、避免内部的Getters和Setters。
   5、布局层次尽可能的少，使用RelativeLayout代替LinearLayout实现相同的布局效果。
5、计算一个App的最大可用内存
      long maxMemory = Runtime.getRuntime().maxMemory();
      在我的华为手机的测试为96M。




























