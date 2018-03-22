# Glide3.7解析

## Glide.with
Glide.with是一组静态方法，传入的参数有context，activity，FragmentActivity，fragment
等等

width方法中首先获取RequestManagerRetriever的实例，其实一个单例模式。传入参数生成RequestManger对象。

```java
public static RequestManager with(Context context) {
    RequestManagerRetriever retriever = RequestManagerRetriever.get();
    return retriever.get(context);
}

   
public static RequestManager with(Activity activity) {
    RequestManagerRetriever retriever = RequestManagerRetriever.get();
    return retriever.get(activity);
}

  
public static RequestManager with(FragmentActivity activity) {
    RequestManagerRetriever retriever = RequestManagerRetriever.get();
    return retriever.get(activity);
}

@TargetApi(Build.VERSION_CODES.HONEYCOMB)
public static RequestManager with(android.app.Fragment fragment) {
    RequestManagerRetriever retriever = RequestManagerRetriever.get();
    return retriever.get(fragment);
}

   
public static RequestManager with(Fragment fragment) {
    RequestManagerRetriever retriever = RequestManagerRetriever.get();
    return retriever.get(fragment);
}
```

* RequestManagerRetriever.get


```java
  public RequestManager get(Context context) {
    //为空报异常。
    if (context == null) {
        throw new IllegalArgumentException("You cannot start a load on a null Context");
    } else if (Util.isOnMainThread() && !(context instanceof Application)) {
        //如果在主线程且不是Application，那么转为Activity调用。
        if (context instanceof FragmentActivity) {
            return get((FragmentActivity) context);
        } else if (context instanceof Activity) {
            return get((Activity) context);
        } else if (context instanceof ContextWrapper) {
            return get(((ContextWrapper) context).getBaseContext());
        }
    }

    return getApplicationManager(context);
}



public RequestManager get(FragmentActivity activity) {
    if (Util.isOnBackgroundThread()) { 
        //如果在线程中调用，就调用get(context。)
        return get(activity.getApplicationContext());
    } else {
        assertNotDestroyed(activity);
        FragmentManager fm = activity.getSupportFragmentManager();
        return supportFragmentGet(activity, fm);
    }
}

public RequestManager get(Fragment fragment) {
    if (fragment.getActivity() == null) {
        throw new IllegalArgumentException("You cannot start a load on a fragment before it is attached");
    }
    if (Util.isOnBackgroundThread()) {
        return get(fragment.getActivity().getApplicationContext());
    } else {
        FragmentManager fm = fragment.getChildFragmentManager();
        return supportFragmentGet(fragment.getActivity(), fm);
    }
}

@TargetApi(Build.VERSION_CODES.HONEYCOMB)
public RequestManager get(Activity activity) {
    if (Util.isOnBackgroundThread() || Build.VERSION.SDK_INT < Build.VERSION_CODES.HONEYCOMB) {
        return get(activity.getApplicationContext());
    } else {
        assertNotDestroyed(activity);
        android.app.FragmentManager fm = activity.getFragmentManager();
        return fragmentGet(activity, fm);
    }
}
```


* RequestManagerRetriever.getApplicaitonManager 

```java
private RequestManager getApplicationManager(Context context) {
      
    if (applicationManager == null) {
        synchronized (this) {
            if (applicationManager == null) {
               
                applicationManager = new RequestManager(context.getApplicationContext(),
                        new ApplicationLifecycle(), new EmptyRequestManagerTreeNode());
            }
        }
    }

    return applicationManager;
}
```

* RequestManagerRetriever.fragmentGet()

创建了空的Fragment，进行声明周期的管理。
```java

 @TargetApi(Build.VERSION_CODES.HONEYCOMB)
 RequestManager fragmentGet(Context context, android.app.FragmentManager fm) {
    RequestManagerFragment current = getRequestManagerFragment(fm);
    RequestManager requestManager = current.getRequestManager();
    if (requestManager == null) {
        requestManager = new RequestManager(context, current.getLifecycle(), current.getRequestManagerTreeNode());
        current.setRequestManager(requestManager);
    }
    return requestManager;
}

RequestManager supportFragmentGet(Context context, FragmentManager fm) {
    SupportRequestManagerFragment current = getSupportRequestManagerFragment(fm);
    RequestManager requestManager = current.getRequestManager();
    if (requestManager == null) {
        requestManager = new RequestManager(context, current.getLifecycle(), current.getRequestManagerTreeNode());
        current.setRequestManager(requestManager);
    }
    return requestManager;
}

 @TargetApi(Build.VERSION_CODES.JELLY_BEAN_MR1)
    RequestManagerFragment getRequestManagerFragment(final android.app.FragmentManager fm) {
    RequestManagerFragment current = (RequestManagerFragment) fm.findFragmentByTag(FRAGMENT_TAG);
    if (current == null) {
        current = pendingRequestManagerFragments.get(fm);
        if (current == null) {
            current = new RequestManagerFragment();
            pendingRequestManagerFragments.put(fm, current);
            fm.beginTransaction().add(current, FRAGMENT_TAG).commitAllowingStateLoss();
            handler.obtainMessage(ID_REMOVE_FRAGMENT_MANAGER, fm).sendToTarget();
        }
    }
    return current;
}

  SupportRequestManagerFragment getSupportRequestManagerFragment(final FragmentManager fm) {
    SupportRequestManagerFragment current = (SupportRequestManagerFragment) fm.findFragmentByTag(
        FRAGMENT_TAG);
    if (current == null) {
        current = pendingSupportRequestManagerFragments.get(fm);
        if (current == null) {
            current = new SupportRequestManagerFragment();
            pendingSupportRequestManagerFragments.put(fm, current);
            fm.beginTransaction().add(current, FRAGMENT_TAG).commitAllowingStateLoss();
            handler.obtainMessage(ID_REMOVE_SUPPORT_FRAGMENT_MANAGER, fm).sendToTarget();
        }
    }
    return current;
}

```

