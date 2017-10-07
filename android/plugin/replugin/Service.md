# Service

## PluginContext.startService


```
@Override
    public ComponentName startService(Intent service) {
        if (mContextInjector != null) {
            mContextInjector.startServiceBefore(service);
        }

        if (mLoader.mPluginObj.mInfo.getFrameworkVersion() <= 2) {
            // 仅框架版本为3及以上的才支持
            return super.startService(service);
        }
        try {
            return PluginServiceClient.startService(this, service, true);
        } catch (PluginClientHelper.ShouldCallSystem e) {
            // 若打开插件出错，则直接走系统逻辑
            return super.startService(service);
        } finally {
            if (mContextInjector != null) {
                mContextInjector.startServiceAfter(service);
            }
        }
    }
```

## com.qihoo360.replugin.component.service.PluginServiceClient


```
 // @hide
    public static ComponentName startService(Context context, Intent intent, boolean throwOnFail) {

        // 从 Intent 中获取 ComponentName
        ComponentName cn = getServiceComponentFromIntent(context, intent);

        // 获取将要使用服务的进程ID，并在稍后获取PSS对象
        int process = getProcessByComponentName(cn);
        if (process == PROCESS_UNKNOWN) {
            // 有可能是不支持的插件，也有可能本意就是想调用Main工程的服务。则直接调用系统方法来做
            if (LOG) {
                LogDebug.d(PLUGIN_TAG, "PSS.startService(): Call SystemAPI: in=" + intent);
            }
            if (throwOnFail) {
                throw new PluginClientHelper.ShouldCallSystem();
            } else {
                return context.startService(intent);
            }
        }

        // 既然确认是插件，则将之前获取的插件信息填入
        intent.setComponent(cn);

        IPluginServiceServer pss = sServerFetcher.fetchByProcess(process);
        if (pss == null) {
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "psc.ss: pss n");
            }
            return null;
        }

        // 开启服务
        try {
            return pss.startService(intent, sClientMessenger);
        } catch (Throwable e) {
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "psc.ss: pss e", e);
            }
        }
        return null;
    }
```

