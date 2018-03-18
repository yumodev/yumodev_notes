# 从Am打开一个Activity的流程

## Am.runStart()

调用ActivityManagerServer的startActivityAndWait方法。

```java

   private void runStart() throws Exception {
    Intent intent = makeIntent();
    System.out.println("Starting: " + intent);
    intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
    // XXX should do something to determine the MIME type.
    IActivityManager.WaitResult result = null;
    int res;
    if (mWaitOption) {
        result = mAm.startActivityAndWait(null, intent, intent.getType(),
                    null, 0, null, null, 0, false, mDebugOption);
        res = result.result;
    } else {
        res = mAm.startActivity(null, intent, intent.getType(),
                null, 0, null, null, 0, false, mDebugOption);
    }
    PrintStream out = mWaitOption ? System.out : System.err;
    boolean launched = false;
    switch (res) {
        case IActivityManager.START_SUCCESS:
            launched = true;
            break;
        case IActivityManager.START_SWITCHES_CANCELED:
            launched = true;
            out.println(
                    "Warning: Activity not started because the "
                    + " current activity is being kept for the user.");
            break;
        case IActivityManager.START_DELIVERED_TO_TOP:
            launched = true;
            out.println(
                    "Warning: Activity not started, intent has "
                    + "been delivered to currently running "
                    + "top-most instance.");
            break;
        case IActivityManager.START_RETURN_INTENT_TO_CALLER:
            launched = true;
            out.println(
                    "Warning: Activity not started because intent "
                    + "should be handled by the caller");
            break;
        case IActivityManager.START_TASK_TO_FRONT:
            launched = true;
            out.println(
                    "Warning: Activity not started, its current "
                    + "task has been brought to the front");
            break;
        case IActivityManager.START_INTENT_NOT_RESOLVED:
            out.println(
                    "Error: Activity not started, unable to "
                    + "resolve " + intent.toString());
            break;
        case IActivityManager.START_CLASS_NOT_FOUND:
            out.println(NO_CLASS_ERROR_CODE);
            out.println("Error: Activity class " +
                    intent.getComponent().toShortString()
                    + " does not exist.");
            break;
        case IActivityManager.START_FORWARD_AND_REQUEST_CONFLICT:
            out.println(
                    "Error: Activity not started, you requested to "
                    + "both forward and receive its result");
            break;
        case IActivityManager.START_PERMISSION_DENIED:
            out.println(
                    "Error: Activity not started, you do not "
                    + "have permission to access it.");
            break;
        default:
            out.println(
                    "Error: Activity not started, unknown error code " + res);
            break;
    }
    if (mWaitOption && launched) {
        if (result == null) {
            result = new IActivityManager.WaitResult();
            result.who = intent.getComponent();
        }
        System.out.println("Status: " + (result.timeout ? "timeout" : "ok"));
        if (result.who != null) {
            System.out.println("Activity: " + result.who.flattenToShortString());
        }
        if (result.thisTime >= 0) {
            System.out.println("ThisTime: " + result.thisTime);
        }
        if (result.totalTime >= 0) {
            System.out.println("TotalTime: " + result.totalTime);
        }
        System.out.println("Complete");
    }
}

```

## Ams的startActivityAndWait函数分析
startActivityAndWait参数众多呀。

* IApplicationThread : 在绝大数情况下Activity的启动是由一个应用进程发起的。IApplication是应用进程和AMS交互的通道，也是调用进程的表示。如果是Am启动的activity，那么caller可以为空。
* Intent :描述Activity
* String reslvedType
* grantedUriPermissions:
* grantedMode
* IBinder resultTo
* String resultWho
* requestCode
* boolean onlyIfNeeded
* boolean debug


```java
  public final WaitResult startActivityAndWait(IApplicationThread caller,
            Intent intent, String resolvedType, Uri[] grantedUriPermissions,
            int grantedMode, IBinder resultTo,
            String resultWho, int requestCode, boolean onlyIfNeeded,
            boolean debug) {
    WaitResult res = new WaitResult();
    mMainStack.startActivityMayWait(caller, intent, resolvedType,
            grantedUriPermissions, grantedMode, resultTo, resultWho,
            requestCode, onlyIfNeeded, debug, res, null);
    return res;
}
```

## ActivityStack.startActivityMayWait


```java
  final int startActivityMayWait(IApplicationThread caller,
            Intent intent, String resolvedType, Uri[] grantedUriPermissions,
            int grantedMode, IBinder resultTo,
            String resultWho, int requestCode, boolean onlyIfNeeded,
            boolean debug, WaitResult outResult, Configuration config) {
    // Refuse possible leaked file descriptors
    if (intent != null && intent.hasFileDescriptors()) {
        throw new IllegalArgumentException("File descriptors passed in Intent");
    }

    boolean componentSpecified = intent.getComponent() != null;
    
    // Don't modify the client's object! 创建一个新的Intent，防止客户端对象被修改。
    intent = new Intent(intent);

    // 获取响应的ActivityInfo信息
    ActivityInfo aInfo;
    try {
        ResolveInfo rInfo =
            AppGlobals.getPackageManager().resolveIntent(
                    intent, resolvedType,
                    PackageManager.MATCH_DEFAULT_ONLY
                    | ActivityManagerService.STOCK_PM_FLAGS);
        aInfo = rInfo != null ? rInfo.activityInfo : null;
    } catch (RemoteException e) {
        aInfo = null;
    }

    if (aInfo != null) {
        intent.setComponent(new ComponentName(
                aInfo.applicationInfo.packageName, aInfo.name));

        // Don't debug things in the system process
        if (debug) {
            if (!aInfo.processName.equals("system")) {
                mService.setDebugApp(aInfo.processName, true, false);
            }
        }
    }

    synchronized (mService) {
        int callingPid;
        int callingUid;
        if (caller == null) {
            callingPid = Binder.getCallingPid();
            callingUid = Binder.getCallingUid();
        } else {
            callingPid = callingUid = -1;
        }
        
        mConfigWillChange = config != null
                && mService.mConfiguration.diff(config) != 0;
        if (DEBUG_CONFIGURATION) Slog.v(TAG,
                "Starting activity when config will change = " + mConfigWillChange);
        
        final long origId = Binder.clearCallingIdentity();
        
        if (mMainStack && aInfo != null &&
                (aInfo.applicationInfo.flags&ApplicationInfo.FLAG_CANT_SAVE_STATE) != 0) {
          //AndroidManifest.xml 中的Application标签可以声明一个cantSaveState属性，设置该属性的Application将不享受系统提供的状态保存和恢复功能，但是目前该功能并未实现。   
          ......             
            }
        }
        
        //启动Activity，返回值保存到res中。
        int res = startActivityLocked(caller, intent, resolvedType,
                grantedUriPermissions, grantedMode, aInfo,
                resultTo, resultWho, requestCode, callingPid, callingUid,
                onlyIfNeeded, componentSpecified);
        //configuration发生变化，则调用AMS的updateconfigurationLocked
        
        if (mConfigWillChange && mMainStack) {
           mService.enforceCallingPermission(android.Manifest.permission.CHANGE_CONFIGURATION,
                    "updateConfiguration()");
            mConfigWillChange = false;
            if (DEBUG_CONFIGURATION) Slog.v(TAG,
                    "Updating to new configuration after starting activity.");
            mService.updateConfigurationLocked(config, null);
        }
        
        Binder.restoreCallingIdentity(origId);
        
        //设置启动结果
        if (outResult != null) {
          
            outResult.result = res;
            if (res == IActivityManager.START_SUCCESS) {
            //启动成功
                mWaitingActivityLaunched.add(outResult);
                do {
                    try {
                        mService.wait();
                    } catch (InterruptedException e) {
                    }
                } while (!outResult.timeout && outResult.who == null);
            } else if (res == IActivityManager.START_TASK_TO_FRONT) {
                ActivityRecord r = this.topRunningActivityLocked(null);
                if (r.nowVisible) {
                    outResult.timeout = false;
                    outResult.who = new ComponentName(r.info.packageName, r.info.name);
                    outResult.totalTime = 0;
                    outResult.thisTime = 0;
                } else {
                    outResult.thisTime = SystemClock.uptimeMillis();
                    mWaitingActivityVisible.add(outResult);
                    do {
                        try {
                            mService.wait();
                        } catch (InterruptedException e) {
                        }
                    } while (!outResult.timeout && outResult.who == null);
                }
            }
        }
        
        return res;
    }
}
```

## ActivityStack.startActivityLocked



```java
  final int startActivityLocked(IApplicationThread caller,
            Intent intent, String resolvedType,
            Uri[] grantedUriPermissions,
            int grantedMode, ActivityInfo aInfo, IBinder resultTo,
            String resultWho, int requestCode,
            int callingPid, int callingUid, boolean onlyIfNeeded,
            boolean componentSpecified) {

    int err = START_SUCCESS;

    ProcessRecord callerApp = null;
    if (caller != null) {
        //如果其caller不为空，则需要找出他的ProcessRecord
        callerApp = mService.getRecordForAppLocked(caller);
        if (callerApp != null) {
            //等到调用进程的pid和uid。
            callingPid = callerApp.pid;
            callingUid = callerApp.info.uid;
        } else {
            //非法的。
            err = START_PERMISSION_DENIED;
        }
    }

    if (err == START_SUCCESS) {
        Slog.i(TAG, "Starting: " + intent + " from pid "
                + (callerApp != null ? callerApp.pid : callingPid));
    }

    //用于描述启动目标的Activity的那个Activity。
    ActivityRecord sourceRecord = null;
    //用于描述接收启动结果的Activity。
    ActivityRecord resultRecord = null;
    if (resultTo != null) {
        //如果从am启动的，那么resultTo 为null；
        int index = indexOfTokenLocked(resultTo);
        if (DEBUG_RESULTS) Slog.v(
            TAG, "Sending result to " + resultTo + " (index " + index + ")");
        if (index >= 0) {
            sourceRecord = (ActivityRecord)mHistory.get(index);
            if (requestCode >= 0 && !sourceRecord.finishing) {
                resultRecord = sourceRecord;
            }
        }
    }

    int launchFlags = intent.getFlags();

    if ((launchFlags&Intent.FLAG_ACTIVITY_FORWARD_RESULT) != 0
            && sourceRecord != null) {
      
        if (requestCode >= 0) {
            return START_FORWARD_AND_REQUEST_CONFLICT;
        }
        resultRecord = sourceRecord.resultTo;
        resultWho = sourceRecord.resultWho;
        requestCode = sourceRecord.requestCode;
        sourceRecord.resultTo = null;
        if (resultRecord != null) {
            resultRecord.removeResultsLocked(
                sourceRecord, resultWho, requestCode);
        }
    }

    if (err == START_SUCCESS && intent.getComponent() == null) {
        err = START_INTENT_NOT_RESOLVED;
    }

    if (err == START_SUCCESS && aInfo == null) {
       
        err = START_CLASS_NOT_FOUND;
    }

    if (err != START_SUCCESS) {
        if (resultRecord != null) {
            sendActivityResultLocked(-1,
                resultRecord, resultWho, requestCode,
                Activity.RESULT_CANCELED, null);
        }
        return err;
    }

    final int perm = mService.checkComponentPermission(aInfo.permission, callingPid,
            callingUid, aInfo.exported ? -1 : aInfo.applicationInfo.uid);
    if (perm != PackageManager.PERMISSION_GRANTED) {
        if (resultRecord != null) {
            sendActivityResultLocked(-1,
                resultRecord, resultWho, requestCode,
                Activity.RESULT_CANCELED, null);
        }
        String msg = "Permission Denial: starting " + intent.toString()
                + " from " + callerApp + " (pid=" + callingPid
                + ", uid=" + callingUid + ")"
                + " requires " + aInfo.permission;
        Slog.w(TAG, msg);
        throw new SecurityException(msg);
    }

    if (mMainStack) {
        if (mService.mController != null) {
            boolean abort = false;
            try {
                // The Intent we give to the watcher has the extra data
                // stripped off, since it can contain private information.
                Intent watchIntent = intent.cloneFilter();
                abort = !mService.mController.activityStarting(watchIntent,
                        aInfo.applicationInfo.packageName);
            } catch (RemoteException e) {
                mService.mController = null;
            }
    
            if (abort) {
                if (resultRecord != null) {
                    sendActivityResultLocked(-1,
                        resultRecord, resultWho, requestCode,
                        Activity.RESULT_CANCELED, null);
                }
                // We pretend to the caller that it was really started, but
                // they will just get a cancel result.
                return START_SUCCESS;
            }
        }
    }
    
    ActivityRecord r = new ActivityRecord(mService, this, callerApp, callingUid,
            intent, resolvedType, aInfo, mService.mConfiguration,
            resultRecord, resultWho, requestCode, componentSpecified);

    if (mMainStack) {
        //mResumedActivity代表当前显示的Activity。
        if (mResumedActivity == null
                || mResumedActivity.info.applicationInfo.uid != callingUid) {
            //需要切换进程。
            if (!mService.checkAppSwitchAllowedLocked(callingPid, callingUid, "Activity start")) {
                //如果没有权限切换进程，那么久保存请求。
                PendingActivityLaunch pal = new PendingActivityLaunch();
                pal.r = r;
                pal.sourceRecord = sourceRecord;
                pal.grantedUriPermissions = grantedUriPermissions;
                pal.grantedMode = grantedMode;
                pal.onlyIfNeeded = onlyIfNeeded;
                mService.mPendingActivityLaunches.add(pal);
                return START_SWITCHES_CANCELED;
            }
        }
    
        if (mService.mDidAppSwitch) {
            // This is the second allowed switch since we stopped switches,
            // so now just generally allow switches.  Use case: user presses
            // home (switches disabled, switch to home, mDidAppSwitch now true);
            // user taps a home icon (coming from home so allowed, we hit here
            // and now allow anyone to switch again).
            mService.mAppSwitchesAllowedTime = 0;
        } else {
            mService.mDidAppSwitch = true;
        }
     
        //启动pending状态的Activity。
        mService.doPendingActivityLaunchesLocked(false);
    }
    
    return startActivityUncheckedLocked(r, sourceRecord,
            grantedUriPermissions, grantedMode, onlyIfNeeded, true);
}
```

* startActivityUncheckedLocked

 startActivityUncheckLocked函数主要是创建ActivityRecord和TaskRecord,并将ActivityRecord添加到mHistory的末尾，然后调用resumeTopActivityLocked启动它。
 方法很长，但是主要的作用是为activityRecord寻找一个合适的Task。
 其第一阶段的工作就是是否需要为新的Activity创建一个Task，是否设置FLAG_ACTIVITY_NEW_TASK

```java

  final int startActivityUncheckedLocked(ActivityRecord r,
            ActivityRecord sourceRecord, Uri[] grantedUriPermissions,
            int grantedMode, boolean onlyIfNeeded, boolean doResume) {
    final Intent intent = r.intent;
    final int callingUid = r.launchedFromUid;
    
    int launchFlags = intent.getFlags();
    
    //判断是否需要调用因为本次Activity启动而被系统一到后台的当前Activity的onUserLeaveHint函数。
    mUserLeaving = (launchFlags&Intent.FLAG_ACTIVITY_NO_USER_ACTION) == 0;
    if (DEBUG_USER_LEAVING) Slog.v(TAG,
            "startActivity() => mUserLeaving=" + mUserLeaving);
    
    //设置ActivityRecord的delayedResume值为true。
    if (!doResume) {
        r.delayedResume = true;
    }
    
    ActivityRecord notTop = (launchFlags&Intent.FLAG_ACTIVITY_PREVIOUS_IS_TOP)
            != 0 ? r : null;
            
    if (onlyIfNeeded) {
        ActivityRecord checkedCaller = sourceRecord;
        if (checkedCaller == null) {
            checkedCaller = topRunningNonDelayedActivityLocked(notTop);
        }
        if (!checkedCaller.realActivity.equals(r.realActivity)) {
            // Caller is not the same as launcher, so always needed.
            onlyIfNeeded = false;
        }
    }
    //如果请求的发起者为空，则需要新建一个Task。
    if (sourceRecord == null) {
       if ((launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK) == 0) {            launchFlags |= Intent.FLAG_ACTIVITY_NEW_TASK;
        }
    } else if (sourceRecord.launchMode == ActivityInfo.LAUNCH_SINGLE_INSTANCE) {
             launchFlags |= Intent.FLAG_ACTIVITY_NEW_TASK;
    } else if (r.launchMode == ActivityInfo.LAUNCH_SINGLE_INSTANCE
            || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TASK) {
       launchFlags |= Intent.FLAG_ACTIVITY_NEW_TASK;
    }

    if (r.resultTo != null && (launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK) != 0) {
        sendActivityResultLocked(-1,
                r.resultTo, r.resultWho, r.requestCode,
            Activity.RESULT_CANCELED, null);
        r.resultTo = null;
    }

    boolean addingToTask = false;
    if (((launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK) != 0 &&
            (launchFlags&Intent.FLAG_ACTIVITY_MULTIPLE_TASK) == 0)
            || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TASK
            || r.launchMode == ActivityInfo.LAUNCH_SINGLE_INSTANCE) {
     
        if (r.resultTo == null) {
          ActivityRecord taskTop = r.launchMode != ActivityInfo.LAUNCH_SINGLE_INSTANCE
                    ? findTaskLocked(intent, r.info)
                    : findActivityLocked(intent, r.info);
            if (taskTop != null) {
                if (taskTop.task.intent == null) {
                  
                    taskTop.task.setIntent(intent, r.info);
                }
              
                ActivityRecord curTop = topRunningNonDelayedActivityLocked(notTop);
                if (curTop.task != taskTop.task) {
                    r.intent.addFlags(Intent.FLAG_ACTIVITY_BROUGHT_TO_FRONT);
                    boolean callerAtFront = sourceRecord == null
                            || curTop.task == sourceRecord.task;
                    if (callerAtFront) {
                      moveTaskToFrontLocked(taskTop.task, r);
                    }
                }
               
                if ((launchFlags&Intent.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED) != 0) {
                    taskTop = resetTaskIfNeededLocked(taskTop, r);
                }
                if (onlyIfNeeded) {
                                    if (doResume) {
                        resumeTopActivityLocked(null);
                    }
                    return START_RETURN_INTENT_TO_CALLER;
                }
                if ((launchFlags&Intent.FLAG_ACTIVITY_CLEAR_TOP) != 0
                        || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TASK
                        || r.launchMode == ActivityInfo.LAUNCH_SINGLE_INSTANCE) {
                 
                    ActivityRecord top = performClearTaskLocked(
                            taskTop.task.taskId, r, launchFlags, true);
                    if (top != null) {
                        if (top.frontOfTask) {
                           
                            top.task.setIntent(r.intent, r.info);
                        }
                        logStartActivity(EventLogTags.AM_NEW_INTENT, r, top.task);
                        top.deliverNewIntentLocked(callingUid, r.intent);
                    } else {
                      
                        addingToTask = true;
                        sourceRecord = taskTop;
                    }
                } else if (r.realActivity.equals(taskTop.task.realActivity)) {
                    
                    if ((launchFlags&Intent.FLAG_ACTIVITY_SINGLE_TOP) != 0
                            && taskTop.realActivity.equals(r.realActivity)) {
                        logStartActivity(EventLogTags.AM_NEW_INTENT, r, taskTop.task);
                        if (taskTop.frontOfTask) {
                            taskTop.task.setIntent(r.intent, r.info);
                        }
                        taskTop.deliverNewIntentLocked(callingUid, r.intent);
                    } else if (!r.intent.filterEquals(taskTop.task.intent)) {
                       
                        addingToTask = true;
                        sourceRecord = taskTop;
                    }
                } else if ((launchFlags&Intent.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED) == 0) {
                    addingToTask = true;
                    sourceRecord = taskTop;
                } else if (!taskTop.task.rootWasReset) {
                    taskTop.task.setIntent(r.intent, r.info);
                }
                if (!addingToTask) {
                    if (doResume) {
                        resumeTopActivityLocked(null);
                    }
                    return START_TASK_TO_FRONT;
                }
            }
        }
    }

    if (r.packageName != null) {
        //查找栈顶的Activity，确定是新建Activty还是传递onNewIntent。
        ActivityRecord top = topRunningNonDelayedActivityLocked(notTop);
        if (top != null && r.resultTo == null) {
            if (top.realActivity.equals(r.realActivity)) {
                if (top.app != null && top.app.thread != null) {
                    if ((launchFlags&Intent.FLAG_ACTIVITY_SINGLE_TOP) != 0
                        || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TOP
                        || r.launchMode == ActivityInfo.LAUNCH_SINGLE_TASK) {
                        logStartActivity(EventLogTags.AM_NEW_INTENT, top, top.task);
                        // For paranoia, make sure we have correctly
                        // resumed the top activity.
                        if (doResume) {
                            resumeTopActivityLocked(null);
                        }
                        if (onlyIfNeeded) {
                            // We don't need to start a new activity, and
                            // the client said not to do anything if that
                            // is the case, so this is it!
                            return START_RETURN_INTENT_TO_CALLER;
                        }
                        top.deliverNewIntentLocked(callingUid, r.intent);
                        return START_DELIVERED_TO_TOP;
                    }
                }
            }
        }

    } else {
        if (r.resultTo != null) {
            sendActivityResultLocked(-1,
                    r.resultTo, r.resultWho, r.requestCode,
                Activity.RESULT_CANCELED, null);
        }
        return START_CLASS_NOT_FOUND;
    }

    boolean newTask = false;

    // Should this be considered a new task?
    if (r.resultTo == null && !addingToTask
            && (launchFlags&Intent.FLAG_ACTIVITY_NEW_TASK) != 0) {
        // todo: should do better management of integers.
        mService.mCurTask++;
        if (mService.mCurTask <= 0) {
            mService.mCurTask = 1;
        }
        //新创建一个Task。
        r.task = new TaskRecord(mService.mCurTask, r.info, intent,
                (r.info.flags&ActivityInfo.FLAG_CLEAR_TASK_ON_LAUNCH) != 0);
        if (DEBUG_TASKS) Slog.v(TAG, "Starting new activity " + r
                + " in new task " + r.task);
        newTask = true;
        if (mMainStack) {
            mService.addRecentTaskLocked(r.task);
        }
        
    } else if (sourceRecord != null) {
        if (!addingToTask &&
                (launchFlags&Intent.FLAG_ACTIVITY_CLEAR_TOP) != 0) {
            // In this case, we are adding the activity to an existing
            // task, but the caller has asked to clear that task if the
            // activity is already running.
            ActivityRecord top = performClearTaskLocked(
                    sourceRecord.task.taskId, r, launchFlags, true);
            if (top != null) {
                logStartActivity(EventLogTags.AM_NEW_INTENT, r, top.task);
                top.deliverNewIntentLocked(callingUid, r.intent);
                // For paranoia, make sure we have correctly
                // resumed the top activity.
                if (doResume) {
                    resumeTopActivityLocked(null);
                }
                return START_DELIVERED_TO_TOP;
            }
        } else if (!addingToTask &&
                (launchFlags&Intent.FLAG_ACTIVITY_REORDER_TO_FRONT) != 0) {
            // In this case, we are launching an activity in our own task
            // that may already be running somewhere in the history, and
            // we want to shuffle it to the front of the stack if so.
            int where = findActivityInHistoryLocked(r, sourceRecord.task.taskId);
            if (where >= 0) {
                ActivityRecord top = moveActivityToFrontLocked(where);
                logStartActivity(EventLogTags.AM_NEW_INTENT, r, top.task);
                top.deliverNewIntentLocked(callingUid, r.intent);
                if (doResume) {
                    resumeTopActivityLocked(null);
                }
                return START_DELIVERED_TO_TOP;
            }
        }
        // An existing activity is starting this new activity, so we want
        // to keep the new one in the same task as the one that is starting
        // it.
        r.task = sourceRecord.task;
        if (DEBUG_TASKS) Slog.v(TAG, "Starting new activity " + r
                + " in existing task " + r.task);

    } else {
        // This not being started from an existing activity, and not part
        // of a new task...  just put it in the top task, though these days
        // this case should never happen.
        final int N = mHistory.size();
        ActivityRecord prev =
            N > 0 ? (ActivityRecord)mHistory.get(N-1) : null;
        r.task = prev != null
            ? prev.task
            : new TaskRecord(mService.mCurTask, r.info, intent,
                    (r.info.flags&ActivityInfo.FLAG_CLEAR_TASK_ON_LAUNCH) != 0);
        if (DEBUG_TASKS) Slog.v(TAG, "Starting new activity " + r
                + " in new guessed " + r.task);
    }

    if (grantedUriPermissions != null && callingUid > 0) {
        for (int i=0; i<grantedUriPermissions.length; i++) {
            mService.grantUriPermissionLocked(callingUid, r.packageName,
                    grantedUriPermissions[i], grantedMode, r.getUriPermissionsLocked());
        }
    }

    mService.grantUriPermissionFromIntentLocked(callingUid, r.packageName,
            intent, r.getUriPermissionsLocked());

    if (newTask) {
        EventLog.writeEvent(EventLogTags.AM_CREATE_TASK, r.task.taskId);
    }
    logStartActivity(EventLogTags.AM_CREATE_ACTIVITY, r, r.task);
    startActivityLocked(r, newTask, doResume);
    return START_SUCCESS;
}
```


* startActivityLocked

```java
private final void startActivityLocked(ActivityRecord r, boolean newTask,
            boolean doResume) {
    final int NH = mHistory.size();

    int addPos = -1;
    
    if (!newTask) {
        //如果不是新的newTask，则找到对应的位置。
        boolean startIt = true;
        for (int i = NH-1; i >= 0; i--) {
            ActivityRecord p = (ActivityRecord)mHistory.get(i);
            if (p.finishing) {
                continue;
            }
            if (p.task == r.task) {
                // Here it is!  Now, if this is not yet visible to the
                // user, then just add it without starting; it will
                // get started when the user navigates back to it.
                addPos = i+1;
                if (!startIt) {
                    mHistory.add(addPos, r);
                    r.inHistory = true;
                    r.task.numActivities++;
                    mService.mWindowManager.addAppToken(addPos, r, r.task.taskId,
                            r.info.screenOrientation, r.fullscreen);
                    if (VALIDATE_TOKENS) {
                        mService.mWindowManager.validateAppTokens(mHistory);
                    }
                    return;
                }
                break;
            }
            if (p.fullscreen) {
                startIt = false;
            }
        }
    }

    //添加到最后
    if (addPos < 0) {
        addPos = NH;
    }
    
    // If we are not placing the new activity frontmost, we do not want
    // to deliver the onUserLeaving callback to the actual frontmost
    // activity
    if (addPos < NH) {
        mUserLeaving = false;
        if (DEBUG_USER_LEAVING) Slog.v(TAG, "startActivity() behind front, mUserLeaving=false");
    }
    
    // Slot the activity into the history stack and proceed
    mHistory.add(addPos, r);
    r.inHistory = true;
    r.frontOfTask = newTask;
    r.task.numActivities++;
    if (NH > 0) {
        // We want to show the starting preview window if we are
        // switching to a new task, or the next activity's process is
        // not currently running.
        boolean showStartingIcon = newTask;
        ProcessRecord proc = r.app;
        if (proc == null) {
            proc = mService.mProcessNames.get(r.processName, r.info.applicationInfo.uid);
        }
        if (proc == null || proc.thread == null) {
            showStartingIcon = true;
        }
        if (DEBUG_TRANSITION) Slog.v(TAG,
                "Prepare open transition: starting " + r);
        if ((r.intent.getFlags()&Intent.FLAG_ACTIVITY_NO_ANIMATION) != 0) {
            mService.mWindowManager.prepareAppTransition(WindowManagerPolicy.TRANSIT_NONE);
            mNoAnimActivities.add(r);
        } else if ((r.intent.getFlags()&Intent.FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET) != 0) {
            mService.mWindowManager.prepareAppTransition(
                    WindowManagerPolicy.TRANSIT_TASK_OPEN);
            mNoAnimActivities.remove(r);
        } else {
            mService.mWindowManager.prepareAppTransition(newTask
                    ? WindowManagerPolicy.TRANSIT_TASK_OPEN
                    : WindowManagerPolicy.TRANSIT_ACTIVITY_OPEN);
            mNoAnimActivities.remove(r);
        }
        mService.mWindowManager.addAppToken(
                addPos, r, r.task.taskId, r.info.screenOrientation, r.fullscreen);
        boolean doShow = true;
        if (newTask) {
               if ((r.intent.getFlags()
                    &Intent.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED) != 0) {
                resetTaskIfNeededLocked(r, r);
                doShow = topRunningNonDelayedActivityLocked(null) == r;
            }
        }
        if (SHOW_APP_STARTING_PREVIEW && doShow) {
           ActivityRecord prev = mResumedActivity;
            if (prev != null) {
                // We don't want to reuse the previous starting preview if:
                // (1) The current activity is in a different task.
                if (prev.task != r.task) prev = null;
                // (2) The current activity is already displayed.
                else if (prev.nowVisible) prev = null;
            }
            mService.mWindowManager.setAppStartingWindow(
                    r, r.packageName, r.theme, r.nonLocalizedLabel,
                    r.labelRes, r.icon, prev, showStartingIcon);
        }
    } else {
          mService.mWindowManager.addAppToken(addPos, r, r.task.taskId,
                r.info.screenOrientation, r.fullscreen);
    }
    if (VALIDATE_TOKENS) {
        mService.mWindowManager.validateAppTokens(mHistory);
    }

    if (doResume) {
        resumeTopActivityLocked(null);
    }
}

```

* resumeTopActivityLocked


```java
final boolean resumeTopActivityLocked(ActivityRecord prev) {
    //找到第一个启动的ActivityRecord
    ActivityRecord next = topRunningActivityLocked(null);
    final boolean userLeaving = mUserLeaving;
    mUserLeaving = false;

    if (next == null) {
        //如果没有要启动的Activity就打开Home页面
        if (mMainStack) {
            return mService.startHomeActivityLocked();
        }
    }

    next.delayedResume = false;
    
    if (mResumedActivity == next && next.state == ActivityState.RESUMED){        mService.mWindowManager.executeAppTransition();
        mNoAnimActivities.clear();
        return false;
    }
if ((mService.mSleeping || mService.mShuttingDown)
            && mLastPausedActivity == next && next.state == ActivityState.PAUSED) {
        mService.mWindowManager.executeAppTransition();
        mNoAnimActivities.clear();
        return false;
    }
    mStoppingActivities.remove(next);
    mWaitingVisibleActivities.remove(next);

    if (DEBUG_SWITCH) Slog.v(TAG, "Resuming " + next);
    //如果中断一个Activity，需要哪个Activity中断完毕
    if (mPausingActivity != null) {
        if (DEBUG_SWITCH) Slog.v(TAG, "Skip resume: pausing=" + mPausingActivity);
        return false;
    }

    // Okay we are now going to start a switch, to 'next'.  We may first
    // have to pause the current activity, but this is an important point
    // where we have decided to go to 'next' so keep track of that.
    // XXX "App Redirected" dialog is getting too many false positives
    // at this point, so turn off for now.
    if (false) {
        if (mLastStartedActivity != null && !mLastStartedActivity.finishing) {
            long now = SystemClock.uptimeMillis();
            final boolean inTime = mLastStartedActivity.startTime != 0
                    && (mLastStartedActivity.startTime + START_WARN_TIME) >= now;
            final int lastUid = mLastStartedActivity.info.applicationInfo.uid;
            final int nextUid = next.info.applicationInfo.uid;
            if (inTime && lastUid != nextUid
                    && lastUid != next.launchedFromUid
                    && mService.checkPermission(
                            android.Manifest.permission.STOP_APP_SWITCHES,
                            -1, next.launchedFromUid)
                    != PackageManager.PERMISSION_GRANTED) {
                mService.showLaunchWarningLocked(mLastStartedActivity, next);
            } else {
                next.startTime = now;
                mLastStartedActivity = next;
            }
        } else {
            next.startTime = SystemClock.uptimeMillis();
            mLastStartedActivity = next;
        }
    }
    
    // 中断当前的Activity。 mResumedActivity 当前正在显示的Activity。
    if (mResumedActivity != null) {
        if (DEBUG_SWITCH) Slog.v(TAG, "Skip resume: need to start pausing");
        startPausingLocked(userLeaving, false);
        return true;
    }

    if (prev != null && prev != next) {
        if (!prev.waitingVisible && next != null && !next.nowVisible) {
            prev.waitingVisible = true;
            mWaitingVisibleActivities.add(prev);
            if (DEBUG_SWITCH) Slog.v(
                    TAG, "Resuming top, waiting visible to hide: " + prev);
        } else {
          
            if (prev.finishing) {
                mService.mWindowManager.setAppVisibility(prev, false);
                if (DEBUG_SWITCH) Slog.v(TAG, "Not waiting for visible to hide: "
                        + prev + ", waitingVisible="
                        + (prev != null ? prev.waitingVisible : null)
                        + ", nowVisible=" + next.nowVisible);
            } else {
                if (DEBUG_SWITCH) Slog.v(TAG, "Previous already visible but still waiting to hide: "
                    + prev + ", waitingVisible="
                    + (prev != null ? prev.waitingVisible : null)
                    + ", nowVisible=" + next.nowVisible);
            }
        }
    }


    if (prev != null) {
        if (prev.finishing) {
            if (DEBUG_TRANSITION) Slog.v(TAG,
                    "Prepare close transition: prev=" + prev);
            if (mNoAnimActivities.contains(prev)) {
                mService.mWindowManager.prepareAppTransition(WindowManagerPolicy.TRANSIT_NONE);
            } else {
                mService.mWindowManager.prepareAppTransition(prev.task == next.task
                        ? WindowManagerPolicy.TRANSIT_ACTIVITY_CLOSE
                        : WindowManagerPolicy.TRANSIT_TASK_CLOSE);
            }
            mService.mWindowManager.setAppWillBeHidden(prev);
            mService.mWindowManager.setAppVisibility(prev, false);
        } else {
            if (DEBUG_TRANSITION) Slog.v(TAG,
                    "Prepare open transition: prev=" + prev);
            if (mNoAnimActivities.contains(next)) {
                mService.mWindowManager.prepareAppTransition(WindowManagerPolicy.TRANSIT_NONE);
            } else {
                mService.mWindowManager.prepareAppTransition(prev.task == next.task
                        ? WindowManagerPolicy.TRANSIT_ACTIVITY_OPEN
                        : WindowManagerPolicy.TRANSIT_TASK_OPEN);
            }
        }
        if (false) {
            mService.mWindowManager.setAppWillBeHidden(prev);
            mService.mWindowManager.setAppVisibility(prev, false);
        }
    } else if (mHistory.size() > 1) {
        if (DEBUG_TRANSITION) Slog.v(TAG,
                "Prepare open transition: no previous");
        if (mNoAnimActivities.contains(next)) {
            mService.mWindowManager.prepareAppTransition(WindowManagerPolicy.TRANSIT_NONE);
        } else {
            mService.mWindowManager.prepareAppTransition(WindowManagerPolicy.TRANSIT_ACTIVITY_OPEN);
        }
    }

    if (next.app != null && next.app.thread != null) {
        if (DEBUG_SWITCH) Slog.v(TAG, "Resume running: " + next);
        //如果该ActivityRecord对应的进程已经存在，就重启Activity就可以了。
        // This activity is now becoming visible.
        mService.mWindowManager.setAppVisibility(next, true);

        ActivityRecord lastResumedActivity = mResumedActivity;
        ActivityState lastState = next.state;

        mService.updateCpuStats();
        
        next.state = ActivityState.RESUMED;
        mResumedActivity = next;
        next.task.touchActiveTime();
        mService.updateLruProcessLocked(next.app, true, true);
        updateLRUListLocked(next);

       
        boolean updated = false;
        if (mMainStack) {
            synchronized (mService) {
                Configuration config = mService.mWindowManager.updateOrientationFromAppTokens(
                        mService.mConfiguration,
                        next.mayFreezeScreenLocked(next.app) ? next : null);
                if (config != null) {
                    next.frozenBeforeDestroy = true;
                }
                updated = mService.updateConfigurationLocked(config, next);
            }
        }
        if (!updated) {
            // The configuration update wasn't able to keep the existing
            // instance of the activity, and instead started a new one.
            // We should be all done, but let's just make sure our activity
            // is still at the top and schedule another run if something
            // weird happened.
            ActivityRecord nextNext = topRunningActivityLocked(null);
            if (DEBUG_SWITCH) Slog.i(TAG,
                    "Activity config changed during resume: " + next
                    + ", new next: " + nextNext);
            if (nextNext != next) {
                // Do over!
                mHandler.sendEmptyMessage(RESUME_TOP_ACTIVITY_MSG);
            }
            if (mMainStack) {
                mService.setFocusedActivityLocked(next);
            }
            ensureActivitiesVisibleLocked(null, 0);
            mService.mWindowManager.executeAppTransition();
            mNoAnimActivities.clear();
            return true;
        }
        
        try {
            // Deliver all pending results.
            ArrayList a = next.results;
            if (a != null) {
                final int N = a.size();
                if (!next.finishing && N > 0) {
                    if (DEBUG_RESULTS) Slog.v(
                            TAG, "Delivering results to " + next
                            + ": " + a);
                    next.app.thread.scheduleSendResult(next, a);
                }
            }

            if (next.newIntents != null) {
                next.app.thread.scheduleNewIntent(next.newIntents, next);
            }

            EventLog.writeEvent(EventLogTags.AM_RESUME_ACTIVITY,
                    System.identityHashCode(next),
                    next.task.taskId, next.shortComponentName);
            
            next.app.thread.scheduleResumeActivity(next,
                    mService.isNextTransitionForward());
            
            pauseIfSleepingLocked();

        } catch (Exception e) {
            // Whoops, need to restart this activity!
            next.state = lastState;
            mResumedActivity = lastResumedActivity;
            Slog.i(TAG, "Restarting because process died: " + next);
            if (!next.hasBeenLaunched) {
                next.hasBeenLaunched = true;
            } else {
                if (SHOW_APP_STARTING_PREVIEW && mMainStack) {
                    mService.mWindowManager.setAppStartingWindow(
                            next, next.packageName, next.theme,
                            next.nonLocalizedLabel,
                            next.labelRes, next.icon, null, true);
                }
            }
            startSpecificActivityLocked(next, true, false);
            return true;
        }

        // From this point on, if something goes wrong there is no way
        // to recover the activity.
        try {
            next.visible = true;
            completeResumeLocked(next);
        } catch (Exception e) {
            // If any exception gets thrown, toss away this
            // activity and try the next one.
            Slog.w(TAG, "Exception thrown during resume of " + next, e);
            requestFinishActivityLocked(next, Activity.RESULT_CANCELED, null,
                    "resume-exception");
            return true;
        }

        // Didn't need to use the icicle, and it is now out of date.
        next.icicle = null;
        next.haveState = false;
        next.stopped = false;

    } else {
        // 如果是第一次启动。
        if (!next.hasBeenLaunched) {
            next.hasBeenLaunched = true;
        } else {
            if (SHOW_APP_STARTING_PREVIEW) {
                mService.mWindowManager.setAppStartingWindow(
                        next, next.packageName, next.theme,
                        next.nonLocalizedLabel,
                        next.labelRes, next.icon, null, true);
            }
            if (DEBUG_SWITCH) Slog.v(TAG, "Restarting: " + next);
        }
        startSpecificActivityLocked(next, true, true);
    }

    return true;
}

```

* startSpecificActivityLocked



```java

 private final void startSpecificActivityLocked(ActivityRecord r,
            boolean andResume, boolean checkConfig) {
        // 查询是否满足存在的进程。
        ProcessRecord app = mService.getProcessRecordLocked(r.processName,
                r.info.applicationInfo.uid);
        
        if (r.launchTime == 0) {
            r.launchTime = SystemClock.uptimeMillis();
            if (mInitialStartTime == 0) {
                mInitialStartTime = r.launchTime;
            }
        } else if (mInitialStartTime == 0) {
            mInitialStartTime = SystemClock.uptimeMillis();
        }
        
        if (app != null && app.thread != null) {
            //如果进程已存在，并向AMS注册
            try {
                realStartActivityLocked(r, app, andResume, checkConfig);
                return;
            } catch (RemoteException e) {
                Slog.w(TAG, "Exception when starting activity "
                        + r.intent.getComponent().flattenToShortString(), e);
            }
        }
        //如果进程不存在，则需要调用AMS的startProcessLocked创建一个应用进程。
        mService.startProcessLocked(r.processName, r.info.applicationInfo, true, 0,
                "activity", r.intent.getComponent(), false);
    }
```

* ActivityManagerService.startProcessLocked
创建一个新的进程。

```java
  final ProcessRecord startProcessLocked(String processName,
            ApplicationInfo info, boolean knownToBeDead, int intentFlags,
            String hostingType, ComponentName hostingName, boolean allowWhileBooting) {
    //根据进程名字和uid，寻找存在的进程ProcessRecord。
    ProcessRecord app = getProcessRecordLocked(processName, info.uid);
   if (app != null && app.pid > 0) {
        if (!knownToBeDead || app.thread == null) {
           return app;
        } else {
            // An application record is attached to a previous process,
            // clean it up now.
            if (DEBUG_PROCESSES) Slog.v(TAG, "App died: " + app);
            handleAppDiedLocked(app, true);
        }
    }

    String hostingNameStr = hostingName != null
            ? hostingName.flattenToShortString() : null;
    
    if ((intentFlags&Intent.FLAG_FROM_BACKGROUND) != 0) {
        //如果是后台进程，则查看是其是否已经损坏。
        if (mBadProcesses.get(info.processName, info.uid) != null) {
            if (DEBUG_PROCESSES) Slog.v(TAG, "Bad process: " + info.uid
                    + "/" + info.processName);
            return null;
        }
    } else {
        mProcessCrashTimes.remove(info.processName, info.uid);
        if (mBadProcesses.get(info.processName, info.uid) != null) {
            EventLog.writeEvent(EventLogTags.AM_PROC_GOOD, info.uid,
                    info.processName);
            mBadProcesses.remove(info.processName, info.uid);
            if (app != null) {
                app.bad = false;
            }
        }
    }
    
    if (app == null) {
        app = newProcessRecordLocked(null, info, processName);
        mProcessNames.put(processName, info.uid, app);
    } else {
        app.addPackage(info.packageName);
    } 
    if (!mProcessesReady
            && !isAllowedWhileBooting(info)
            && !allowWhileBooting) {
        if (!mProcessesOnHold.contains(app)) {
            mProcessesOnHold.add(app);
        }
        if (DEBUG_PROCESSES) Slog.v(TAG, "System not ready, putting on hold: " + app);
        return app;
    }

    startProcessLocked(app, hostingType, hostingNameStr);
    return (app.pid != 0) ? app : null;
}


private final void startProcessLocked(ProcessRecord app,
            String hostingType, String hostingNameStr) {
        if (app.pid > 0 && app.pid != MY_PID) {
            synchronized (mPidsSelfLocked) {
                mPidsSelfLocked.remove(app.pid);
                mHandler.removeMessages(PROC_START_TIMEOUT_MSG, app);
            }
            app.pid = 0;
        }

        if (DEBUG_PROCESSES && mProcessesOnHold.contains(app)) Slog.v(TAG,
                "startProcessLocked removing on hold: " + app);
        //mProcessesOnHold 中保存那些在系统还没有准备好久提前请求启动的ProcessRecord.
                
        mProcessesOnHold.remove(app);

        updateCpuStats();
        
        System.arraycopy(mProcDeaths, 0, mProcDeaths, 1, mProcDeaths.length-1);
        mProcDeaths[0] = 0;
        
        try {
            int uid = app.info.uid;
            int[] gids = null;
            try {
                gids = mContext.getPackageManager().getPackageGids(
                        app.info.packageName);
            } catch (PackageManager.NameNotFoundException e) {
                Slog.w(TAG, "Unable to retrieve gids", e);
            }
            if (mFactoryTest != SystemServer.FACTORY_TEST_OFF) {
                if (mFactoryTest == SystemServer.FACTORY_TEST_LOW_LEVEL
                        && mTopComponent != null
                        && app.processName.equals(mTopComponent.getPackageName())) {
                    uid = 0;
                }
                if (mFactoryTest == SystemServer.FACTORY_TEST_HIGH_LEVEL
                        && (app.info.flags&ApplicationInfo.FLAG_FACTORY_TEST) != 0) {
                    uid = 0;
                }
            }
            int debugFlags = 0;
            if ((app.info.flags & ApplicationInfo.FLAG_DEBUGGABLE) != 0) {
                debugFlags |= Zygote.DEBUG_ENABLE_DEBUGGER;
            }
            // Run the app in safe mode if its manifest requests so or the
            // system is booted in safe mode.
            if ((app.info.flags & ApplicationInfo.FLAG_VM_SAFE_MODE) != 0 ||
                Zygote.systemInSafeMode == true) {
                debugFlags |= Zygote.DEBUG_ENABLE_SAFEMODE;
            }
            if ("1".equals(SystemProperties.get("debug.checkjni"))) {
                debugFlags |= Zygote.DEBUG_ENABLE_CHECKJNI;
            }
            if ("1".equals(SystemProperties.get("debug.assert"))) {
                debugFlags |= Zygote.DEBUG_ENABLE_ASSERT;
            }
            //发送消息给Zygote，它将派生一个子进程，该子进程执行ActivityThread的main函数。
            //我们传递给Zygote的参数并没有保护任何与Activity相关的信息。
            int pid = Process.start("android.app.ActivityThread",
                    mSimpleProcessManagement ? app.processName : null, uid, uid,
                    gids, debugFlags, null);
            BatteryStatsImpl bs = app.batteryStats.getBatteryStats();
            synchronized (bs) {
                if (bs.isOnBattery()) {
                    app.batteryStats.incStartsLocked();
                }
            }
            
            EventLog.writeEvent(EventLogTags.AM_PROC_START, pid, uid,
                    app.processName, hostingType,
                    hostingNameStr != null ? hostingNameStr : "");
            
            if (app.persistent) {
                Watchdog.getInstance().processStarted(app.processName, pid);
            }
            
            StringBuilder buf = mStringBuilder;
            buf.setLength(0);
            buf.append("Start proc ");
            buf.append(app.processName);
            buf.append(" for ");
            buf.append(hostingType);
            if (hostingNameStr != null) {
                buf.append(" ");
                buf.append(hostingNameStr);
            }
            buf.append(": pid=");
            buf.append(pid);
            buf.append(" uid=");
            buf.append(uid);
            buf.append(" gids={");
            if (gids != null) {
                for (int gi=0; gi<gids.length; gi++) {
                    if (gi != 0) buf.append(", ");
                    buf.append(gids[gi]);

                }
            }
            buf.append("}");
            Slog.i(TAG, buf.toString());
            if (pid == 0 || pid == MY_PID) {
                // Processes are being emulated with threads.
                app.pid = MY_PID;
                app.removed = false;
                mStartingProcesses.add(app);
            } else if (pid > 0) {
                app.pid = pid;
                app.removed = false;
                synchronized (mPidsSelfLocked) {
                    //将进程ProcessRecord对象加入到mPidsSelfLocked中保管。
                    this.mPidsSelfLocked.put(pid, app);
                    Message msg = mHandler.obtainMessage(PROC_START_TIMEOUT_MSG);
                    msg.obj = app;
                    mHandler.sendMessageDelayed(msg, PROC_START_TIMEOUT);
                }
            } else {
                app.pid = 0;
                RuntimeException e = new RuntimeException(
                        "Failure starting process " + app.processName
                        + ": returned pid=" + pid);
                Slog.e(TAG, e.getMessage(), e);
            }
        } catch (RuntimeException e) {
            // XXX do better error recovery.
            app.pid = 0;
            Slog.e(TAG, "Failure starting process " + app.processName, e);
        }
    }


```

## 应用进程的创建和初始化

应用进程的入口是ActivityThread的main函数，它在主线程中执行的。

```java
  public static final void main(String[] args) {
    SamplingProfilerIntegration.start();

    Process.setArgV0("<pre-initialized>");

    //准备主线程消息循环。
    Looper.prepareMainLooper();
    if (sMainThreadHandler == null) {
        sMainThreadHandler = new Handler();
    }
    
    //创建一个ActivityThread对象。
    ActivityThread thread = new ActivityThread();
    thread.attach(false);//传入false，非系统。

    if (false) {
        Looper.myLooper().setMessageLogging(new
                LogPrinter(Log.DEBUG, "ActivityThread"));
    }
    
    //主线程消息循环。
    Looper.loop();

    if (Process.supportsProcesses()) {
        throw new RuntimeException("Main thread loop unexpectedly exited");
    }

    thread.detach();
    String name = (thread.mInitialApplication != null)
        ? thread.mInitialApplication.getPackageName()
        : "<unknown>";
    Slog.i(TAG, "Main thread of " + name + " is now exiting");
}
```

* ActivityThread.attach


```java

   private final void attach(boolean system) {
    sThreadLocal.set(this);
    mSystemThread = system;
    if (!system) {
        ViewRoot.addFirstDrawHandler(new Runnable() {
            public void run() {
                ensureJitEnabled();
            }
        });
        android.ddm.DdmHandleAppName.setAppName("<pre-initialized>");
        RuntimeInit.setApplicationObject(mAppThread.asBinder());
        //将AMS和当前线程进行关联。
        IActivityManager mgr = ActivityManagerNative.getDefault();
        try {
            mgr.attachApplication(mAppThread);
        } catch (RemoteException ex) {
        }
    } else {
       //初始化AMS服务所在的主线程的会进入。
       ...
    }
  }
```

* ActivityManagerService.attachApplicationLocked

//
```java
  private final boolean attachApplicationLocked(IApplicationThread thread,
            int pid) {//pid代表调用进程的pid。


    ProcessRecord app;
    if (pid != MY_PID && pid >= 0) {
        synchronized (mPidsSelfLocked) {
            app = mPidsSelfLocked.get(pid);
        }
    } else if (mStartingProcesses.size() > 0) {
        app = mStartingProcesses.remove(0);
        app.setPid(pid);
    } else {
        app = null;
    }

    //在AMS不存在的记录进程，肯定不是AMS创建的，所以要删除掉。
    if (app == null) {
        EventLog.writeEvent(EventLogTags.AM_DROP_PROCESS, pid);
        if (pid > 0 && pid != MY_PID) {
            Process.killProcess(pid);
        } else {
            try {
                thread.scheduleExit();
            } catch (Exception e) {
                // Ignore exceptions.
            }
        }
        return false;
    }

    //此时app.thread应该为null，否则就是旧进程没有被扇死。
    if (app.thread != null) {
        handleAppDiedLocked(app, true);
    }

    // Tell the process all about itself.

    if (localLOGV) Slog.v(
            TAG, "Binding process pid " + pid + " to record " + app);

    String processName = app.processName;
    try {
        //创建一个应用对象的讣告接收对象，当应用进程退出时，该对象的binderDied将被调用
        //这样AMS就能做响应的处理。binderDied函数将在另外一个线程中执行，其内部也会调用HandleAppDieLoacked。
        thread.asBinder().linkToDeath(new AppDeathRecipient(
                app, pid, thread), 0);
    } catch (RemoteException e) {
        app.resetPackageList();
        startProcessLocked(app, "link fail", processName);
        return false;
    }

    EventLog.writeEvent(EventLogTags.AM_PROC_BOUND, app.pid, app.processName);
    
    app.thread = thread;
    app.curAdj = app.setAdj = -100;
    app.curSchedGroup = Process.THREAD_GROUP_DEFAULT;
    app.setSchedGroup = Process.THREAD_GROUP_BG_NONINTERACTIVE;
    app.forcingToForeground = null;
    app.foregroundServices = false;
    app.debugging = false;

    //启动成功，从消息队列中撤销PROC_START_TIMEOUT_MSG消息。
    mHandler.removeMessages(PROC_START_TIMEOUT_MSG, app);

    boolean normalMode = mProcessesReady || isAllowedWhileBooting(app.info);
    List providers = normalMode ? generateApplicationProvidersLocked(app) : null;

    if (!normalMode) {
        Slog.i(TAG, "Launching preboot mode app: " + app);
    }
    
    if (localLOGV) Slog.v(
        TAG, "New app record " + app
        + " thread=" + thread.asBinder() + " pid=" + pid);
    try {
        int testMode = IApplicationThread.DEBUG_OFF;
        if (mDebugApp != null && mDebugApp.equals(processName)) {
            testMode = mWaitForDebugger
                ? IApplicationThread.DEBUG_WAIT
                : IApplicationThread.DEBUG_ON;
            app.debugging = true;
            if (mDebugTransient) {
                mDebugApp = mOrigDebugApp;
                mWaitForDebugger = mOrigWaitForDebugger;
            }
        }
        
        // If the app is being launched for restore or full backup, set it up specially
        boolean isRestrictedBackupMode = false;
        if (mBackupTarget != null && mBackupAppName.equals(processName)) {
            isRestrictedBackupMode = (mBackupTarget.backupMode == BackupRecord.RESTORE)
                    || (mBackupTarget.backupMode == BackupRecord.BACKUP_FULL);
        }
        
        //dex化对应的APK包。
        ensurePackageDexOpt(app.instrumentationInfo != null
                ? app.instrumentationInfo.packageName
                : app.info.packageName);
        if (app.instrumentationClass != null) {
            ensurePackageDexOpt(app.instrumentationClass.getPackageName());
        }
        if (DEBUG_CONFIGURATION) Slog.v(TAG, "Binding proc "
                + processName + " with config " + mConfiguration);
        thread.bindApplication(processName, app.instrumentationInfo != null
                ? app.instrumentationInfo : app.info, providers,
                app.instrumentationClass, app.instrumentationProfileFile,
                app.instrumentationArguments, app.instrumentationWatcher, testMode, 
                isRestrictedBackupMode || !normalMode,
                mConfiguration, getCommonServicesLocked());
        updateLruProcessLocked(app, false, true);
        app.lastRequestedGc = app.lastLowMemory = SystemClock.uptimeMillis();
    } catch (Exception e) {
        // todo: Yikes!  What should we do?  For now we will try to
        // start another process, but that could easily get us in
        // an infinite loop of restarting processes...
        Slog.w(TAG, "Exception thrown during bind!", e);

        app.resetPackageList();
        startProcessLocked(app, "bind fail", processName);
        return false;
    }

    // Remove this record from the list of starting applications.
    mPersistentStartingProcesses.remove(app);
    if (DEBUG_PROCESSES && mProcessesOnHold.contains(app)) Slog.v(TAG,
            "Attach application locked removing on hold: " + app);
    mProcessesOnHold.remove(app);

    boolean badApp = false;
    boolean didSomething = false;

    // 获取要启动的Activity

    ActivityRecord hr = mMainStack.topRunningActivityLocked(null);
    if (hr != null && normalMode) {
        if (hr.app == null && app.info.uid == hr.info.applicationInfo.uid
                && processName.equals(hr.processName)) {
            try {
                //在ActivityStack类中，启动Activity。
                if (mMainStack.realStartActivityLocked(hr, app, true, true)) {
                    didSomething = true;
                }
            } catch (Exception e) {
                Slog.w(TAG, "Exception in new application when starting activity "
                      + hr.intent.getComponent().flattenToShortString(), e);
                badApp = true;
            }
        } else {
            mMainStack.ensureActivitiesVisibleLocked(hr, null, processName, 0);
        }
    }

    // Find any services that should be running in this process...
    if (!badApp && mPendingServices.size() > 0) {
        ServiceRecord sr = null;
        try {
            for (int i=0; i<mPendingServices.size(); i++) {
                sr = mPendingServices.get(i);
                if (app.info.uid != sr.appInfo.uid
                        || !processName.equals(sr.processName)) {
                    continue;
                }

                mPendingServices.remove(i);
                i--;
                realStartServiceLocked(sr, app);
                didSomething = true;
            }
        } catch (Exception e) {
            Slog.w(TAG, "Exception in new application when starting service "
                  + sr.shortName, e);
            badApp = true;
        }
    }

    // Check if the next broadcast receiver is in this process...
    BroadcastRecord br = mPendingBroadcast;
    if (!badApp && br != null && br.curApp == app) {
        try {
            mPendingBroadcast = null;
            processCurBroadcastLocked(br, app);
            didSomething = true;
        } catch (Exception e) {
            Slog.w(TAG, "Exception in new application when starting receiver "
                  + br.curComponent.flattenToShortString(), e);
            badApp = true;
            logBroadcastReceiverDiscardLocked(br);
            finishReceiverLocked(br.receiver, br.resultCode, br.resultData,
                    br.resultExtras, br.resultAbort, true);
            scheduleBroadcastsLocked();
            // We need to reset the state if we fails to start the receiver.
            br.state = BroadcastRecord.IDLE;
        }
    }

    // Check whether the next backup agent is in this process...
    if (!badApp && mBackupTarget != null && mBackupTarget.appInfo.uid == app.info.uid) {
        if (DEBUG_BACKUP) Slog.v(TAG, "New app is backup target, launching agent for " + app);
        ensurePackageDexOpt(mBackupTarget.appInfo.packageName);
        try {
            thread.scheduleCreateBackupAgent(mBackupTarget.appInfo, mBackupTarget.backupMode);
        } catch (Exception e) {
            Slog.w(TAG, "Exception scheduling backup agent creation: ");
            e.printStackTrace();
        }
    }

    if (badApp) {
        // todo: Also need to kill application to deal with all
        // kinds of exceptions.
        handleAppDiedLocked(app, false);
        return false;
    }

    if (!didSomething) {
        updateOomAdjLocked();
    }

    return true;
}

public final void attachApplication(IApplicationThread thread) {
    synchronized (this) {
        int callingPid = Binder.getCallingPid();
        final long origId = Binder.clearCallingIdentity();
        attachApplicationLocked(thread, callingPid);
        Binder.restoreCallingIdentity(origId);
    }
}
```

* ActivityThread.bindApplication、hindleBindApplication

绑定Application，构建对象AppBindData，通过发送消息，转交给主线程处理，
```java

 public final void bindApplication(String processName,
                ApplicationInfo appInfo, List<ProviderInfo> providers,
                ComponentName instrumentationName, String profileFile,
                Bundle instrumentationArgs, IInstrumentationWatcher instrumentationWatcher,
                int debugMode, boolean isRestrictedBackupMode, Configuration config,
                Map<String, IBinder> services) {

        if (services != null) {
            // Setup the service cache in the ServiceManager
            ServiceManager.initServiceCache(services);
        }

        AppBindData data = new AppBindData();
        data.processName = processName;
        data.appInfo = appInfo;
        data.providers = providers;
        data.instrumentationName = instrumentationName;
        data.profileFile = profileFile;
        data.instrumentationArgs = instrumentationArgs;
        data.instrumentationWatcher = instrumentationWatcher;
        data.debugMode = debugMode;
        data.restrictedBackupMode = isRestrictedBackupMode;
        data.config = config;
        queueOrSendMessage(H.BIND_APPLICATION, data);
    }
    
    
    private final void handleBindApplication(AppBindData data) {
        mBoundApplication = data;
        mConfiguration = new Configuration(data.config);

        // send up app name; do this *before* waiting for debugger
        Process.setArgV0(data.processName);
        android.ddm.DdmHandleAppName.setAppName(data.processName);

        TimeZone.setDefault(null);
        Locale.setDefault(data.config.locale);

       
        Resources.getSystem().updateConfiguration(mConfiguration, null);

        data.info = getPackageInfoNoCheck(data.appInfo);

       
        if ((data.appInfo.flags &
             (ApplicationInfo.FLAG_SYSTEM |
              ApplicationInfo.FLAG_UPDATED_SYSTEM_APP)) != 0) {
            StrictMode.conditionallyEnableDebugLogging();
        }

        
        if ((data.appInfo.flags&ApplicationInfo.FLAG_SUPPORTS_SCREEN_DENSITIES)
                == 0) {
            Bitmap.setDefaultDensity(DisplayMetrics.DENSITY_DEFAULT);
        }

        if (data.debugMode != IApplicationThread.DEBUG_OFF) {
            // XXX should have option to change the port.
            Debug.changeDebugPort(8100);
            if (data.debugMode == IApplicationThread.DEBUG_WAIT) {
                Slog.w(TAG, "Application " + data.info.getPackageName()
                      + " is waiting for the debugger on port 8100...");

                IActivityManager mgr = ActivityManagerNative.getDefault();
                try {
                    mgr.showWaitingForDebugger(mAppThread, true);
                } catch (RemoteException ex) {
                }

                Debug.waitForDebugger();

                try {
                    mgr.showWaitingForDebugger(mAppThread, false);
                } catch (RemoteException ex) {
                }

            } else {
                Slog.w(TAG, "Application " + data.info.getPackageName()
                      + " can be debugged on port 8100...");
            }
        }

        if (data.instrumentationName != null) {
            //创建ContextImpl， 初始化appContext.
            ContextImpl appContext = new ContextImpl();
            appContext.init(data.info, null, this);
            InstrumentationInfo ii = null;
            try {
                ii = appContext.getPackageManager().
                    getInstrumentationInfo(data.instrumentationName, 0);
            } catch (PackageManager.NameNotFoundException e) {
            }
            if (ii == null) {
                throw new RuntimeException(
                    "Unable to find instrumentation info for: "
                    + data.instrumentationName);
            }

            mInstrumentationAppDir = ii.sourceDir;
            mInstrumentationAppPackage = ii.packageName;
            mInstrumentedAppDir = data.info.getAppDir();

            //创建ApplicationInfo对象
            ApplicationInfo instrApp = new ApplicationInfo();
            instrApp.packageName = ii.packageName;
            instrApp.sourceDir = ii.sourceDir;
            instrApp.publicSourceDir = ii.publicSourceDir;
            instrApp.dataDir = ii.dataDir;
            instrApp.nativeLibraryDir = ii.nativeLibraryDir;
            LoadedApk pi = getPackageInfo(instrApp,
                    appContext.getClassLoader(), false, true);
            ContextImpl instrContext = new ContextImpl();
            instrContext.init(pi, null, this);

            //通过ClassLoader对象，创建Instrumentation对象那个。
            try {
                java.lang.ClassLoader cl = instrContext.getClassLoader();
                mInstrumentation = (Instrumentation)
                    cl.loadClass(data.instrumentationName.getClassName()).newInstance();
            } catch (Exception e) {
                throw new RuntimeException(
                    "Unable to instantiate instrumentation "
                    + data.instrumentationName + ": " + e.toString(), e);
            }

            //初始化。
            mInstrumentation.init(this, instrContext, appContext,
                    new ComponentName(ii.packageName, ii.name), data.instrumentationWatcher);

            if (data.profileFile != null && !ii.handleProfiling) {
                data.handlingProfiling = true;
                File file = new File(data.profileFile);
                file.getParentFile().mkdirs();
                Debug.startMethodTracing(file.toString(), 8 * 1024 * 1024);
            }

            try {
                mInstrumentation.onCreate(data.instrumentationArgs);
            }
            catch (Exception e) {
                throw new RuntimeException(
                    "Exception thrown in onCreate() of "
                    + data.instrumentationName + ": " + e.toString(), e);
            }

        } else {
            mInstrumentation = new Instrumentation();
        }

			//创建Application。
        Application app = data.info.makeApplication(data.restrictedBackupMode, null);
        mInitialApplication = app;

        List<ProviderInfo> providers = data.providers;
        if (providers != null) {
            installContentProviders(app, providers);
            // For process that contain content providers, we want to
            // ensure that the JIT is enabled "at some point".
            mH.sendEmptyMessageDelayed(H.ENABLE_JIT, 10*1000);
        }

        try {
            mInstrumentation.callApplicationOnCreate(app);
        } catch (Exception e) {
            if (!mInstrumentation.onException(app, e)) {
                throw new RuntimeException(
                    "Unable to create application " + app.getClass().getName()
                    + ": " + e.toString(), e);
            }
        }
    }

    /*package*/ final void finishInstrumentation(int resultCode, Bundle results) {
        IActivityManager am = ActivityManagerNative.getDefault();
        if (mBoundApplication.profileFile != null && mBoundApplication.handlingProfiling) {
            Debug.stopMethodTracing();
        }
        //Slog.i(TAG, "am: " + ActivityManagerNative.getDefault()
        //      + ", app thr: " + mAppThread);
        try {
            am.finishInstrumentation(mAppThread, resultCode, results);
        } catch (RemoteException ex) {
        }
    }
    
       
```

## 终于要启动Activity了

* ActivityStack.realStartActivityLocked

```java
 final boolean realStartActivityLocked(ActivityRecord r,
            ProcessRecord app, boolean andResume, boolean checkConfig)
            throws RemoteException {

        r.startFreezingScreenLocked(app, 0);
        mService.mWindowManager.setAppVisibility(r, true);

        if (checkConfig) {
            Configuration config = mService.mWindowManager.updateOrientationFromAppTokens(
                    mService.mConfiguration,
                    r.mayFreezeScreenLocked(app) ? r : null);
            mService.updateConfigurationLocked(config, r);
        }

        r.app = app;

        if (localLOGV) Slog.v(TAG, "Launching: " + r);

        int idx = app.activities.indexOf(r);
        if (idx < 0) {
            app.activities.add(r);
        }
        mService.updateLruProcessLocked(app, true, true);

        try {
            if (app.thread == null) {
                throw new RemoteException();
            }
            List<ResultInfo> results = null;
            List<Intent> newIntents = null;
            if (andResume) {
                results = r.results;
                newIntents = r.newIntents;
            }
            if (DEBUG_SWITCH) Slog.v(TAG, "Launching: " + r
                    + " icicle=" + r.icicle
                    + " with results=" + results + " newIntents=" + newIntents
                    + " andResume=" + andResume);
            if (andResume) {
                EventLog.writeEvent(EventLogTags.AM_RESTART_ACTIVITY,
                        System.identityHashCode(r),
                        r.task.taskId, r.shortComponentName);
            }
            if (r.isHomeActivity) {
                mService.mHomeProcess = app;
            }
            mService.ensurePackageDexOpt(r.intent.getComponent().getPackageName());
            //通知应用进程启动Activity。
            app.thread.scheduleLaunchActivity(new Intent(r.intent), r,
                    System.identityHashCode(r),
                    r.info, r.icicle, results, newIntents, !andResume,
                    mService.isNextTransitionForward());
            
            if ((app.info.flags&ApplicationInfo.FLAG_CANT_SAVE_STATE) != 0) {
              
                if (app.processName.equals(app.info.packageName)) {
                    if (mService.mHeavyWeightProcess != null
                            && mService.mHeavyWeightProcess != app) {
                        Log.w(TAG, "Starting new heavy weight process " + app
                                + " when already running "
                                + mService.mHeavyWeightProcess);
                    }
                    mService.mHeavyWeightProcess = app;
                    Message msg = mService.mHandler.obtainMessage(
                            ActivityManagerService.POST_HEAVY_NOTIFICATION_MSG);
                    msg.obj = r;
                    mService.mHandler.sendMessage(msg);
                }
            }
            
        } catch (RemoteException e) {
            if (r.launchFailed) {
                 mService.appDiedLocked(app, app.pid, app.thread);
                requestFinishActivityLocked(r, Activity.RESULT_CANCELED, null,
                        "2nd-crash");
                return false;
            }
        
            app.activities.remove(r);
            throw e;
        }

        r.launchFailed = false;
        if (updateLRUListLocked(r)) {
            Slog.w(TAG, "Activity " + r
                  + " being launched, but already in LRU list");
        }

        if (andResume) {
            // As part of the process of launching, ActivityThread also performs
            // a resume.
            r.state = ActivityState.RESUMED;
            r.icicle = null;
            r.haveState = false;
            r.stopped = false;
            mResumedActivity = r;
            r.task.touchActiveTime();
            completeResumeLocked(r);
            pauseIfSleepingLocked();                
        } else {
           
            r.state = ActivityState.STOPPED;
            r.stopped = true;
        }

        if (mMainStack) {
            mService.startSetupActivityLocked();
        }
        
        return true;
    }

    ```



