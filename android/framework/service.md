
# Service总结

## 什么使service

服务是Android中实现后台运行的解决方案，它非常适合不需要和界面交互，而又需要长时间运行的任务。

## 服务的两种启动方式

* context.startService()

    调用者和服务没有关联，就是调用着推出了，服务依然可以运行。它时启动服务，用stopService()停止服务，该该服务启动后，和一个Activity绑定了，还需要unBindService服务才能退出。

* context.bindService()

  调用者和服务有关系，调用者退出，服务也就退出了。一个serviece可以和多个Activiy获取关联，获取Bindler实例。如果该服务没有启动就启动这个服务，然后绑定，否则就是直接绑定。

## 服务的生命周期
  
  * onCreate : 只被调用一次
  * onStart():多次启动服务的时候，会被多次重复调用。
  * onStartCommand():里面处理参数。
  * onBind()； 不会被多次重复调用
  * unBind();
  * onDestory();

## 服务和线程之间如何传递数据 intent

1. 广播方式：sendBroadCast(); 使用向多个Activity发送消息。
2. 回调方式:定义接口主动通知数据 和广播相似，只是广播更加灵活一些。
3. binder方式： bind 成功以后会回调serviceConnection，在里面获取一个Bindler 得到该服务的实例对象，然后就可以操作服务，只是应用bind服务区。
4. intent只是初始化时候传递
5. 保存在sharepareshance， file ，sqlite
6. IDAL

## 如何保证服务不被kill掉

1、是个难题。
2、开启两个进程或者服务，检测到被关闭时就力气重启
3、onStartCommand START_STIKY 设置此标志后，进程被关闭后，会在内存充足的时候，重启该服务
4、提高进程优先级 startForgroud
5、在onDestory中重启
6、加行persitent属性
7、监听系统广播，获取状态。

## 服务中够弹出toast。

服务是可以直接弹出toast的。因为Service是运行在主线程中，所以可以直接弹出Toast。

## 服务中可以弹出Dialog吗

可以弹出Dialog。但是弹出Dialog的时候需要需要坐下特殊处理，因为Dialog必须要有一个Activity。可以把dialog设置为一个全局的dilog来处理
`dialog.getWindow().setType((WindowManager.LayoutParams.TYPE_SYSTEM_ALERT));`
添加权限：
`android.permission.SYSTEM_ALERT_WINDOW`

## service使运行在主线程中的吗，可以执行多长时间的错左。5秒
     
 主线程中。之所以使用service而不使用thread。因为Thread非常难以控制，如果开了多线程就更加难以控制了。Server就是一个管理后台服务和后台进程的接口。

## 停止一个服务

1、stopServie
2、自身停止：stopSelf， stopSelfforResult

## 服务的优先级：

1、如果服务处于onCreate或者onCommand中的时候不会被杀掉
2、Servie的优先级较高，它所在进程比后台进程要后，一般不会杀掉
3、service可以使用startForeground 方法设置自己为前台状态，那就默认时用户可见的不会被杀掉。
     
## Thread和Service有什么不同。

Thead：是程序执行的最小单元，是分配CPU的基本单位，用Thread执行一些异步操作。
Service：Service是android 的一种机制，当它运行的时候，如果是LocalService，那么对应的Service是运行在主进程的main线程上的。如果是远程Service，那么对应的Sercie是运行在独立进程的main线程上的。

## 开机启动一个service

    1、在Manafifest文件中定义一个开机启动的广播
    <!-- 开机启动的广播 -->
        <receiver android:name=".BootBroadCast" >
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" >
                </action>
            </intent-filter>
        </receiver>
        
      2、在接受到开机启动广播后，启动这个服务即可。


