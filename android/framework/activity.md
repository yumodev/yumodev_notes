## Activity 总结

### 什么是Activity

* Activiy是Android的四大组件之一，是和用户进行交互的解决方案，
  Activity是负责和用户交互的组件，让用户的看的至关重要。一个Activity是一个页面，也是一个容器，里面可以存放不同的小页面。
  
* Activity是Context的子类，同时实现了Window.callback和keyEvent.callback所以可以与用户进行交互
     
### Activity的生命周期

* onCreate: 活动第一次创建的时候进入
* OnStart：可见时进入
* OnResume:进入前台时进入
* OnPause：离开前台时进入
* OnStop：退出前台时进入
* onDestory:Activity销毁时进入。一般用来释放一些资源
* 一个Activity启动的时候的生命周期为：OnCreate->OnStart->OnResume
* 从当前Activity进入另外一个Activity时候的生命周期为：
onPause->onSaveInstanceState->onStop
* 从另外一个页面至少需要onStart，onResume两个周期
* 点击home键后的生命周期为：
onPause->onSaveInstanceState


### 如何退出Activity？如何安全推出已调用多个的Activity的App
* 在把Activity实例放到列表里面，推出时循环遍历finish掉。

### 后台或者异常推出时，如何保存数据
onSaveInstanceState 和 onRestoreInstanceState();
### Activity在屏幕旋转时候的生命周期是：
不设置Activity的android：configChanges时，切屏会重新调用各个生命周期，切横屏时会执行一次，切竖屏时执行两次
当在xml中对该Activity进行android:configChanges="orienation|keyboardHidden"时，切屏不会在重新调用各个周期，只会调用onConfigurationChanged()方法。
当在xml中对该Activity进行android:configChanges="orienation"时，切屏还会重新调用各个周期，切横、树屏时只会执行一次。
如果要销毁Actiity时，除了调用一切生命周期以外，还会调用onSaveInstanceState 和 onRestoryInstanceState();
### 默认四种模式 adb shell dumpsys activity 查看任务栈
standard 默认， singleTop，singleTask，singleInstance  可以在xml的launchMode来配置

* standared 标准模式一般情况下采用。激活该Activity时就向任务栈加入自己，推出的时候也会在任务栈中销毁。每次都是生成一个新的实例，当前的任务栈中允许有多个该活动的实例。
* singleTop模式：该Actity的实例是否处于栈顶，如果处于栈顶则无需创建新的实例，否则会在任务栈中创建新的实例。如果处于栈顶那么通过onNewIntent方法将Intent传递给该实例。
* singleTask模式： 在默认情况下，在当前的任务栈中还有该Activity的实例，就把它以上的全部实例移除栈，使的该实例处于栈顶的位置，如果没有就新创建该实例放到栈顶。也即在同一个任务栈中具有唯一性。
                               如果其设置了taskAffnity属性，且不为空，那么先去寻找这个任务栈，如果这个任务栈存在，那么就如同上述的操作，否则就会新开启一个任务栈。
* singleIntance模式。首先这个是个全局的实例应用，也就是该实例在android系统只实例化一次，在不同的App只要调用了该实例也是调用的这一个实例。；如果不存在该实例就新建一个任务栈，并在新建的任务栈中创建该实例最后由于该实例所在的栈中只有这么一个实例，所以在该实例启动的activity都是在另外的一个任务栈中，至于会不会新创建一个任务栈要看具体情况，可参见singleTask模式。多用于系统设置里面。
### 与Activity和task任务栈相关的Intent启动模式有哪些。
* FLAG_ACTIVITY_NEW_TASK 和singleTask 模式一样。选首先根据taskAffnity查找任务栈，如果该任务栈存在就在该栈中，否则就启动一个新任务栈。
* FLAG_ACTIVITY_CLEAR_TOP 清楚它上面的Activty，使自己到栈顶。
* FLAG_ACTIVITY_SINGLE_TASK 如果在该栈顶中已经存在了该实例就不在创建，否则就要创建新的实例
* FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET: 如果一个intent中包含属性，那么它启动的activity以及其上的所有的activity豆浆清楚task中，主要用于从后台到前台上面来。
* FLAG_ACTIVITY_REST_TASK_IF_NEEDED: 是否需要重置栈，含义不明。
### 栈的理解：
 * 栈位于Framwork层，使一组Activity的集合。
 * 栈是可以跨应用的，比如发送邮件。
 * taskAffnity 属性，可以将栈进行分组。如果activity不设置taskAffnity属性，那么它的默认值就是包名，如果设置为空，那么该Activity将不属于任何一个栈。
### Activity在onStart之前做了什么

### home键后Activity执行了什么方法。
  onPause->onSaveInstanceState->onStop
### 设置Activity的样式

  * 设置窗口样式
   android:theme="@android:style/Theme.Dialog"
  * 设置透明窗口样式
  
### Activity的两种启动方式
* 显示启动
  `startActivity(new Intent(FirstActivity.this, SecondActitiy.class))`
* 隐式启动
  
```Intent intent ＝ new Intent("com.yumo.android.intentstart");
  intent.addCategory("com.yumo.addroid.category");//可以选。如果没有设置，那么默认的类别就是android.intent.category.DEFAULT
  startActivity(intent);
```
### Android的应用中所有的Activity都必须运行在同一个线程中吗？
不同的Activity可以运行在不同的进程当中，但是在默认的情况下四大组件都是运行在一个进程里面的。

* 如果所有的Activity运行在一个进程当中，可以降低整个应用的不同组件之间的藕合度。
* 要想让Activity运行在独立的进程中，则需要在Androidmanifest.xml中对Activity的属性Android:process="value"进行配置，value的值就是此Activity所在的进程的进程名字。如果进程名以“：”开头表示私有进程，如果进程名以小写字母开头，则为全局进程。
* 进程之间主要看Binder机制通信。
### 在同一个程序中，不同的Activity中，是否可以放在不同的Task任务栈中。

可以放在不同的任务栈中，需要为不同的activity设置不同的taskaffinity属性，启动activity的Intent需要包含FLAG_ACTIVITY_NEW_TASK标记。
### Activity的三种状态

一个Activity基本上有三个生命状态。
Active状态：在前台显示，并且可以交互
Pause状态：虽然在前台显示，但是已经不能和用户狡猾
stop状态：activity的后台状态，已经被完全的隐藏起来，用户看不到了。

































