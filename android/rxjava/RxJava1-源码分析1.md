# RxJava的源码解析

## 一个常规的RxJava的使用

通过RxJava循环打印20个数字。

1. 创建一个被观察者 Observable
2. 创建一个观察者Observer或者Subscriber
3. 被观察者订阅观察者。

```java

  Observable<String> observable = Observable.create(new    Observable.OnSubscribe<String>(){

        @Override
        public void call(Subscriber<? super String> subscriber) {
            for (int i = 0; i < 20; i++){
                subscriber.onNext(" i = "+i);
            }
            subscriber.onCompleted();
        }
    });

    Subscriber<String> subscriber = new Subscriber<String>() {
        @Override
        public void onCompleted() {
            Log.i(LOG_TAG, "onCompleted");
       }

        @Override
        public void onError(Throwable e) {
            Log.i(LOG_TAG, "onError");
        }

        @Override
        public void onNext(String s) {
            Log.i(LOG_TAG, "onNext:"+s);            }
    };

    observable.subscribe(subscriber);
```


## 源码解析

### 创建被观察者
主要工作
1. 构造了onSubscribe.
2.

```java
@Deprecated
public static <T> Observable<T> create(OnSubscribe<T> f) {
    return new Observable<T>(RxJavaHooks.onCreate(f));
}

protected Observable(OnSubscribe<T> f) {
    this.onSubscribe = f;
}
```

* RxJavaHooks.onCreate


```java
 @SuppressWarnings({ "rawtypes", "unchecked" })
public static <T> Observable.OnSubscribe<T> onCreate(Observable.OnSubscribe<T> onSubscribe) {
Func1<Observable.OnSubscribe, Observable.OnSubscribe> f = onObservableCreate;
if (f != null) {
    return f.call(onSubscribe);
}
return onSubscribe;
}

```

### 创建观察者

#### Subscriber类的介绍

* Subscriber定义

是一个抽象类，所有要继承才可以实现。
继承了Observer和Subscription接口

```java
public abstract class Subscriber<T> implements Observer<T>, Subscription
```

#### Observer的定义


```java
public interface Observer<T> {
    
    void onCompleted();

 
    void onError(Throwable e);

    void onNext(T t);

}

```

#### Subscription定义


```java
public interface Subscription {
	//解绑
   void unsubscribe();
  //是否已经解绑
   boolean isUnsubscribed();
}
```

#### Subscriber成员

```java
// represents requested not set yet
private static final long NOT_SET = Long.MIN_VALUE;
//存放被观察者列表。
private final SubscriptionList subscriptions;
private final Subscriber<?> subscriber;
/* protected by `this` */
private Producer producer;
/* protected by `this` */
private long requested = NOT_SET; // default to not set
```


## 订阅


```java
public final Subscription subscribe(Subscriber<? super T> subscriber) {
    return Observable.subscribe(subscriber, this);
}
//静态方法
static <T> Subscription subscribe(Subscriber<? super T> subscriber, Observable<T> observable) {
        if (subscriber == null) {
            throw new IllegalArgumentException("subscriber can not be null");
        }
        if (observable.onSubscribe == null) {
            throw new IllegalStateException("onSubscribe function can not be null.");
      }

        //调用Subscriber的onStart方法，空方法有开发者自己实现。
        subscriber.onStart();
        //将传入的普通Subscriber包装成为SafeSubscriber
        //这里用到了装饰器设计模式。
        if (!(subscriber instanceof SafeSubscriber)) {
            // assign to `observer` so we return the protected version
            subscriber = new SafeSubscriber<T>(subscriber);
        }

     
        try {
            // allow the hook to intercept and/or decorate
            RxJavaHooks.onObservableStart(observable, observable.onSubscribe).call(subscriber);
            return RxJavaHooks.onObservableReturn(subscriber);
        } catch (Throwable e) {
            //处理异常
            Exceptions.throwIfFatal(e);
            // in case the subscriber can't listen to exceptions anymore
            if (subscriber.isUnsubscribed()) {
                RxJavaHooks.onError(RxJavaHooks.onObservableError(e));
            } else {
                try {
                    subscriber.onError(RxJavaHooks.onObservableError(e));
                } catch (Throwable e2) {
                    Exceptions.throwIfFatal(e2);
                    RuntimeException r = new OnErrorFailedException("Error occurred attempting to subscribe [" + e.getMessage() + "] and then again while trying to pass to onError.", e2);
                   
                    RxJavaHooks.onObservableError(r);
                   
                    throw r; // NOPMD
                }
            }
            return Subscriptions.unsubscribed();
        }
    }

```

* RxJavaBooks

```Java

 public static <T> Observable.OnSubscribe<T> onObservableStart(Observable<T> instance, Observable.OnSubscribe<T> onSubscribe) {
 //如果定义Func2方法，就调用fun2,否则调用onSubscribe。
    Func2<Observable, Observable.OnSubscribe, Observable.OnSubscribe> f = onObservableStart;
    if (f != null) {
        return f.call(instance, onSubscribe);
    }
    return onSubscribe;
}


/**
 * Hook to call before the Observable.subscribe() method is about to return a Subscription.
 * @param subscription the original subscription
 * @return the original or alternative subscription that will be returned
 */
public static Subscription onObservableReturn(Subscription subscription) {
    Func1<Subscription, Subscription> f = onObservableReturn;
    if (f != null) {
        return f.call(subscription);
    }
    return subscription;
}
```



