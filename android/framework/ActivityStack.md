# ActivityStack

Activity由ActivityRecord表示，Task由TaskRecord表示。
ActivityRecord的task成员指向该Activity所在的Task。
state变量用于表示该Activity所处的状态（INITIALIZING，RESUMED，PAUSED等）

ActivityStack中用mHistory这个ArrayList保存ActivityRecord，该mHistory保存了所有
的ActivityRecord。

ActivityStack中没有成员用于保存了TaskRecord，ActivityStack采用数组的方式保存了所有的Task的
ActivityRecord，并且没有成员保存了TaskRecord，优点是少了TaskRecord的一级的管理，直接以ActivityRecord为管理单元，降低管理方面的开销，但是若弱化了Task的概念，结构不够清晰。

## 成员变量

``` java
//Activity的状态
enum ActivityState {
    INITIALIZING,
    RESUMED,
    PAUSING,
    PAUSED,
    STOPPING,
    STOPPED,
    FINISHING,
    DESTROYING,
    DESTROYED
}

final ActivityManagerService mService;//AMS
final boolean mMainStack;//是否为主栈，true
    
final Context mContext;
   
//保存打开的所有的栈    
final ArrayList mHistory = new ArrayList();
    
/**
 * List of running activities, sorted by recent usage.
 * The first entry in the list is the least recently used.
 * It contains HistoryRecord objects.
 */
 //正在运行的活动列，按照最近使用的方式排序。列表中第一个数据是最后使用。
final ArrayList mLRUActivities = new ArrayList();

/**
 * List of activities that are waiting for a new activity
 * to become visible before completing whatever operation they are
 * supposed to do.
 */
final ArrayList<ActivityRecord> mWaitingVisibleActivities
        = new ArrayList<ActivityRecord>();

/**
 * List of activities that are ready to be stopped, but waiting
 * for the next activity to settle down before doing so.  It contains
 * HistoryRecord objects.
 */
final ArrayList<ActivityRecord> mStoppingActivities
        = new ArrayList<ActivityRecord>();

/**
 * Animations that for the current transition have requested not to
 * be considered for the transition animation.
 */
final ArrayList<ActivityRecord> mNoAnimActivities
        = new ArrayList<ActivityRecord>();

/**
 * List of activities that are ready to be finished, but waiting
 * for the previous activity to settle down before doing so.  It contains
 * HistoryRecord objects.
 */
final ArrayList<ActivityRecord> mFinishingActivities
        = new ArrayList<ActivityRecord>();
    
/**
 * List of people waiting to find out about the next launched activity.
 */
final ArrayList<IActivityManager.WaitResult> mWaitingActivityLaunched
        = new ArrayList<IActivityManager.WaitResult>();
    
/**
 * List of people waiting to find out about the next visible activity.
 */
final ArrayList<IActivityManager.WaitResult> mWaitingActivityVisible
        = new ArrayList<IActivityManager.WaitResult>();
    
/**
 * Set when the system is going to sleep, until we have
 * successfully paused the current activity and released our wake lock.
 * At that point the system is allowed to actually sleep.
 */
final PowerManager.WakeLock mGoingToSleep;

/**
 * We don't want to allow the device to go to sleep while in the process
 * of launching an activity.  This is primarily to allow alarm intent
 * receivers to launch an activity and get that to run before the device
 * goes back to sleep.
 */
final PowerManager.WakeLock mLaunchingActivity;

/**
 * When we are in the process of pausing an activity, before starting the
 * next one, this variable holds the activity that is currently being paused.
 */
ActivityRecord mPausingActivity = null;

/**
 * This is the last activity that we put into the paused state.  This is
 * used to determine if we need to do an activity transition while sleeping,
 * when we normally hold the top activity paused.
 */
ActivityRecord mLastPausedActivity = null;

/**
 * Current activity that is resumed, or null if there is none.
 */
ActivityRecord mResumedActivity = null;
    
/**
 * This is the last activity that has been started.  It is only used to
 * identify when multiple activities are started at once so that the user
 * can be warned they may not be in the activity they think they are.
 */
ActivityRecord mLastStartedActivity = null;
    
/**
 * Set when we know we are going to be calling updateConfiguration()
 * soon, so want to skip intermediate config checks.
 */
boolean mConfigWillChange;

/**
 * Set to indicate whether to issue an onUserLeaving callback when a
 * newly launched activity is being brought in front of us.
 */
boolean mUserLeaving = false;
    
long mInitialStartTime = 0;
    
static final int PAUSE_TIMEOUT_MSG = 9;
static final int IDLE_TIMEOUT_MSG = 10;
static final int IDLE_NOW_MSG = 11;
static final int LAUNCH_TIMEOUT_MSG = 16;
static final int DESTROY_TIMEOUT_MSG = 17;
static final int RESUME_TOP_ACTIVITY_MSG = 19;
```

## 常用方法分析

* topRunningActivityLocked

找到栈中第一个与notTop不同的，并且不处于finishing状态的ActivityRecord。当notTop为null
时返回

```java
final ActivityRecord topRunningActivityLocked(ActivityRecord notTop) {
    int i = mHistory.size()-1;
    while (i >= 0) {
        ActivityRecord r = (ActivityRecord)mHistory.get(i);
        if (!r.finishing && r != notTop) {
            return r;
        }
        i--;
    }
    return null;
}
```

* findTaskLocked

```java
/**
 * Returns the first activity (starting from the top of the stack) that
 * is the same as the given activity.  Returns null if no such activity
 * is found.
 */
private ActivityRecord findActivityLocked(Intent intent, ActivityInfo info) {
    ComponentName cls = intent.getComponent();
    if (info.targetActivity != null) {
        cls = new ComponentName(info.packageName, info.targetActivity);
    }

    final int N = mHistory.size();
    //数组尾部向前查找。
    for (int i=(N-1); i>=0; i--) {
        ActivityRecord r = (ActivityRecord)mHistory.get(i);
        if (!r.finishing) {
            if (r.intent.getComponent().equals(cls)) {
               return r;
            }
        }
    }

    return null;
}

```

* findTaskLocked


```java

/**
 * Returns the top activity in any existing task matching the given
 * Intent.  Returns null if no such task is found.
 */
private ActivityRecord findTaskLocked(Intent intent, ActivityInfo info) {
    ComponentName cls = intent.getComponent();
    if (info.targetActivity != null) {
        cls = new ComponentName(info.packageName, info.targetActivity);
    }

    TaskRecord cp = null;

    final int N = mHistory.size();
    for (int i=(N-1); i>=0; i--) {
        ActivityRecord r = (ActivityRecord)mHistory.get(i);
        //r.task != cp 表示不搜索属于同一Task的ActivityRecord。
        if (!r.finishing && r.task != cp
                && r.launchMode != ActivityInfo.LAUNCH_SINGLE_INSTANCE) {
            cp = r.task;
          if (r.task.affinity != null) {
                if (r.task.affinity.equals(info.taskAffinity)) {
                    return r;
                }
            } else if (r.task.intent != null
                    && r.task.intent.getComponent().equals(cls)) {
                 return r;
            } else if (r.task.affinityIntent != null
                    && r.task.affinityIntent.getComponent().equals(cls)) {
                return r;
            }
        }
    }

    return null;
}
```



