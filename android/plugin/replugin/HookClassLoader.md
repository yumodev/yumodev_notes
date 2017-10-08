# ClassLoader Hook机制

## 宿主
### PMF.init


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

### PatchClassLoaderUtils.patch


```
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

            // 获取mPackageInfo.mClassLoader
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

##生成RePluginClassLoader

```
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

## loadClass


```
@Override
    protected Class<?> loadClass(String className, boolean resolve) throws ClassNotFoundException {
        //
        Class<?> c = null;
        c = PMF.loadClass(className, resolve);
        if (c != null) {
            return c;
        }
        //
        try {
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


