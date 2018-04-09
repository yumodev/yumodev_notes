# PowerManager

## 什么是PowerManager

PowerManager来管理电源和电量。

## WakeLock


```java
  //获取PowerManager对象。
    PowerManager powerManager = (PowerManager) getContext().getSystemService(Service.POWER_SERVICE);
    PowerManager.WakeLock wakeLock = powerManager.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK, "newWake");

    //获取唤醒锁。
    wakeLock.acquire();

    //执行自定义操作。

    //释放唤醒锁
    wakeLock.release();
```

* 创建WakeLock锁


```java
 public WakeLock newWakeLock(int levelAndFlags, String tag) {
    validateWakeLockParameters(levelAndFlags, tag);
    return new WakeLock(levelAndFlags, tag, mContext.getOpPackageName());
    }
```

其中参数levelAndFlags取值有

### level
* public static final int PARTIAL_WAKE_LOCK = 0x00000001;
  使CPU保持高性能运行，而屏幕和键盘背光可以被关闭。
* @Deprecated public static final int SCREEN_DIM_WAKE_LOCK = 0x00000006;
  只限制屏幕，保证亮起，但是允许亮度变低。
  
* @Deprecated public static final int SCREEN_BRIGHT_WAKE_LOCK = 0x0000000a;
  保持屏幕最高亮，但是键盘背光灯允许熄灭。
  
* @Deprecated public static final int FULL_WAKE_LOCK = 0x0000001a;
  
  保持屏幕最高亮，键盘背光灯也不允许熄灭。
  
* public static final int PROXIMITY_SCREEN_OFF_WAKE_LOCK = 0x00000020;
  用于和接近传感器使用，来实现电话类App中，手机贴近耳朵饿时候，将屏幕关闭，而
  离开的时候，又使屏幕亮的功能。
* public static final int DOZE_WAKE_LOCK = 0x00000040;
* public static final int DRAW_WAKE_LOCK = 0x00000080;

### Flags

*  public static final int ACQUIRE_CAUSES_WAKEUP = 0x10000000;

   和Level配合，可以使屏幕亮起。如果调用acquire()的时候，屏幕已经熄灭，就需要配合
   ACQUIRE_CAUSES_WAKEUP将屏幕点亮，不能和PARTIAL_WAKE_LOCK同时使用。

*  public static final int ON_AFTER_RELEASE = 0x20000000;

    和level配合，标记如果释放WakeLock的时候，屏幕处于亮着的状态，则在释放WakeLock之后，再让屏幕保持亮一小会儿，如果使用WakeLock的时候，屏幕已经熄灭，则不会有任何动作。


