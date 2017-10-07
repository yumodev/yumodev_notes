## RePluginApplicaiton

```
 @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);

        RePluginConfig c = createConfig();
        if (c == null) {
            c = new RePluginConfig();
        }

        RePluginCallbacks cb = createCallbacks();
        if (cb != null) {
            c.setCallbacks(cb);
        }

        RePlugin.App.attachBaseContext(this, c);
    }
```

## RePlugin.App.attachBaseContext


```
 /**
         * （推荐）当Application的attachBaseContext调用时需调用此方法 <p>
         * 可自定义插件框架的行为。参见RePluginConfig类的说明
         *
         * @param app Application对象
         * @see Application#attachBaseContext(Context)
         * @see RePluginConfig
         * @since 1.2.0
         */
        public static void attachBaseContext(Application app, RePluginConfig config) {
            if (sAttached) {
                if (LogDebug.LOG) {
                    LogDebug.d(TAG, "attachBaseContext: Already called");
                }
                return;
            }

            RePluginInternal.init(app);
            sConfig = config;
            sConfig.initDefaults(app);

            IPC.init(app);

            // 打印当前内存占用情况
            // 只有开启“详细日志”才会输出，防止“消耗性能”
            if (LOG && RePlugin.getConfig().isPrintDetailLog()) {
                LogDebug.printMemoryStatus(LogDebug.TAG, "act=, init, flag=, Start, pn=, framework, func=, attachBaseContext, lib=, RePlugin");
            }

            // 初始化HostConfigHelper（通过反射HostConfig来实现）
            // NOTE 一定要在IPC类初始化之后才使用
            HostConfigHelper.init();

            // FIXME 此处需要优化掉
            AppVar.sAppContext = app;

            // Plugin Status Controller
            PluginStatusController.setAppContext(app);

            PMF.init(app);
            PMF.callAttach();

            sAttached = true;
        }
```

## PMF.init


```
 /**
     * @param application
     */
    public static final void init(Application application) {
        setApplicationContext(application);

        PluginManager.init(application);

        sPluginMgr = new PmBase(application);
        sPluginMgr.init();

        Factory.sPluginManager = PMF.getLocal();
        Factory2.sPLProxy = PMF.getInternal();

        PatchClassLoaderUtils.patch(application);
    }

```

## PmBase init()


```
 void init() {
        if (HostConfigHelper.PERSISTENT_ENABLE) {
            // （默认）“常驻进程”作为插件管理进程，则常驻进程作为Server，其余进程作为Client
            if (IPC.isPersistentProcess()) {
                // 初始化“Server”所做工作
                initForServer();
            } else {
                // 连接到Server
                initForClient();
            }
        } else {
            // “UI进程”作为插件管理进程（唯一进程），则UI进程既可以作为Server也可以作为Client
            if (IPC.isUIProcess()) {
                // 1. 尝试初始化Server所做工作，
                initForServer();

                // 2. 注册该进程信息到“插件管理进程”中
                // 注意：这里无需再做 initForClient，因为不需要再走一次Binder
                PMF.sPluginMgr.attach();

            } else {
                // 其它进程？直接连接到Server即可
                initForClient();
            }
        }

        // 最新快照
        PluginTable.initPlugins(mPlugins);

        // 输出
        if (LOG) {
            for (Plugin p : mPlugins.values()) {
                LogDebug.d(PLUGIN_TAG, "plugin: p=" + p.mInfo);
            }
        }
    }
```
## PmBase initClient()

```java
  /**
     * Client(UI进程)的初始化
     *
     */
    private final void initForClient() {
        if (LOG) {
            LogDebug.d(PLUGIN_TAG, "list plugins from persistent process");
        }

        // 1. 先尝试连接
        PluginProcessMain.connectToHostSvc();

        // 2. 然后从常驻进程获取插件列表
        refreshPluginsFromHostSvc();
    }

```

##com.qihoo360.loader2.PluginProcessMain.connectToHostSvc()


```java
/**
     * 非常驻进程调用，获取常驻进程的 IPluginHost
     */
    static final void connectToHostSvc() {
        Context context = PMF.getApplicationContext();

        //
        IBinder binder = PluginProviderStub.proxyFetchHostBinder(context);
        if (LOG) {
            LogDebug.d(PLUGIN_TAG, "host binder = " + binder);
        }
        if (binder == null) {
            // 无法连接到常驻进程，当前进程自杀
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "p.p fhb fail");
            }
            System.exit(1);
        }

        //
        try {
            binder.linkToDeath(new IBinder.DeathRecipient() {

                @Override
                public void binderDied() {
                    if (LOGR) {
                        LogRelease.i(PLUGIN_TAG, "p.p d, p.h s n");
                    }
                    // 检测到常驻进程退出，插件进程自杀
                    if (PluginManager.isPluginProcess()) {
                        if (LOGR) {
                            // persistent process exception, PLUGIN process quit now
                            LogRelease.i(MAIN_TAG, "p p e, pp q n");
                        }
                        System.exit(0);
                    }
                    sPluginHostRemote = null;

                    // 断开和插件化管理器服务端的连接，因为已经失效
                    PluginManagerProxy.disconnect();
                }
            }, 0);
        } catch (RemoteException e) {
            // 无法连接到常驻进程，当前进程自杀
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "p.p p.h l2a: " + e.getMessage(), e);
            }
            System.exit(1);
        }

        //
        sPluginHostRemote = IPluginHost.Stub.asInterface(binder);
        if (LOG) {
            LogDebug.d(PLUGIN_TAG, "host binder.i = " + PluginProcessMain.sPluginHostRemote);
        }

        // 连接到插件化管理器的服务端
        // Added by Jiongxuan Zhang
        try {
            PluginManagerProxy.connectToServer(sPluginHostRemote);

            // 将当前进程的"正在运行"列表和常驻做同步
            // TODO 若常驻进程重启，则应在启动时发送广播，各存活着的进程调用该方法来同步
            PluginManagerProxy.syncRunningPlugins();
        } catch (RemoteException e) {
            // 获取PluginManagerServer时出现问题，可能常驻进程突然挂掉等，当前进程自杀
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "p.p p.h l3a: " + e.getMessage(), e);
            }
            System.exit(1);
        }

        // 注册该进程信息到“插件管理进程”中
        PMF.sPluginMgr.attach();
    }
```

##com.qihoo360.loader2.PluginProviderStub.proxyFetchHostBinder


```java
  private static final IBinder proxyFetchHostBinder(Context context, String selection) {
        //
        Cursor cursor = null;
        try {
            Uri uri = ProcessPitProviderPersist.URI;
            cursor = context.getContentResolver().query(uri, PROJECTION_MAIN, selection, null, null);
            if (cursor == null) {
                if (LOG) {
                    LogDebug.d(PLUGIN_TAG, "proxy fetch binder: cursor is null");
                }
                return null;
            }
            while (cursor.moveToNext()) {
                //
            }
            IBinder binder = BinderCursor.getBinder(cursor);
            if (LOG) {
                LogDebug.d(PLUGIN_TAG, "proxy fetch binder: binder=" + binder);
            }
            return binder;
        } finally {
            CloseableUtils.closeQuietly(cursor);
        }
    }
```

##com.qihoo360.replugin.component.process.ProcessPitProviderPersist


```
 @Override
    public Cursor query(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
        sInvoked = true;
        return PluginProviderStub.stubMain(uri, projection, selection, selectionArgs, sortOrder);
    }
```

##com.qihoo360.loader2.PluginProviderStub.stubMain

```
 public static final Cursor stubMain(Uri uri, String[] projection, String selection, String[] selectionArgs, String sortOrder) {
        //
        if (LOG) {
            LogDebug.d(PLUGIN_TAG, "stubMain projection=" + Arrays.toString(projection) + " selection=" + selection);
        }

        if (SELECTION_MAIN_BINDER.equals(selection)) {
            return BinderCursor.queryBinder(PMF.sPluginMgr.getHostBinder());
        }

        if (SELECTION_MAIN_PREF.equals(selection)) {
            // 需要枷锁否？
            initPref();
            return BinderCursor.queryBinder(sPrefImpl);
        }

        return null;
    }

```

## PmBase.getHostBinder()

```
final IBinder getHostBinder() {
        return mHostSvc;
    }
```

## PmBase.initForServer


```
 /**
     * Persistent(常驻)进程的初始化
     *
     */
    private final void initForServer() {
        if (LOG) {
            LogDebug.d(PLUGIN_TAG, "search plugins from file system");
        }

        mHostSvc = new PmHostSvc(mContext, this);
        PluginProcessMain.installHost(mHostSvc);
        PluginProcessMain.schedulePluginProcessLoop(PluginProcessMain.CHECK_STAGE1_DELAY);

        // 兼容即将废弃的p-n方案 by Jiongxuan Zhang
        mAll = new Builder.PxAll();
        Builder.builder(mContext, mAll);
        refreshPluginMap(mAll.getPlugins());

        // [Newest!] 使用全新的RePlugin APK方案
        // Added by Jiongxuan Zhang
        try {
            List<PluginInfo> l = PluginManagerProxy.load();
            if (l != null) {
                // 将"纯APK"插件信息并入总的插件信息表中，方便查询
                // 这里有可能会覆盖之前在p-n中加入的信息。本来我们就想这么干，以"纯APK"插件为准
                refreshPluginMap(l);
            }
        } catch (RemoteException e) {
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "lst.p: " + e.getMessage(), e);
            }
        }
    }
```

## com.qihoo360.replugin.packages.PluginManagerProxy.load


```
 /**
     * 调用常驻进程的Server端去加载插件列表，方便之后使用
     *
     * @return PluginInfo的列表
     * @throws RemoteException 和常驻进程通讯出现异常
     */
    public static List<PluginInfo> load() throws RemoteException {
        // 不判断sRemote在不在，因为本应该在sRemote获取后就马上调用
        return sRemote.load();
    }
    
   private static IPluginManagerServer sRemote;

/**
     * 连接到常驻进程，并缓存IPluginManagerServer对象
     *
     * @param host IPluginHost对象
     * @throws RemoteException 和常驻进程通讯出现异常
     */
    public static void connectToServer(IPluginHost host) throws RemoteException {
        if (sRemote != null) {
            if (LogDebug.LOG) {
                LogDebug.e(TAG, "connectToServer: Already connected! host=" + sRemote);
            }
            return;
        }

        sRemote = host.fetchManagerServer();
    }
```


### com.qihoo360.replugin.packages.PluginManagerServer.Stub.load()


```
@Override
        public List<PluginInfo> load() throws RemoteException {
            synchronized (LOCKER) {
                return PluginManagerServer.this.loadLocked();
            }
        }
        
         private List<PluginInfo> loadLocked() {
        if (!mList.load(mContext)) {
            return null;
        }

        // 执行“更新或删除Pending”插件，并返回结果
        return updateAllLocked();
    }
    
    private PluginInfoList mList = new PluginInfoList();

```

## com.qihoo360.replugin.model.PluginInfoList


```
public boolean load(Context context) {
         try {
            // 1. 新建或打开文件
            File d = context.getDir(Constant.LOCAL_PLUGIN_APK_SUB_DIR, 0);
            File f = new File(d, "p.l");
            if (!f.exists()) {
                // 不存在？直接创建一个新的即可
                if (!f.createNewFile()) {
                    if (LogDebug.LOG) {
                        LogDebug.e(TAG, "load: Create error!");
                    }
                    return false;
                } else {
                    if (LogDebug.LOG) {
                        LogDebug.i(TAG, "load: Create a new list file");
                    }
                    return true;
                }
            }

            // 2. 读出字符串
            String result = FileUtils.readFileToString(f, Charsets.UTF_8);
            if (TextUtils.isEmpty(result)) {
                if (LogDebug.LOG) {
                    LogDebug.e(TAG, "load: Read Json error!");
                }
                return false;
            }

            // 3. 解析出JSON
            mJson = new JSONArray(result);

        } catch (IOException e) {
            if (LogDebug.LOG) {
                LogDebug.e(TAG, "load: Load error!", e);
            }
            return false;
        } catch (JSONException e) {
            if (LogDebug.LOG) {
                LogDebug.e(TAG, "load: Parse Json Error!", e);
            }
            return false;
        }

        for (int i = 0; i < mJson.length(); i++) {
            JSONObject jo = mJson.optJSONObject(i);
            if (jo != null) {
                PluginInfo pi = PluginInfo.createByJO(jo);
                if (pi == null) {
                    if (LogDebug.LOG) {
                        LogDebug.e(TAG, "load: PluginInfo Invalid. Ignore! jo=" + jo);
                    }
                    continue;
                }
                addToMap(pi);
            }
        }
        return true;
    }

```

