# WakefulBroadcastReceiver

## 介绍

WakefulBroadcastReceiver是一种特殊的广播接收器, 可以自动创建和管理唤醒锁PARTIAL_WAKE_LOCK来执行任务.

## 过程

收到广播后, 启动Service来处理耗时任务, 同时确保设备在整个Service执行过程中保持唤醒状态, 

## 定义接收器

`<receiver android:name=".MyWakefulReceiver"></receiver>`

## 启动服务

在onReceive中启动服务,一般为IntentService服务.


```java
Intent service = new Intent(context, MyIntentService.class);
startWakefulService(context, service);
```

## 释放锁-completeWakefulIntent(intent)


```java
public class MyIntentService extends IntentService {
    public static final int NOTIFICATION_ID = 1;
    private NotificationManager mNotificationManager;
    NotificationCompat.Builder builder;
    public MyIntentService() {
        super("MyIntentService");
    }
    @Override
    protected void onHandleIntent(Intent intent) {
        Bundle extras = intent.getExtras();
        // Do the work that requires your app to keep the CPU running.
        // ...
        // Release the wake lock provided by the WakefulBroadcastReceiver.
        MyWakefulReceiver.completeWakefulIntent(intent);
    }
}

```

## 和BroadcastReceiver的区别

BroadcastReceiver不保证CPU将保持苏醒.

