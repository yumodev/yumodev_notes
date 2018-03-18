# ActivityManagerService

[ActivityManagerService](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/services/java/com/android/server/am/ActivityManagerService.java)
[Instrumentation.java](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/core/java/android/app/Instrumentation.java)
[ActivityThread](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/core/java/android/app/ActivityThread.java)
AMS是Android中最核心的服务，主要负责系统中四大组件的启动、切换、调度以及应用进程的挂你和调度工作。

AMS由ActivityManagerNative类派生，并实现Watchdog.Monitor等接口
AMN由Binder派生，实现IActivityManager接口。

客户端通过获取到的ActivityManager类调用AMN的getDefault函数等到一个ActivityManagerProxy对象，通过它与AMS通信。

## ActivityManagerService

在system_server中的ServerThread线程线程创建。

## SystemServer其注册和启动AMS服务


```
class ServerThread extends Thread {
	 public void run(){
	 
	 .....
	  Slog.i(TAG, "Activity Manager");
	  //调用ams的main函数生成Context
    context = ActivityManagerService.main(factoryTest);
    
    ...
    //将system_server进程放到AMS中。
    ActivityManagerService.setSystemProcess();
    
    
    ...
    
    Slog.i(TAG, "System Content Providers");
    ActivityManagerService.installSystemProviders();
    
    ...
    Slog.i(TAG, "Init Watchdog");
    Watchdog.getInstance().init(context, battery, power, alarm,
                    ActivityManagerService.self());
                    
    Slog.i(TAG, "Window Manager");
    wm = WindowManagerService.main(context, power,
                    factoryTest != SystemServer.FACTORY_TEST_LOW_LEVEL);
    ServiceManager.addService(Context.WINDOW_SERVICE, wm);

    ((ActivityManagerService)ServiceManager.getService("activity"))
                    .setWindowManager(wm);
                    
     //AMS是系统的核心，只有它准备好以后，才能调用其他的服务的SystemReady。    
    ((ActivityManagerService)ActivityManagerNative.getDefault())
                .systemReady(new Runnable() {
            public void run() {
                Slog.i(TAG, "Making services ready");

                if (statusBarF != null) statusBarF.systemReady2();
                if (batteryF != null) batteryF.systemReady();
                if (connectivityF != null) connectivityF.systemReady();
                if (dockF != null) dockF.systemReady();
                if (usbF != null) usbF.systemReady();
                if (uiModeF != null) uiModeF.systemReady();
                if (recognitionF != null) recognitionF.systemReady();
                Watchdog.getInstance().start();

                // It is now okay to let the various system services start their
                // third party code...

                if (appWidgetF != null) appWidgetF.systemReady(safeMode);
                if (wallpaperF != null) wallpaperF.systemReady();
                if (immF != null) immF.systemReady();
                if (locationF != null) locationF.systemReady();
                if (throttleF != null) throttleF.systemReady();
            }
        });            
	 .....
	 }
}

```


## ActivityManagerService的main函数

AMS的main函数由ServerThread线程调用，但是AMS自己则新创建了一个线程，即AThread线程。

```java
 public static final Context main(int factoryTest) {
    //创建AThread线程，并启动。
    AThread thr = new AThread();
    thr.start();
 
 	 //等待AMS启动成功了，设计到wait和notify通信机制。
    synchronized (thr) {
        while (thr.mService == null) {
            try {
                thr.wait();
            } catch (InterruptedException e) {
            }
        }
    }

    ActivityManagerService m = thr.mService;
    mSelf = m;
    //调用ActivityThread的systemMain()；方法，生成一个系统的ActivityThread。
    ActivityThread at = ActivityThread.systemMain();
    mSystemThread = at;
    //获取系统Context。
    Context context = at.getSystemContext();
    m.mContext = context;
    m.mFactoryTest = factoryTest;
    //创建Activity栈。
    m.mMainStack = new ActivityStack(m, context, true);
    
    //发布点亮等统计。
    m.mBatteryStatsService.publish(context);
    m.mUsageStatsService.publish(context);
    
    //通知等待的东东。
    synchronized (thr) {
        thr.mReady = true;
        thr.notifyAll();
    }

    //调用Start的startRunning。
    m.startRunning(null, null, null, null);
    
    return context;
}
```

* AThread

主要作用的创建ActivityManagerService()服务
双线程互相等待常见线程。
```java

static class AThread extends Thread {
    ActivityManagerService mService;
    boolean mReady = false;

    public AThread() {
        super("ActivityManager");
    }

    public void run() {
        Looper.prepare();

        android.os.Process.setThreadPriority(
                android.os.Process.THREAD_PRIORITY_FOREGROUND);
        android.os.Process.setCanSelfBackground(false);
        //通过New的方式创建了AMS
        ActivityManagerService m = new ActivityManagerService();

        synchronized (this) {
            mService = m;
            notifyAll();
        }

        synchronized (this) {
            while (!mReady) {
                try {
                    wait();
                } catch (InterruptedException e) {
                }
            }
        }

        Looper.loop();
    }
}
```

* ActivityManagerService的构造方法


```java
  private ActivityManagerService() {
        String v = System.getenv("ANDROID_SIMPLE_PROCESS_MANAGEMENT");
        if (v != null && Integer.getInteger(v) != 0) {
            mSimpleProcessManagement = true;
        }
        v = System.getenv("ANDROID_DEBUG_APP");
        if (v != null) {
            mSimpleProcessManagement = true;
        }

        Slog.i(TAG, "Memory class: " + ActivityManager.staticGetMemoryClass());
        
        File dataDir = Environment.getDataDirectory();
        File systemDir = new File(dataDir, "system");
        systemDir.mkdirs();
        mBatteryStatsService = new BatteryStatsService(new File(
                systemDir, "batterystats.bin").toString());
        mBatteryStatsService.getActiveStatistics().readLocked();
        mBatteryStatsService.getActiveStatistics().writeAsyncLocked();
        mOnBattery = DEBUG_POWER ? true
                : mBatteryStatsService.getActiveStatistics().getIsOnBattery();
        mBatteryStatsService.getActiveStatistics().setCallback(this);
        
        mUsageStatsService = new UsageStatsService(new File(
                systemDir, "usagestats").toString());

        GL_ES_VERSION = SystemProperties.getInt("ro.opengles.version",
            ConfigurationInfo.GL_ES_VERSION_UNDEFINED);

        mConfiguration.setToDefaults();
        mConfiguration.locale = Locale.getDefault();
        mProcessStats.init();
        
        // Add ourself to the Watchdog monitors.
        Watchdog.getInstance().addMonitor(this);

        mProcessStatsThread = new Thread("ProcessStats") {
            public void run() {
                while (true) {
                    try {
                        try {
                            synchronized(this) {
                                final long now = SystemClock.uptimeMillis();
                                long nextCpuDelay = (mLastCpuTime.get()+MONITOR_CPU_MAX_TIME)-now;
                                long nextWriteDelay = (mLastWriteTime+BATTERY_STATS_TIME)-now;
                                //Slog.i(TAG, "Cpu delay=" + nextCpuDelay
                                //        + ", write delay=" + nextWriteDelay);
                                if (nextWriteDelay < nextCpuDelay) {
                                    nextCpuDelay = nextWriteDelay;
                                }
                                if (nextCpuDelay > 0) {
                                    mProcessStatsMutexFree.set(true);
                                    this.wait(nextCpuDelay);
                                }
                            }
                        } catch (InterruptedException e) {
                        }
                        updateCpuStatsNow();
                    } catch (Exception e) {
                        Slog.e(TAG, "Unexpected exception collecting process stats", e);
                    }
                }
            }
        };
        mProcessStatsThread.start();
    }
```

* ActivityThread.sytemMain

ActivityThread是AndroidFramework中一个非常重要的类，代表一个应用进程的主线程，主要作用
就是调度和执行在该线程中运行的四大组件。

```java
 public static final ActivityThread systemMain() {
    ActivityThread thread = new ActivityThread();
    //true代表是系统进程。
    thread.attach(true);
    return thread;
}

 private final void attach(boolean system) {
    //将当前进程保存为线程私有数据，方便取出。
    sThreadLocal.set(this);
    //是否为系统进程
    mSystemThread = system;
    if (!system) {
        ViewRoot.addFirstDrawHandler(new Runnable() {
            public void run() {
                ensureJitEnabled();
            }
        });
        android.ddm.DdmHandleAppName.setAppName("<pre-initialized>");
        RuntimeInit.setApplicationObject(mAppThread.asBinder());
        IActivityManager mgr = ActivityManagerNative.getDefault();
        try {
            mgr.attachApplication(mAppThread);
        } catch (RemoteException ex) {
        }
    } else {
        // Don't set application object here -- if the system crashes,
        // we can't display an alert, we just want to die die die.
        //设置线程名字为system_process
        android.ddm.DdmHandleAppName.setAppName("system_process");
        try {
            mInstrumentation = new Instrumentation();
            //创建contextImple
            ContextImpl context = new ContextImpl();
            context.init(getSystemContext().mPackageInfo, null, this);
            //创建Application
            Application app = Instrumentation.newApplication(Application.class, context);
            
            mAllApplications.add(app);
            mInitialApplication = app;
            //调用Applicaiton的onCreate方法。
            app.onCreate();
        } catch (Exception e) {
            throw new RuntimeException(
                    "Unable to instantiate Application():" + e.toString(), e);
        }
    }
    
    //注册ConfigCallback。
    ViewRoot.addConfigCallback(new ComponentCallbacks() {
        public void onConfigurationChanged(Configuration newConfig) {
            synchronized (mPackages) {
                // We need to apply this change to the resources
                // immediately, because upon returning the view
                // hierarchy will be informed about it.
                if (applyConfigurationToResourcesLocked(newConfig)) {
                    // This actually changed the resources!  Tell
                    // everyone about it.
                    if (mPendingConfiguration == null ||
                            mPendingConfiguration.isOtherSeqNewer(newConfig)) {
                        mPendingConfiguration = newConfig;
                        
                        queueOrSendMessage(H.CONFIGURATION_CHANGED, newConfig);
                    }
                }
            }
        }
        public void onLowMemory() {
        }
 }      
```

 * ActivityThread.getSystemContext


```java

 public ContextImpl getSystemContext() {
    //单例模式 mSystemContext 单例模式
    synchronized (this) {
        if (mSystemContext == null) {
            ContextImpl context =
                ContextImpl.createSystemContext(this);
            
            //表示加载到系统的APK。第四个参数为0，它没有关联到具体的ApplicationInfo
            LoadedApk info = new LoadedApk(this, "android", context, null);
            //初始化ContextImpl对象。
            context.init(info, null, this);
            //初始化资源信息
            context.getResources().updateConfiguration(
                    getConfiguration(), getDisplayMetricsLocked(false));
            mSystemContext = context;
       }
    }
    return mSystemContext;
}

```

* ActivityManagerService。startRunning


```java

public final void startRunning(String pkg, String cls, String action,
            String data) {
    synchronized(this) {
        if (mStartRunning) {
            return;
        }
        mStartRunning = true;
        mTopComponent = pkg != null && cls != null
                ? new ComponentName(pkg, cls) : null;
        mTopAction = action != null ? action : Intent.ACTION_MAIN;
        mTopData = data;
        if (!mSystemReady) {
            return;
        }
    }

    systemReady(null);
}
```

## ActivityManagerService函数的总结

AMS的main函数的目的有两个就是创建AMS对象，并且创建一个供System_Server 进程使用的Android运行环境。

## ActivityManager.setSystemProcess

AMS向PKMS查询名称为android的ApplicationInfo

```java

public static void setSystemProcess() {
    try {
        ActivityManagerService m = mSelf;
        
        //注册服务
        ServiceManager.addService("activity", m);
        //打印内存的服务
        ServiceManager.addService("meminfo", new MemBinder(m));
        if (MONITOR_CPU_USAGE) {
            ServiceManager.addService("cpuinfo", new CpuBinder(m));
        }
        ServiceManager.addService("permission", new PermissionController(m));

        //通过PMS获取包名为android的ApplicationInfo。
        ApplicationInfo info =
            mSelf.mContext.getPackageManager().getApplicationInfo(
                    "android", STOCK_PM_FLAGS);
        //ActivityThread
        mSystemThread.installSystemApplicationInfo(info);
   
        synchronized (mSelf) {
            //进程管理
            ProcessRecord app = mSelf.newProcessRecordLocked(
                    mSystemThread.getApplicationThread(), info,
                    info.processName);//名字设置为system
            app.persistent = true;//可以创筑
            app.pid = MY_PID;//pid设置为system_server进程号
            app.maxAdj = SYSTEM_ADJ;//
            mSelf.mProcessNames.put(app.processName, app.info.uid, app);
            synchronized (mSelf.mPidsSelfLocked) {
                mSelf.mPidsSelfLocked.put(app.pid, app);
            }
            mSelf.updateLruProcessLocked(app, true, true);
        }
    } catch (PackageManager.NameNotFoundException e) {
        throw new RuntimeException(
                "Unable to find android system package", e);
    }
}

```

* ActivityThread.installSystemApplicationInfo


```java
  public void installSystemApplicationInfo(ApplicationInfo info) {
    synchronized (this) {
        ContextImpl context = getSystemContext();
        //context 初始化进行了两次，第一次仅仅是创建了一个Android运行环境，
        //第二次执行的时候，先利用Context和PKMS交互等到一个实际的ApplicationInfo，然后
        //通过init将次Context和ApplicationInfo绑定
        context.init(new LoadedApk(this, "android", context, info), null, this);
    }
}

```


* ProcessRecord和IApplication

  AMS是专门用于进程管理和调度的，所以运行的APK的进程应该在AMS中有对应的管理结构，因此AMS的下一步工作就是讲这个运行环境和一个进程管理结构对应起来，并交由AMS统一管理。
  AMS中的进程管理工具为ProcessRecord。
  AMS如何与应用进程交互，如果AMS启动一个位于其他进程的Activity，势必要通过Binder机制进行跨进程通信，为此Andorid提供了一个IApplicationThread接口，该接口定义了AMS和应用进程之间的交互函数。
 
  ApplicationThreadNative实现了IApplicationThread接口。AMS通过其可以和应用进程交互，比如AMS启动一个Activity的时候，会调用该接口的scheduleLaunchActivity函数。
  
  ActivityThread通过成员变量mAppThread指向它的内部类ApplicationThread。Application从ApplicationThreadNativie中排成。
  
  Binder支持客户端监听服务端的死亡消息，服务器端自然在应用进程中，AMS需要监听应用进程的死亡通知。
  
  保存创建的进程记录：
   `final ProcessMap<ProcessRecord> mProcessNames
            = new ProcessMap<ProcessRecord>();`
 
  * ActivityManagerService.newProcessRecordLocked

  
```java
   final ProcessRecord newProcessRecordLocked(IApplicationThread thread,
            ApplicationInfo info, String customProcess) {
        String proc = customProcess != null ? customProcess : info.processName;
        //为该进程创建一个耗电量统计
        BatteryStatsImpl.Uid.Proc ps = null;
        BatteryStatsImpl stats = mBatteryStatsService.getActiveStatistics();
        synchronized (stats) {
            ps = stats.getProcessStatsLocked(info.uid, proc);
        }
        return new ProcessRecord(ps, thread, info, proc);
    }
```

* ProcessRecord.java 构造方法


```java
ProcessRecord(BatteryStatsImpl.Uid.Proc _batteryStats, IApplicationThread _thread,
            ApplicationInfo _info, String _processName) {
    batteryStats = _batteryStats;//用于电量统计
    info = _info;//保存ApplicationInfo
    processName = _processName;//保存进程名字
    pkgList.add(_info.packageName);//一个进程可以运行多个Package
    thread = _thread;//保存IApplicaitonThread。
    maxAdj = ActivityManagerService.EMPTY_APP_ADJ;
    hiddenAdj = ActivityManagerService.HIDDEN_APP_MIN_ADJ;
    curRawAdj = setRawAdj = -100;
    curAdj = setAdj = -100;
    persistent = false;//是否为常驻进程。
    removed = false;
    }
```

## installSystemProviders


```java
 public static final void installSystemProviders() {
    List providers;
    synchronized (mSelf) {
        //获取system进程对应的记录
        ProcessRecord app = mSelf.mProcessNames.get("system", Process.SYSTEM_UID);
        providers = mSelf.generateApplicationProvidersLocked(app);
        if (providers != null) {
            for (int i=providers.size()-1; i>=0; i--) {
                ProviderInfo pi = (ProviderInfo)providers.get(i);
                //过滤掉非系统的Provider
                if ((pi.applicationInfo.flags&ApplicationInfo.FLAG_SYSTEM) == 0) {
                    Slog.w(TAG, "Not installing system proc provider " + pi.name
                            + ": not system .apk");
                    providers.remove(i);
                }
            }
        }
    }
    if (providers != null) {
        //在ActivityThread中安装Providers。
        mSystemThread.installSystemProviders(providers);
    }
}
```


* generateApplicationProvidersLocked


```java
private final List generateApplicationProvidersLocked(ProcessRecord app) {
    List providers = null;
    try {
        //向PKMS查询满足要求的ProviderInfo，最主要的查询条件包括进程名和进程uid。
        providers = AppGlobals.getPackageManager().
            queryContentProviders(app.processName, app.info.uid,
                    STOCK_PM_FLAGS | PackageManager.GET_URI_PERMISSION_PATTERNS);
    } catch (RemoteException ex) {
    }
    if (providers != null) {
        final int N = providers.size();
        for (int i=0; i<N; i++) {
            ProviderInfo cpi =
                (ProviderInfo)providers.get(i);
            ContentProviderRecord cpr = mProvidersByClass.get(cpi.name);
            if (cpr == null) {
                cpr = new ContentProviderRecord(cpi, app.info);
                mProvidersByClass.put(cpi.name, cpr);
            }
            //将信息保存到ProcessRecord
            app.pubProviders.put(cpi.name, cpr);
            //添加包名
            app.addPackage(cpi.applicationInfo.packageName);
            //对该APK进程dex优化。
            ensurePackageDexOpt(cpi.applicationInfo.packageName);
        }
    }
    return providers;
}
```

* PackageManagerService.queryContentProvides


```java

 public List<ProviderInfo> queryContentProviders(String processName,
            int uid, int flags) {
    ArrayList<ProviderInfo> finalList = null;

    synchronized (mPackages) {
        Iterator<PackageParser.Provider> i = mProvidersByComponent.values().iterator();
        while (i.hasNext()) {
            PackageParser.Provider p = i.next();
            if (p.info.authority != null
                && (processName == null ||
                        (p.info.processName.equals(processName)
                                && p.info.applicationInfo.uid == uid))
                && mSettings.isEnabledLP(p.info, flags)
                && (!mSafeMode || (p.info.applicationInfo.flags
                        &ApplicationInfo.FLAG_SYSTEM) != 0)) {
                if (finalList == null) {
                    finalList = new ArrayList<ProviderInfo>(3);
                }
                finalList.add(PackageParser.generateProviderInfo(p,
                        flags));
            }
        }
    }

    if (finalList != null) {
        Collections.sort(finalList, mProviderInitOrderSorter);
    }

    return finalList;
}
```

* ActivityThread.installSystemConproviders()

安装ContentProvider分为两步：
1、调用installProvider得到一个ContentProvider
2、向AMS发布这个ContentProvider实例。

```java

  public final void installSystemProviders(List providers) {
    if (providers != null) {
        //mInitialApplication 系统Applicaiton
        installContentProviders(mInitialApplication,
                                (List<ProviderInfo>)providers);
    }
}

//安装一个ContentProvder。
 private final void installContentProviders(
            Context context, List<ProviderInfo> providers) {
    final ArrayList<IActivityManager.ContentProviderHolder> results =
        new ArrayList<IActivityManager.ContentProviderHolder>();

    Iterator<ProviderInfo> i = providers.iterator();
    while (i.hasNext()) {
        ProviderInfo cpi = i.next();
        StringBuilder buf = new StringBuilder(128);
        buf.append("Pub ");
        buf.append(cpi.authority);
        buf.append(": ");
        buf.append(cpi.name);
        Log.i(TAG, buf.toString());
        //得到IContentProvider对象
        IContentProvider cp = installProvider(context, null, cpi, false);
        if (cp != null) {
            IActivityManager.ContentProviderHolder cph =
                new IActivityManager.ContentProviderHolder(cpi);
            cph.provider = cp;
            results.add(cph);
            // 使用ProviderRefCountMap对ContentProvider进行引用计数控制。
            synchronized(mProviderMap) {
                mProviderRefCountMap.put(cp.asBinder(), new ProviderRefCount(10000));
            }
        }
    }

    try {
        ActivityManagerNative.getDefault().publishContentProviders(
            getApplicationThread(), results);
    } catch (RemoteException ex) {
    }
}

```

* ActivityThread.installProviders


```java
    private final IContentProvider installProvider(Context context,
            IContentProvider provider, ProviderInfo info, boolean noisy) {
    ContentProvider localProvider = null;
    if (provider == null) {
        if (noisy) {
            Slog.d(TAG, "Loading provider " + info.authority + ": "
                    + info.name);
        }
        Context c = null;
        ApplicationInfo ai = info.applicationInfo;
        if (context.getPackageName().equals(ai.packageName)) {
            c = context;
        } else if (mInitialApplication != null &&
                mInitialApplication.getPackageName().equals(ai.packageName)) {
            c = mInitialApplication;
        } else {
            try {
                c = context.createPackageContext(ai.packageName,
                        Context.CONTEXT_INCLUDE_CODE);
            } catch (PackageManager.NameNotFoundException e) {
            }
        }
        if (c == null) {
            Slog.w(TAG, "Unable to get context for package " +
                  ai.packageName +
                  " while loading content provider " +
                  info.name);
            return null;
        }
        try {
            //通过反射机制生成ContentProvider实例。
            final java.lang.ClassLoader cl = c.getClassLoader();
            localProvider = (ContentProvider)cl.
                loadClass(info.name).newInstance();
            provider = localProvider.getIContentProvider();
            if (provider == null) {
                Slog.e(TAG, "Failed to instantiate class " +
                      info.name + " from sourceDir " +
                      info.applicationInfo.sourceDir);
                return null;
            }
            if (Config.LOGV) Slog.v(
                TAG, "Instantiating local provider " + info.name);
            // XXX Need to create the correct context for this provider.
            localProvider.attachInfo(c, info);
        } catch (java.lang.Exception e) {
            if (!mInstrumentation.onException(null, e)) {
                throw new RuntimeException(
                        "Unable to get provider " + info.name
                        + ": " + e.toString(), e);
            }
            return null;
        }
    } else if (localLOGV) {
        Slog.v(TAG, "Installing external provider " + info.authority + ": "
                + info.name);
    }

    synchronized (mProviderMap) {
        // Cache the pointer for the remote provider.
        String names[] = PATTERN_SEMICOLON.split(info.authority);
        for (int i=0; i<names.length; i++) {
            ProviderClientRecord pr = new ProviderClientRecord(names[i], provider,
                    localProvider);
            try {
                provider.asBinder().linkToDeath(pr, 0);
                mProviderMap.put(names[i], pr);
            } catch (RemoteException e) {
                return null;
            }
        }
        if (localProvider != null) {
            mLocalProviders.put(provider.asBinder(),
                    new ProviderClientRecord(null, provider, localProvider));
        }
    }

    return provider;
}
```

## AMS的SystemReady


```java
 public void systemReady(final Runnable goingCallback) {
    // In the simulator, startRunning will never have been called, which
    // normally sets a few crucial variables. Do it here instead.
    if (!Process.supportsProcesses()) {
        mStartRunning = true;
        mTopAction = Intent.ACTION_MAIN;
    }

    synchronized(this) {
        if (mSystemReady) {
            if (goingCallback != null) goingCallback.run();
            return;
        }
        
        // 判断是否为升级操作
        if (!mDidUpdate) {
            if (mWaitingUpdate) {//升级未完成，直接返回
                return;
            }
            //准备PRE_BOOT_COMPLITED广播
            Intent intent = new Intent(Intent.ACTION_PRE_BOOT_COMPLETED);
            //向PMS中查询该广播的接收者。
            List<ResolveInfo> ris = null;
            try {
                ris = AppGlobals.getPackageManager().queryIntentReceivers(
                            intent, null, 0);
            } catch (RemoteException e) {
            }
            if (ris != null) {
                //删除非系统广播接收者。
                for (int i=ris.size()-1; i>=0; i--) {
                    if ((ris.get(i).activityInfo.applicationInfo.flags
                            &ApplicationInfo.FLAG_SYSTEM) == 0) {
                        ris.remove(i);
                    }
                }
                intent.addFlags(Intent.FLAG_RECEIVER_BOOT_UPGRADE);
                
                //获取上次启动时已经处理PRE_BOOT_COMPLITED广播的组件。
                ArrayList<ComponentName> lastDoneReceivers = readLastDonePreBootReceivers();
                
                final ArrayList<ComponentName> doneReceivers = new ArrayList<ComponentName>();
                //删除上次已经处理过该广播的对象。
                for (int i=0; i<ris.size(); i++) {
                    ActivityInfo ai = ris.get(i).activityInfo;
                    ComponentName comp = new ComponentName(ai.packageName, ai.name);
                    if (lastDoneReceivers.contains(comp)) {
                        ris.remove(i);
                        i--;
                    }
                }
                
                for (int i=0; i<ris.size(); i++) {
                    ActivityInfo ai = ris.get(i).activityInfo;
                    ComponentName comp = new ComponentName(ai.packageName, ai.name);
                    doneReceivers.add(comp);
                    intent.setComponent(comp);
                    IIntentReceiver finisher = null;
                    if (i == ris.size()-1) {
                        //为最后一个广播接收者，注册一个回调通知。
                        finisher = new IIntentReceiver.Stub() {
                            public void performReceive(Intent intent, int resultCode,
                                    String data, Bundle extras, boolean ordered,
                                    boolean sticky) {
                                // The raw IIntentReceiver interface is called
                                // with the AM lock held, so redispatch to
                                // execute our code without the lock.
                                mHandler.post(new Runnable() {
                                    public void run() {
                                        synchronized (ActivityManagerService.this) {
                                            mDidUpdate = true;
                                        }
           //保存已经处理过的广播接受者信息。                             writeLastDonePreBootReceivers(doneReceivers);
                                        systemReady(goingCallback);
                                    }
                                });
                            }
                        };
                    }
                    Slog.i(TAG, "Sending system update to: " + intent.getComponent());
                    //发送广播给指定的接收者。
                    broadcastIntentLocked(null, null, intent, null, finisher,
                            0, null, null, null, true, false, MY_PID, Process.SYSTEM_UID);
                    if (finisher != null) {
                        mWaitingUpdate = true;
                    }
                }
            }
            if (mWaitingUpdate) {
                return;
            }
            mDidUpdate = true;
        }
        
        mSystemReady = true;
        if (!mStartRunning) {
            return;
        }
    }
    //杀死那些在AMS还未启动完毕就启动应用进程。
    ArrayList<ProcessRecord> procsToKill = null;
    synchronized(mPidsSelfLocked) {
        for (int i=mPidsSelfLocked.size()-1; i>=0; i--) {
            ProcessRecord proc = mPidsSelfLocked.valueAt(i);
            if (!isAllowedWhileBooting(proc.info)){
                if (procsToKill == null) {
                    procsToKill = new ArrayList<ProcessRecord>();
                }
                procsToKill.add(proc);
            }
        }
    }
    
    synchronized(this) {
        if (procsToKill != null) {
            for (int i=procsToKill.size()-1; i>=0; i--) {
                ProcessRecord proc = procsToKill.get(i);
                Slog.i(TAG, "Removing system update proc: " + proc);
                removeProcessLocked(proc, true);
            }
        }
        
        // Now that we have cleaned up any update processes, we
        // are ready to start launching real processes and know that
        // we won't trample on them any more.
        mProcessesReady = true;
    }
    
    Slog.i(TAG, "System now ready");
    EventLog.writeEvent(EventLogTags.BOOT_PROGRESS_AMS_READY,
        SystemClock.uptimeMillis());

    synchronized(this) {
        // Make sure we have no pre-ready processes sitting around.
        
        if (mFactoryTest == SystemServer.FACTORY_TEST_LOW_LEVEL) {
            ResolveInfo ri = mContext.getPackageManager()
                    .resolveActivity(new Intent(Intent.ACTION_FACTORY_TEST),
                            STOCK_PM_FLAGS);
            CharSequence errorMsg = null;
            if (ri != null) {
                ActivityInfo ai = ri.activityInfo;
                ApplicationInfo app = ai.applicationInfo;
                if ((app.flags&ApplicationInfo.FLAG_SYSTEM) != 0) {
                    mTopAction = Intent.ACTION_FACTORY_TEST;
                    mTopData = null;
                    mTopComponent = new ComponentName(app.packageName,
                            ai.name);
                } else {
                    errorMsg = mContext.getResources().getText(
                            com.android.internal.R.string.factorytest_not_system);
                }
            } else {
                errorMsg = mContext.getResources().getText(
                        com.android.internal.R.string.factorytest_no_action);
            }
            if (errorMsg != null) {
                mTopAction = null;
                mTopData = null;
                mTopComponent = null;
                Message msg = Message.obtain();
                msg.what = SHOW_FACTORY_ERROR_MSG;
                msg.getData().putCharSequence("msg", errorMsg);
                mHandler.sendMessage(msg);
            }
        }
    }

    retrieveSettings();

    if (goingCallback != null) goingCallback.run();
    
    synchronized (this) {
        if (mFactoryTest != SystemServer.FACTORY_TEST_LOW_LEVEL) {
            try {
                //查询为persistent为1的applicationInfo
                List apps = AppGlobals.getPackageManager().
                    getPersistentApplications(STOCK_PM_FLAGS);
                if (apps != null) {
                    int N = apps.size();
                    int i;
                    for (i=0; i<N; i++) {
                        ApplicationInfo info
                            = (ApplicationInfo)apps.get(i);
                        if (info != null &&
                                !info.packageName.equals("android")) {
                            addAppLocked(info);//启动该Application所在的进程。
                        }
                    }
                }
            } catch (RemoteException ex) {
                // pm is in same process, this will never happen.
            }
        }

        // Start up initial activity.
        mBooting = true;
        
        try {
            if (AppGlobals.getPackageManager().hasSystemUidErrors()) {
                Message msg = Message.obtain();
                msg.what = SHOW_UID_ERROR_MSG;
                mHandler.sendMessage(msg);
            }
        } catch (RemoteException e) {
        }

		  //启动全系统第一个Activity。
        mMainStack.resumeTopActivityLocked(null);
    }
}

```
  

