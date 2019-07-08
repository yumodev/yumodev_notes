[toc]
# ClassLoader Hook机制

## 宿主

宿主定义了RePluginClassLoader类，其继承在PathClassLoader。

RePluginClassLoader类在RePlugin中初始化的过程中创建，我们可以自定义自己的PathClassLoaderder实现类

### RePlugin.attachBaseContext

在RePlugin启动中调用。

```java
public static void attachBaseContext(Application app, RePluginConfig config) {
   ...

    PMF.init(app);
    ...
}
```
### PMF.init

调用PatchClassLoaderUtils.patch(application);
```java
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

### PatchClassLoaderUtils.patch


```java
public static boolean patch(Application application) {
    try {
        // 获取Application的BaseContext （来自ContextWrapper）
        Context oBase = application.getBaseContext();
        if (oBase == null) {
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "pclu.p: nf mb. ap cl=" + application.getClass());
            }
            return false;
        }

        // 获取mBase.mPackageInfo
        // 1. ApplicationContext - Android 2.1
        // 2. ContextImpl - Android 2.2 and higher
        // 3. AppContextImpl - Android 2.2 and higher
        Object oPackageInfo = ReflectUtils.readField(oBase, "mPackageInfo");
        if (oPackageInfo == null) {
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "pclu.p: nf mpi. mb cl=" + oBase.getClass());
            }
            return false;
        }

        // mPackageInfo的类型主要有两种：
        // 1. android.app.ActivityThread$PackageInfo - Android 2.1 - 2.3
        // 2. android.app.LoadedApk - Android 2.3.3 and higher
        if (LOG) {
            Log.d(TAG, "patch: mBase cl=" + oBase.getClass() + "; mPackageInfo cl=" + oPackageInfo.getClass());
        }

        // 获取mPackageInfo.mClassLoader原有的PathClassLoader。
        ClassLoader oClassLoader = (ClassLoader) ReflectUtils.readField(oPackageInfo, "mClassLoader");
        if (oClassLoader == null) {
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "pclu.p: nf mpi. mb cl=" + oBase.getClass() + "; mpi cl=" + oPackageInfo.getClass());
            }
            return false;
        }

        // 外界可自定义ClassLoader的实现，但一定要基于RePluginClassLoader类
        ClassLoader cl = RePlugin.getConfig().getCallbacks().createClassLoader(oClassLoader.getParent(), oClassLoader);

        // 将新的ClassLoader写入mPackageInfo.mClassLoader
        ReflectUtils.writeField(oPackageInfo, "mClassLoader", cl);

        // 设置线程上下文中的ClassLoader为RePluginClassLoader
        // 防止在个别Java库用到了Thread.currentThread().getContextClassLoader()时，“用了原来的PathClassLoader”，或为空指针
        Thread.currentThread().setContextClassLoader(cl);

        if (LOG) {
            Log.d(TAG, "patch: patch mClassLoader ok");
        }
    } catch (Throwable e) {
        e.printStackTrace();
        return false;
    }
    return true;
}
```

###生成RePluginClassLoader

```java
public RePluginClassLoader(ClassLoader parent, ClassLoader orig) {

    // 由于PathClassLoader在初始化时会做一些Dir的处理，所以这里必须要传一些内容进来
    // 但我们最终不用它，而是拷贝所有的Fields
    super("", "", parent);
    mOrig = orig;

    // 将原来宿主里的关键字段，拷贝到这个对象上，这样骗系统以为用的还是以前的东西（尤其是DexPathList）
    // 注意，这里用的是“浅拷贝”
    // Added by Jiongxuan Zhang
    copyFromOriginal(orig);

    initMethods(orig);
}

```

### RePluginClassLoaderloadClass


```java
@Override
protected Class<?> loadClass(String className, boolean resolve) throws ClassNotFoundException {
    //
    Class<?> c = null;
    //在PMF.loadClass中完成类的替换
    c = PMF.loadClass(className, resolve);
    if (c != null) {
        //找到了直接返回。
        return c;
    }
    //
    try {
        //从原来的ClassLoader中加载文件。
        c = mOrig.loadClass(className);
        // 只有开启“详细日志”才会输出，防止“刷屏”现象
        if (LogDebug.LOG && RePlugin.getConfig().isPrintDetailLog()) {
            LogDebug.d(TAG, "loadClass: load other class, cn=" + className);
        }
        return c;
    } catch (Throwable e) {
        //
    }
    //
    return super.loadClass(className, resolve);
}

```

### PMF.loadClass


```java
/**
 * @param className
 * @param resolve
 * @return
 */
public static final Class<?> loadClass(String className, boolean resolve) {
    return sPluginMgr.loadClass(className, resolve);
}
```

### PmBase.loadClass


```java
/**
 * @param className
 * @param resolve
 * @return
 */
final Class<?> loadClass(String className, boolean resolve) {
    // 加载Service中介坑位
    if (className.startsWith(PluginPitService.class.getName())) {
        if (LOG) {
            LogDebug.i(TAG, "loadClass: Loading PitService Class... clz=" + className);
        }
        return PluginPitService.class;
    }

    //
    if (mContainerActivities.contains(className)) {
        //找目标坑位启动
        Class<?> c = mClient.resolveActivityClass(className);
        if (c != null) {
            return c;
        }
        // 输出warn日志便于查看
        // use DummyActivity orig=
        if (LOGR) {
            LogRelease.w(PLUGIN_TAG, "p m hlc u d a o " + className);
        }
        //找不到插件Activity返回的一个伪造Activity，打开后直接退出。
        return DummyActivity.class;
    }

    //
    if (mContainerServices.contains(className)) {
        Class<?> c = loadServiceClass(className);
        if (c != null) {
            return c;
        }
        // 输出warn日志便于查看
        // use DummyService orig=
        if (LOGR) {
            LogRelease.w(PLUGIN_TAG, "p m hlc u d s o " + className);
        }
        return DummyService.class;
    }

    //
    if (mContainerProviders.contains(className)) {
        Class<?> c = loadProviderClass(className);
        if (c != null) {
            return c;
        }
        // 输出warn日志便于查看
        // use DummyProvider orig=
        if (LOGR) {
            LogRelease.w(PLUGIN_TAG, "p m hlc u d p o " + className);
        }
        return DummyProvider.class;
    }

    // 插件定制表
    DynamicClass dc = mDynamicClasses.get(className);
    if (dc != null) {
        final Context context = RePluginInternal.getAppContext();
        PluginDesc desc = PluginDesc.get(dc.plugin);

        if (LOG) {
            LogDebug.d("loadClass", "desc=" + desc);
            if (desc != null) {
                LogDebug.d("loadClass", "desc.isLarge()=" + desc.isLarge());
            }
            LogDebug.d("loadClass", "RePlugin.isPluginDexExtracted(" + dc.plugin + ") = " + RePlugin.isPluginDexExtracted(dc.plugin));
        }

        // 加载动态类时，如果其对应的插件未下载，则转到代理类
        if (desc != null) {
            String plugin = desc.getPluginName();
            if (PluginTable.getPluginInfo(plugin) == null) {
                if (LOG) {
                    LogDebug.d("loadClass", "plugin=" + plugin + " not found, return DynamicClassProxyActivity.class");
                }
                return DynamicClassProxyActivity.class;
            }
        }

        /* 加载未安装的大插件时，启动一个过度 Activity */
        // todo fixme 仅对 activity 类型才弹窗
        boolean needStartLoadingActivity = (desc != null && desc.isLarge() && !RePlugin.isPluginDexExtracted(dc.plugin));
        if (LOG) {
            LogDebug.d("loadClass", "needStartLoadingActivity = " + needStartLoadingActivity);
        }
        if (needStartLoadingActivity) {
            Intent intent = new Intent();
            intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            // fixme 将 PluginLoadingActivity2 移到 replugin 中来，不写死
            intent.setComponent(new ComponentName(IPC.getPackageName(), "com.qihoo360.loader2.updater.PluginLoadingActivity2"));
            context.startActivity(intent);
        }

        Plugin p = loadAppPlugin(dc.plugin);
        if (LOG) {
            LogDebug.d("loadClass", "p=" + p);
        }
        if (p != null) {
            try {
                Class<?> cls = p.getClassLoader().loadClass(dc.className);
                if (needStartLoadingActivity) {
                    // 发广播给过度 Activity，让其关闭
                    // fixme 发送给 UI 进程
                    Tasks.postDelayed2Thread(new Runnable() {
                        @Override
                        public void run() {
                            if (LOG) {
                                LogDebug.d("loadClass", "发广播，让 PluginLoadingActivity2 消失");
                            }
                            IPC.sendLocalBroadcast2All(context, new Intent("com.qihoo360.replugin.load_large_plugin.dismiss_dlg"));
                        }
                    }, 300);
                    // IPC.sendLocalBroadcast2Process(context, IPC.getPersistentProcessName(), new Intent("com.qihoo360.replugin.load_large_plugin.dismiss_dlg"), )
                }
                return cls;
            } catch (Throwable e) {
                if (LOGR) {
                    LogRelease.w(PLUGIN_TAG, "p m hlc dc " + className, e);
                }
            }
        } else {
            if (LOG) {
                LogDebug.d("loadClass", "加载 " + dc.plugin + " 失败");
            }
            Tasks.postDelayed2Thread(new Runnable() {
                @Override
                public void run() {
                    IPC.sendLocalBroadcast2All(context, new Intent("com.qihoo360.replugin.load_large_plugin.dismiss_dlg"));
                }
            }, 300);
        }
        if (LOGR) {
            LogRelease.w(PLUGIN_TAG, "p m hlc dc failed: " + className + " t=" + dc.className + " tp=" + dc.classType + " df=" + dc.defClass);
        }
        // return dummy class
        if ("activity".equals(dc.classType)) {
            return DummyActivity.class;
        } else if ("service".equals(dc.classType)) {
            return DummyService.class;
        } else if ("provider".equals(dc.classType)) {
            return DummyProvider.class;
        }
        return dc.defClass;
    }

    //
    return loadDefaultClass(className);
}

```

### PmBase.loadDefaultClass



```java
  /**
 * @param className
 * @return
 */
private final Class<?> loadDefaultClass(String className) {
    //
    Plugin p = mDefaultPlugin;
    if (p == null) {
        if (PluginManager.isPluginProcess()) {
            if (LOG) {
                LogDebug.d(PLUGIN_TAG, "plugin class loader: not found default plugin,  in=" + className);
            }
        }
        return null;
    }

    ClassLoader cl = p.getClassLoader();
    if (LOG) {
        LogDebug.d(PLUGIN_TAG, "plugin class loader: in=" + className);
    }
    Class<?> c = null;
    try {
        c = cl.loadClass(className);
    } catch (Throwable e) {
        if (LOG) {
            if (e != null && e.getCause() instanceof ClassNotFoundException) {
                if (LOG) {
                    LogDebug.d(PLUGIN_TAG, "plugin classloader not found className=" + className);
                }
            } else {
                if (LOG) {
                    LogDebug.d(PLUGIN_TAG, e.getMessage(), e);
                }
            }
        }
    }
    if (LOG) {
        LogDebug.d(PLUGIN_TAG, "plugin class loader: c=" + c + ", loader=" + cl);
    }
    return c;
}
```


