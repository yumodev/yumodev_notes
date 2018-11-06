# 安卓权限

## 安卓6.0

安卓6.0以后对权限做了区分，分为一般权限和危险权限。

### 一般权限

一般权限主要是不涉及用户隐私的权限，比如网络、震动等等，它们主要在清单文件中注册即可使用。

### 危险权限

危险权限主要是指涉及到用户权限隐私的文件不仅需要在清单文件中注册，还需要对用户进行请求权限弹窗提醒。

| 权限类型  | 方式 | 描述 
| --- | --- | --- 
| 相机 | android.permission.CAMERA | 使用相机 
| 存储 | android.permission.READ_EXTERNAL_STORAGE | 读SD卡权限
|  | android.permission.WRITE_EXTERNAL_STORAGE| 写SD卡权限
| 电话 | android.permission.READ_PHONE_STATE| 写SD卡权限
|  | android.permission.CALL_PHONE| 写SD卡权限
|  | android.permission.READ_CALL_LOG| 写SD卡权限
|  | android.permission.WRITE_CALL_LOG| 写SD卡权限
|  | android.permission.USE_SIP| 写SD卡权限
|  | android.permission.PROCESS_OUTGOING_CALLS| 写SD卡权限
| 位置 | android.permission.ACCESS_FINE_LOCATION| 写SD卡权限
|  | android.permission.ACCESS_COARSE_LOCATION| 写SD卡权限
| 联系人 | android.permission.READ_CONTACTS| 写SD卡权限
|  | android.permission.WRITE_CONTACTS| 写SD卡权限
|  | android.permission.GET_CONTACTS| 写SD卡权限
| 短信 | android.permission.SEND_SMS| 写SD卡权限
|  | android.permission.RECEIVE_SMS| 写SD卡权限
|  | android.permission.READ_SMS| 写SD卡权限
|  | android.permission.RECEIVE_WAP_PUSH| 写SD卡权限
| | android.permission.RECEIVE_MMS| 写SD卡权限
| | android.permission.READ_CELL_BROADCASTS| 写SD卡权限
| 日历  | android.permission.READ_CALENDAR | 读取日历 
|   | android.permission.WRITE_CALENDAR | 设置日历 
| 传感器  | android.permission.BODY_SENSORS | 设置日历 


## 查看权限获取


```
PermissionChecker.checkPermission
```

## 申请权限


```
requestPermissions
```

## 参考

* [Android权限管理之Permission权限机制及使用](http://www.cnblogs.com/whoislcj/p/6072718.html)
* [Android权限大全](http://www.cnblogs.com/classic/archive/2011/06/20/2085055.html)
* [从源码看6.0权限 —— 跟踪 Activity.requestPermissions()](https://fashare2015.github.io/2017/12/25/runtime-permission-analyzation/)
* [Android权限管理原理（4.3-6.0）](https://juejin.im/post/5878dc9d1b69e6006bdc0536)
* [Android6.0权限封装](https://blog.csdn.net/u014005316/article/details/60466047)
* [RxPermissions获取运行时权限](https://www.jianshu.com/p/314e9e27592f)






