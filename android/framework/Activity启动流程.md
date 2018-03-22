## startActivity 

startActivity时进入startActivityForResult方法，然后调用
Instarumentation开启一个Activity。
Instarumentation应用程序和系统之间的操作。

```java
@Override
public void startActivity(Intent intent) {
    startActivityForResult(intent, -1);
}

public void startActivityForResult(Intent intent, int requestCode) {
    if (mParent == null) {
        Instrumentation.ActivityResult ar =
            mInstrumentation.execStartActivity(
                this, mMainThread.getApplicationThread(), mToken, this,
                intent, requestCode);
        if (ar != null) {
            mMainThread.sendActivityResult(
                mToken, mEmbeddedID, requestCode, ar.getResultCode(),
                ar.getResultData());
        }
        if (requestCode >= 0) {
           mStartedActivity = true;
        }
    } else {
        mParent.startActivityFromChild(this, intent, requestCode);
    }
}
  
```

## Instrumentation execStartActivity

```java

  public ActivityResult execStartActivity(
        Context who, IBinder contextThread, IBinder token, Activity target,
        Intent intent, int requestCode) {
        //转换为IApplicaiton
        IApplicationThread whoThread = (IApplicationThread) contextThread;
        if (mActivityMonitors != null) {
            synchronized (mSync) {
                final int N = mActivityMonitors.size();
                for (int i=0; i<N; i++) {
                    final ActivityMonitor am = mActivityMonitors.get(i);
                    if (am.match(who, null, intent)) {
                        am.mHits++;
                        if (am.isBlocking()) {
                            return requestCode >= 0 ? am.getResult() : null;
                        }
                        break;
                    }
                }
            }
        }
        try {
            int result = ActivityManagerNative.getDefault()
                .startActivity(whoThread, intent,
                        intent.resolveTypeIfNeeded(who.getContentResolver()),
                        null, 0, token, target != null ? target.mEmbeddedID : null,
                        requestCode, false, false);
            checkStartActivityResult(result, intent);
        } catch (RemoteException e) {
        }
        return null;
    }
```

## ActivityManagerNative.getDefault()

获取ActivityManagerService的代理对象。

```java

static public IActivityManager asInterface(IBinder obj)
{
    if (obj == null) {
        return null;
    }
    IActivityManager in =
        (IActivityManager)obj.queryLocalInterface(descriptor);
    if (in != null) {
        return in;
    }
    
    return new ActivityManagerProxy(obj);
}

static public IActivityManager getDefault()
{
    if (gDefault != null) {
        //if (Config.LOGV) Log.v(
        //    "ActivityManager", "returning cur default = " + gDefault);
        return gDefault;
    }
    IBinder b = ServiceManager.getService("activity");
    if (Config.LOGV) Log.v(
        "ActivityManager", "default service binder = " + b);
    gDefault = asInterface(b);
    if (Config.LOGV) Log.v(
        "ActivityManager", "default service = " + gDefault);
    return gDefault;
}
```

## ActivityManagerProxy.startActivity


```java
class ActivityManagerProxy implements IActivityManager
{
    public ActivityManagerProxy(IBinder remote)
    {
        mRemote = remote;
    }
    
    public IBinder asBinder()
    {
        return mRemote;
    }
    
    public int startActivity(IApplicationThread caller, Intent intent,
            String resolvedType, Uri[] grantedUriPermissions, int grantedMode,
            IBinder resultTo, String resultWho,
            int requestCode, boolean onlyIfNeeded,
            boolean debug) throws RemoteException {
        Parcel data = Parcel.obtain();
        Parcel reply = Parcel.obtain();
        data.writeInterfaceToken(IActivityManager.descriptor);
        //源App的Application。
        data.writeStrongBinder(caller != null ? caller.asBinder() : null);
        intent.writeToParcel(data, 0);
        data.writeString(resolvedType);
        data.writeTypedArray(grantedUriPermissions, 0);
        data.writeInt(grantedMode);
        data.writeStrongBinder(resultTo);
        data.writeString(resultWho);
        data.writeInt(requestCode);
        data.writeInt(onlyIfNeeded ? 1 : 0);
        data.writeInt(debug ? 1 : 0);
        //通过Binder对象，像ActivityManagerService发送一个START_ACTIVITY_TRanSACTION
        mRemote.transact(START_ACTIVITY_TRANSACTION, data, reply, 0);
        reply.readException();
        int result = reply.readInt();
        reply.recycle();
        data.recycle();
        return result;
    }
 }
```

##ActivityManagerService.startActivity

```java
public final int startActivity(IApplicationThread caller,
        Intent intent, String resolvedType, Uri[] grantedUriPermissions,
        int grantedMode, IBinder resultTo,
        String resultWho, int requestCode, boolean onlyIfNeeded,
        boolean debug) {
    return mMainStack.startActivityMayWait(caller, intent, resolvedType,
            grantedUriPermissions, grantedMode, resultTo, resultWho,
            requestCode, onlyIfNeeded, debug, null, null);
}
```

