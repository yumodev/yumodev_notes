### 什么是Intent

Intent主要用于进程间，进程和线程之间传递数据，实现按成Android各个组件之间的通信。
. 一个意图对象是传递到Context.startActivity() 或者Activity.startActivityForResult()来启动一个活动或者让一个存在的活动去做一些事情。

. 一个意图对象是传递给Context.startService()来发起一个服务或递交新的指令给运行中的服务。

. 传递给任意的广播。

### 什么是显式Intent 什么是隐式Intent

* 显式Intent：
调用Intent时，通过Intent.setComponent()和Intent.setClass()方法明确制定了组件名的Intent为显式意图，它制定了调用那个组件，主要用于App内部传递消息。

* 隐式意图：
打开Inetent时没有指定组件时就是隐式意图，隐式意图找到合适的组件主要用到了Intent Filter 来实现，根据隐式意图中设置的action,category,Uri等找到合适的组件，一般用户不用的应用程序之间的调用。

### Intent的Flag主要有哪些

   FLAG_ACTIVITY_NEW_TASK：默认，创建一个新的activity。
   FLAG_ACTIVITY_CLEAR_TOP:清除在其上的Activity对象，使自己为顶部。
   FLAG_ACTIVITY_BROUGHT_TO_FRONT:如果有存在，直接放到最顶端，不移除中间的activity。
   FLAG_ACTIVITY_RESET_TASK_IF_NEEDED：在Task的Activity的Activity Stack中设置一个还原点，当task恢复时，需要清理Activity，例如下一次Task带着FLAG_ACTIVITY_RESET_TASK_IF_NEEDED标记进入前台时，这个Activity和它之上的都将关闭，以至于用户不能在返回到它们，但是可以回到之前的Activity中。
   FLAG_ACTIVITY_SINGLE_TOP:如果已经在顶部就不会再启动
   FLAG_ACTIVITY_NO_HISTORY:退出后，不会存在栈中。
   
### Intent可以传递什么数据
  
 创建的内置类型，String类型，支持所有序列化对象。

### Intent的构成

   Action：用来指明要实施的动作是什么。主要有：ACTION_VIEW，ACTION_EDIT等等。
   Data：要施行动作需要的数据。一般用Uri表示
   Category：Intent的类别。
   component：制定Intent的目标组件类的名称。如果没有制定，将会根据action，data，category寻找一个最匹配的目标组件，如果制定了这个属性，就会不再查找直接食用它制定的组件。
   extras：附加信息。
   Flags:意图的运行模式
### IntentFilter

   描述了改组件所能响应Intent请求的能力，  
   响应一个隐式请求的时候根据Action，Data，Category进行判断。
### Android对Intent的解析

   对于显式的Intent，已经明确定义了目标组件的名称，直接调用目标组件响应即可。
   对于隐形的Intent，具体的选择的方法是：Android将intnet的属性和IntentFilter过滤器比较，IntentFilter中包含系统中所有可能的的待选组件，如果IntentFilter中某一组件匹配隐式Intent请求的内容，那么Android就选择该组件作为该隐式Intent的目标组件 
### 常用的IntentAction

   ACTION_VIEW: 用于向用户展示数据
   ACTION_MAIN: APP的主要进入口。
   ACTION_ATTACH_DATA: 用于制定一些数据应该附属于一些其他的地方。
   ACTION_EDIT:访问已给的数据，提供明确的可编辑
   ACTION_PICK:从数据总选择一个字条目
   ACTION_CHOOSER:显式一个Activity选择器
   ACTION_GET_CONTENT:允许用户选择特殊种类的数据
   ACTION_DIAL:拨打一个制定的号码，显式拨打界面
   ACTION_CALL:直接拨打一个电话
   ACTION_SEND:传递数据
   ACTION_SENDTO:发送一个信息到制定的某人
   ACTION_ANSWER:接听电话
   ACTION_INSERT:插入一条空项目到容器
   ACTION_DELETE:从容器中删除已给的数据
   ACTION_RUN:运行数据
   ACTION_SYNC:同步执行一个数据
   ACTION_PICK_ACTIVITY:为已知的Intent选择一个Activity，返回选中的类
   ACTION_SEARCH:执行一次搜索
   ACTION_WEB_SEARCH:执行一次Web搜索
   ACTION_FACTORY_TEST:工厂测试的主要进入点。
### 常见的Category

   CATEGORY_BROWSABLE:目标Activity可通过浏览器安全启动以显示一个链接相关的数据。
   CATEGORY_GADGET:activity可被嵌入另外一个拥有gadget的activity中
   CATEGORY_HOME:acitivyt的现实主页，用户按下home键时的界面。
   CATEGORY_LAUNCHER:activity是一个task的初始Acvitity，是程序启动的高优先级Activity
   CATEGORY_PREFERENCE:目标Activity为preference pancel

### 常见的Extras


### Android调用系统Email发送邮件

调用Email有三种类型的Intent:
.Intent.ACTION_SENDTO:无附件的发送
.Intent.ACTION_SEND:带附件的发送
.Intent.ACTION_SEND_MULTIPLE：带有多附件的发送

Intent.EXTRA_SUBJECT:标题
Intent.EXTRA_TEXT:邮件内容
Intent.EXTRA_EMAIL:收件人
Intent.EXTRA_CC:抄送人
Intent.EXTRA_BCC:密送人
Intent.EXTRA_STREAM:附件



### 常用Intent的实现列子
* 浏览器
   
``` 
 Intent  intent = new Intent(Intent.ACTION_VIEW);
 intent.setData(Uri.parse("http://www.baidu.com"));
 startActivity(intent);
```

* 打电话

  
```   
Intent  intent = new Intent(Intent.ACTION_DIAL);
intent.setData(Uri.parse("tel:101"));
startActivity(intent);
```







































