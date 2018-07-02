## 什么是广播
   
接收来自系统和应用中的广播。
   
## 注册广播有几种方式 有何优缺点

* 清单文件中注册，单独写一个广播类 。常驻型广播，程序关闭后也会接受到通知，比如开机自启动。
* 在代码中进行注册。动态。应用程序关闭后，就不会在接受到广播了。优点就是动态的注册和取消广播，但是缺点是必须在程序启动以后才能注册和取消广播。
     
### 广播的生命周期。

广播在onRecive后就自动停止广播了，这个可能停止掉，所以不能在onRecive里进行长时间的操作。应该在onrecieve里面新建立一个线程或者服务进行长时间的此奥做。

### 广播的种类

   * 标准广播
        标准广播是一种完全异步执行的广播，在广播发出后，所有的广播接收器都可以接收到这么一个广播，它们之间没有先后顺序。这种的广播的效率比较高，但是也是无法拦截这种广播的。
   
   * 有序广播
         这是一种同步执行的广播，在广播发出以后，同一时间只能由一个广播接收到这条广播消息，当这个广播接收器中的逻辑执行完毕后，广播才会继续传递。优先级高的广播接收器会先接收到广播，前面广播接收器可以截断广播的传递。
   
   * 粘性广播 StickyBroadCast广播
         如果发送者发送了某一个广播，而接收者在这个广播发送后才注册的自己的receiver，这时接收者就无法接到广播。为此Android引入了StickyBroadcast，在广播发送结束后会保存刚刚发送的广播。系统网络状态改变发送的广播就是粘性广播
         
### 广播的发送

* 发送系统广播：
sendBroadcast(new intent("com.intent.broadcast"));
* 发送和终止有序广播
sendOrderedBroadcast(new Intent("con.intnet.broadcast"),null); 第二个参数是权限。
abortBroadcast(); 截断广播，不在发送。

### 本地广播

   本地广播无法通过静态注册方式来接收的，使用本地广播可以保证安全性，防止数据泄密；其他的程序无法接收到我们的广播；发送本地广播比发送全局广播高校的多。
  
```
 LocalBroadcastManager localBroadBroadcastManager ＝ LocalBroadcastManager.getInstance(this);

//发送广播
 localBroadcastManager.sendBroadcast(intent);
 
  LocalReceiver localReceiver = null；
//注册广播   localBraodcastManager.registerReceiver(localReceiver,intentFilter);
 
 //反注册广播  localBroadcastManager.unregisterReceiver(localReceiver);
```

## 常用广播

* Intent.ACTION_USER_PRESENT - android.intent.action.USER_PRESENT 

  屏幕解锁
  
* android.intent.action.SCREEN_ON

 开屏

* android.intent.action.SCREEN_OFF

锁屏

* android.intent.action.CLOSE_SYSTEM_DIALOGS

关闭系统对话框

* android.intent.action.BOOT_COMPLETED

开机启动

* android.net.conn.CONNECTIVITY_CHANGE

网络变化

* android.intent.action.TIME_SET

改变时间,重新设置

* android.intent.action.DATE_CHANGED

日期发生改变

* android.intent.action.TIMEZONE_CHANGED

改变时区

* android.intent.action.TIME_TICK_ACTION
* android.intent.action.TIME_TICK

当前时间已经变化

* android.intent.action.PACKAGE_ADDED

新安装一个APP

* android.intent.action.PACKAGE_REMOVED

删除一个APP

* android.intent.action.PACKAGE_REPLACED

替换一个APP

* android.intent.action.BATTERY_CHANGED_ACTION
* android.intent.action.BATTERY_CHANGED

电量发生变化

* android.intent.action.MEDIA_BAD_REMOVAL

扩展卡从sd卡槽诶拔出.

* android.intent.action.MEDIA_EJECT

想要移除扩展介质

* android.intent.action.MEDIA_REMOVED

扩展介质被移除

* android.intent.action.MEDIA_SCANNER_FINISHED

扫描完介质

* android.intent.action.MEDIA_SCANNER_STARTED

开始扫描介质

* android.intent.action.MEDIA_SHARED

扩展介质被解除

* android.intent.action.MEDIA_UNMOUNTED

扩展介质存在,但是还没有挂在

* android.intent.action.MEDIA_MOUNTED

扩展介质被插入.

* android.intent.action.ACTION_POWER_CONNECTED

充电

* android.intent.action.ACTION_POWER_DISCONNECTED

断电

## 参考

* [Android Intent Action汇总](https://blog.csdn.net/bbenskye/article/details/50995519)


