# Service源码解析

## onStartCommand返回值


```java
   /**
     * Bits returned by {@link #onStartCommand} describing how to continue
     * the service if it is killed.  May be {@link #START_STICKY},
     * {@link #START_NOT_STICKY}, {@link #START_REDELIVER_INTENT},
     * or {@link #START_STICKY_COMPATIBILITY}.
     */
    public static final int START_CONTINUATION_MASK = 0xf;
    
    /**
     * Constant to return from {@link #onStartCommand}: compatibility
     * version of {@link #START_STICKY} that does not guarantee that
     * {@link #onStartCommand} will be called again after being killed.
     * START_STICKY的兼容版本，服务被杀死后，不保证被重启。
     */
    public static final int START_STICKY_COMPATIBILITY = 0;
    
    /**
     * Constant to return from {@link #onStartCommand}: if this service's
     * process is killed while it is started (after returning from
     * {@link #onStartCommand}), then leave it in the started state but
     * don't retain this delivered intent.  Later the system will try to
     * re-create the service.  Because it is in the started state, it will
     * guarantee to call {@link #onStartCommand} after creating the new
     * service instance; if there are not any pending start commands to be
     * delivered to the service, it will be called with a null intent
     * object, so you must take care to check for this.
     * 
     * <p>This mode makes sense for things that will be explicitly started
     * and stopped to run for arbitrary periods of time, such as a service
     * performing background music playback.

      如果服务返回该值，那么服务所在的进程被杀死后，会被重启，但是不会保存intent。
      然后系统会重建该服务，但是不会传递过intent。
     */
    public static final int START_STICKY = 1;
    
    /**
     * Constant to return from {@link #onStartCommand}: if this service's
     * process is killed while it is started (after returning from
     * {@link #onStartCommand}), and there are no new start intents to
     * deliver to it, then take the service out of the started state and
     * don't recreate until a future explicit call to
     * {@link Context#startService Context.startService(Intent)}.  The
     * service will not receive a {@link #onStartCommand(Intent, int, int)}
     * call with a null Intent because it will not be re-started if there
     * are no pending Intents to deliver.
     * 
     * <p>This mode makes sense for things that want to do some work as a
     * result of being started, but can be stopped when under memory pressure
     * and will explicit start themselves again later to do more work.  An
     * example of such a service would be one that polls for data from
     * a server: it could schedule an alarm to poll every N minutes by having
     * the alarm start its service.  When its {@link #onStartCommand} is
     * called from the alarm, it schedules a new alarm for N minutes later,
     * and spawns a thread to do its networking.  If its process is killed
     * while doing that check, the service will not be restarted until the
     * alarm goes off.

     杀死后，不重启。
     */
    public static final int START_NOT_STICKY = 2;

    /**
     * Constant to return from {@link #onStartCommand}: if this service's
     * process is killed while it is started (after returning from
     * {@link #onStartCommand}), then it will be scheduled for a restart
     * and the last delivered Intent re-delivered to it again via
     * {@link #onStartCommand}.  This Intent will remain scheduled for
     * redelivery until the service calls {@link #stopSelf(int)} with the
     * start ID provided to {@link #onStartCommand}.  The
     * service will not receive a {@link #onStartCommand(Intent, int, int)}
     * call with a null Intent because it will will only be re-started if
     * it is not finished processing all Intents sent to it (and any such
     * pending events will be delivered at the point of restart).
      重启并传递上次的Intent。
     */
    public static final int START_REDELIVER_INTENT = 3;

    /** @hide */
    @IntDef(flag = false, prefix = { "START_" }, value = {
            START_STICKY_COMPATIBILITY,
            START_STICKY,
            START_NOT_STICKY,
            START_REDELIVER_INTENT,
    })
    @Retention(RetentionPolicy.SOURCE)
    public @interface StartResult {}

    /**
     * Special constant for reporting that we are done processing
     * {@link #onTaskRemoved(Intent)}.
     * @hide
     */
    public static final int START_TASK_REMOVED_COMPLETE = 1000;

    /**
     * This flag is set in {@link #onStartCommand} if the Intent is a
     * re-delivery of a previously delivered intent, because the service
     * had previously returned {@link #START_REDELIVER_INTENT} but had been
     * killed before calling {@link #stopSelf(int)} for that Intent.
     */
    public static final int START_FLAG_REDELIVERY = 0x0001;
    
    /**
     * This flag is set in {@link #onStartCommand} if the Intent is a
     * retry because the original attempt never got to or returned from
     * {@link #onStartCommand(Intent, int, int)}.
     */
    public static final int START_FLAG_RETRY = 0x0002;
```



