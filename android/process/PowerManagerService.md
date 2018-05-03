# PowerManagerService源码基于8.0

## PowserManagerService 初始化

* SystemServer 中初始化


在系统服务端进行初始化。

```java

private void startBootstrapServices(){
    ......
    
     // Power manager needs to be started early because other services need it.
        // Native daemons may be watching for it to be registered so it must be ready
        // to handle incoming binder calls immediately (including being able to verify
        // the permissions for those calls).
        traceBeginAndSlog("StartPowerManager");
        mPowerManagerService = mSystemServiceManager.startService(PowerManagerService.class);
        traceEnd();

        // Now that the power manager has been started, let the activity manager
        // initialize power management features.
        traceBeginAndSlog("InitPowerManagement");
        mActivityManagerService.initPowerManagement();
        traceEnd();
        
        
    .....
    
      traceBeginAndSlog("MakePowerManagerServiceReady");
        try {
            // TODO: use boot phase
            mPowerManagerService.systemReady(mActivityManagerService.getAppOpsService());
        } catch (Throwable e) {
            reportWtf("making Power Manager Service ready", e);
        }
        traceEnd();

        traceBeginAndSlog("MakePackageManagerServiceReady");
        try {
            mPackageManagerService.systemReady();
        } catch (Throwable e) {
            reportWtf("making Package Manager Service ready", e);
        }
        traceEnd();

    
    ......
}
```

* ActivityManagerService.initPowerManagement


```java

  public void initPowerManagement() {
    mStackSupervisor.initPowerManagement();
    mBatteryStatsService.initPowerManagement();
    mLocalPowerManager = LocalServices.getService(PowerManagerInternal.class);
    PowerManager pm = (PowerManager)mContext.getSystemService(Context.POWER_SERVICE);
    mVoiceWakeLock = pm.newWakeLock(PowerManager.PARTIAL_WAKE_LOCK, "*voice*");
    mVoiceWakeLock.setReferenceCounted(false);
 }
```


