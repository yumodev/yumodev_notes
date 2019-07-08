# Activity

## RePlugin.startActivity


```java
/**
 * 开启一个插件的Activity <p>
 * 其中Intent的ComponentName的Key应为插件名（而不是包名），可使用createIntent方法来创建Intent对象
 *
 * @param context Context对象
 * @param intent  要打开Activity的Intent，其中ComponentName的Key必须为插件名
 * @return 插件Activity是否被成功打开？
 * FIXME 是否需要Exception来做？
 * @see #createIntent(String, String)
 * @since 1.0.0
 */
public static boolean startActivity(Context context, Intent intent) {
    // TODO 先用旧的开启Activity方案，以后再优化
    ComponentName cn = intent.getComponent();
    if (cn == null) {
        // TODO 需要支持Action方案
        return false;
    }
    String plugin = cn.getPackageName();
    String cls = cn.getClassName();
    return Factory.startActivityWithNoInjectCN(context, intent, plugin, cls, IPluginManager.PROCESS_AUTO);
}
```

## com.qihoo360.loader2.PluginLibraryInternalProxy


```
 // FIXME 建议去掉plugin和activity参数，直接用intent代替
    /**
     * @hide 内部方法，插件框架使用
     * 启动一个插件中的activity，如果插件不存在会触发下载界面
     * @param context 应用上下文或者Activity上下文
     * @param intent
     * @param plugin 插件名
     * @param activity 待启动的activity类名
     * @param process 是否在指定进程中启动
     * @param download 下载
     * @return 插件机制层是否成功，例如没有插件存在、没有合适的Activity坑
     */
    public boolean startActivity(Context context, Intent intent, String plugin, String activity, int process, boolean download) {
        if (LOG) {
            LogDebug.d(PLUGIN_TAG, "start activity: intent=" + intent + " plugin=" + plugin + " activity=" + activity + " process=" + process + " download=" + download);
        }

        // 是否启动下载
        // 若插件不可用（不存在或版本不匹配），则直接弹出“下载插件”对话框
        // 因为已经打开UpdateActivity，故在这里返回True，告诉外界已经打开，无需处理
        if (download) {
            if (PluginTable.getPluginInfo(plugin) == null) {
                if (LOG) {
                    LogDebug.d(PLUGIN_TAG, "plugin=" + plugin + " not found, start download ...");
                }

                // 如果用户在下载即将完成时突然点按“取消”，则有可能出现插件已下载成功，但没有及时加载进来的情况
                // 因此我们会判断这种情况，如果是，则重新加载一次即可，反之则提示用户下载
                // 原因：“取消”会触发Task.release方法，最终调用mDownloadTask.destroy，导致“下载服务”的Receiver被注销，即使文件下载了也没有回调回来
                // NOTE isNeedToDownload方法会调用pluginDownloaded再次尝试加载
                if (isNeedToDownload(context, plugin)) {
                    return RePlugin.getConfig().getCallbacks().onPluginNotExistsForActivity(context, plugin, intent, process);
                }
            }
        }

        /* 检查是否是动态注册的类 */
        // 如果要启动的 Activity 是动态注册的类，则不使用坑位机制，而是直接动态类。
        // 原因：宿主的某些动态注册的类不能运行在坑位中（如'桌面'插件的入口Activity）
        if (LOG) {
            LogDebug.d("loadClass", "isHookingClass(" + plugin + " , " + activity + ") = "
                    + Factory2.isDynamicClass(plugin, activity));
        }

        if (Factory2.isDynamicClass(plugin, activity)) {
            intent.putExtra(IPluginManager.KEY_COMPATIBLE, true);
            intent.setComponent(new ComponentName(IPC.getPackageName(), activity));
            context.startActivity(intent);
            return true;
        }

        // 如果插件状态出现问题，则每次弹此插件的Activity都应提示无法使用，或提示升级（如有新版）
        // Added by Jiongxuan Zhang
        if (PluginStatusController.getStatus(plugin) < PluginStatusController.STATUS_OK) {
            if (LOG) {
                LogDebug.d(PLUGIN_TAG, "PluginLibraryInternalProxy.startActivity(): Plugin Disabled. pn=" + plugin);
            }
            return RePlugin.getConfig().getCallbacks().onPluginNotExistsForActivity(context, plugin, intent, process);
        }

        // 若为首次加载插件，且是“大插件”，则应异步加载，同时弹窗提示“加载中”
        // Added by Jiongxuan Zhang
        if (!RePlugin.isPluginDexExtracted(plugin)) {
            PluginDesc pd = PluginDesc.get(plugin);
            if (pd != null && pd.isLarge()) {
                if (LOG) {
                    LogDebug.d(PLUGIN_TAG, "PM.startActivity(): Large Plugin! p=" + plugin);
                }
                return RePlugin.getConfig().getCallbacks().onLoadLargePluginForActivity(context, plugin, intent, process);
            }
        }

        // WARNING：千万不要修改intent内容，尤其不要修改其ComponentName
        // 因为一旦分配坑位有误（或压根不是插件Activity），则外界还需要原封不动的startActivity到系统中
        // 可防止出现“本来要打开宿主，结果被改成插件”，进而无法打开宿主Activity的问题

        // 缓存打开前的Intent对象，里面将包括Action等内容
        Intent from = new Intent(intent);

        // 帮助填写打开前的Intent的ComponentName信息（如有。没有的情况如直接通过Action打开等）
        if (!TextUtils.isEmpty(plugin) && !TextUtils.isEmpty(activity)) {
            from.setComponent(new ComponentName(plugin, activity));
        }

        ComponentName cn = mPluginMgr.mLocal.loadPluginActivity(intent, plugin, activity, process);
        if (cn == null) {
            if (LOG) {
                LogDebug.d(PLUGIN_TAG, "plugin cn not found: intent=" + intent + " plugin=" + plugin + " activity=" + activity + " process=" + process);
            }
            return false;
        }

        // 将Intent指向到“坑位”。这样：
        // from：插件原Intent
        // to：坑位Intent
        intent.setComponent(cn);

        if (LOG) {
            LogDebug.d(PLUGIN_TAG, "start activity: real intent=" + intent);
        }

//        if (RePluginInternal.FOR_DEV) {
//            try {
//                String str = cn.getPackageName() + "/" + cn.getClassName();
//                if (LOG) {
//                    LogDebug.d(PLUGIN_TAG, "str=" + str);
//                }
//                new ProcessBuilder().command("am", "start", "-D", "--user", "0", "-n", str).start();
//            } catch (IOException e) {
//                e.printStackTrace();
//            }
//        } else {

        context.startActivity(intent);

        // 通知外界，已准备好要打开Activity了
        // 其中：from为要打开的插件的Intent，to为坑位Intent
        RePlugin.getConfig().getEventCallbacks().onPrepareStartPitActivity(context, from, intent);

        return true;
    }
```

## 寻找坑位-com.qihoo360.loader2.PluginCommImpl


```
 /**
     * 加载插件Activity，在startActivity之前调用
     * @param intent
     * @param plugin 插件名
     * @param target 目标Service名，如果传null，则取获取到的第一个
     * @param process 是否在指定进程中启动
     * @return
     */
    public ComponentName loadPluginActivity(Intent intent, String plugin, String activity, int process) {

        ActivityInfo ai = null;
        String container = null;
        PluginBinderInfo info = new PluginBinderInfo(PluginBinderInfo.ACTIVITY_REQUEST);

        try {
            // 获取 ActivityInfo(可能是其它插件的 Activity，所以这里使用 pair 将 pluginName 也返回)
            ai = getActivityInfo(plugin, activity, intent);
            if (ai == null) {
                if (LOG) {
                    LogDebug.d(PLUGIN_TAG, "PACM: bindActivity: activity not found");
                }
                return null;
            }

            // 存储此 Activity 在插件 Manifest 中声明主题到 Intent
            intent.putExtra(INTENT_KEY_THEME_ID, ai.theme);
            if (LOG) {
                LogDebug.d("theme", String.format("intent.putExtra(%s, %s);", ai.name, ai.theme));
            }

            // 根据 activity 的 processName，选择进程 ID 标识
            if (ai.processName != null) {
                process = PluginClientHelper.getProcessInt(ai.processName);
            }

            // 容器选择（启动目标进程）
            IPluginClient client = MP.startPluginProcess(plugin, process, info);
            if (client == null) {
                return null;
            }

            // 远程分配坑位
            container = client.allocActivityContainer(plugin, process, ai.name, intent);
            if (LOG) {
                LogDebug.i(PLUGIN_TAG, "alloc success: container=" + container + " plugin=" + plugin + " activity=" + activity);
            }
        } catch (Throwable e) {
            if (LOGR) {
                LogRelease.e(PLUGIN_TAG, "l.p.a spp|aac: " + e.getMessage(), e);
            }
        }

        // 分配失败
        if (TextUtils.isEmpty(container)) {
            return null;
        }

        PmBase.cleanIntentPluginParams(intent);

        // TODO 是否重复
        // 附上额外数据，进行校验
//        intent.putExtra(PluginManager.EXTRA_PLUGIN, plugin);
//        intent.putExtra(PluginManager.EXTRA_ACTIVITY, activity);
//        intent.putExtra(PluginManager.EXTRA_PROCESS, process);
//        intent.putExtra(PluginManager.EXTRA_CONTAINER, container);

        PluginIntent ii = new PluginIntent(intent);
        ii.setPlugin(plugin);
        ii.setActivity(ai.name);
        ii.setProcess(IPluginManager.PROCESS_AUTO);
        ii.setContainer(container);
        ii.setCounter(0);
        return new ComponentName(IPC.getPackageName(), container);
    }
```


