# RxJava1多线程

## Schedulesrs

* Schedulers.immediate
* Schedulers.newThread
* Schedulers.io
* Schedulers.computation
* AndroidSchedulers.mainThread()

## 线程控制

* subscribeOn()
  事件产生所在的线程。只调用一次
 
* observeOn()
  事件消费所在的线程。即Subscriber所在的线程，可以调用多次。
  
  
## subscribeOn
  
  
```java
public final Observable<T> subscribeOn(Scheduler scheduler) {
        return subscribeOn(scheduler, !(this.onSubscribe instanceof OnSubscribeCreate));
    }
    
public final Observable<T> subscribeOn(Scheduler scheduler, boolean requestOn) {
        if (this instanceof ScalarSynchronousObservable) {
            return ((ScalarSynchronousObservable<T>)this).scalarScheduleOn(scheduler);
        }
        return unsafeCreate(new OperatorSubscribeOn<T>(this, scheduler, requestOn));
    }


```


* OperatorSubscribeOn



```java
public final class OperatorSubscribeOn<T> implements OnSubscribe<T> {

    final Scheduler scheduler;
    final Observable<T> source;
    final boolean requestOn;

    public OperatorSubscribeOn(Observable<T> source, Scheduler scheduler, boolean requestOn) {
        this.scheduler = scheduler;
        this.source = source;
        this.requestOn = requestOn;
    }

    @Override
    public void call(final Subscriber<? super T> subscriber) {
        //用于线程控制。
        final Worker inner = scheduler.createWorker();

        SubscribeOnSubscriber<T> parent = new SubscribeOnSubscriber<T>(subscriber, requestOn, inner, source);
        subscriber.add(parent);
        subscriber.add(inner);

        inner.schedule(parent);
    }

    static final class SubscribeOnSubscriber<T> extends Subscriber<T> implements Action0 {

        final Subscriber<? super T> actual;

        final boolean requestOn;

        final Worker worker;

        Observable<T> source;

        Thread t;

        SubscribeOnSubscriber(Subscriber<? super T> actual, boolean requestOn, Worker worker, Observable<T> source) {
            this.actual = actual;
            this.requestOn = requestOn;
            this.worker = worker;
            this.source = source;
        }

        @Override
        public void onNext(T t) {
            actual.onNext(t);
        }

        @Override
        public void onError(Throwable e) {
            try {
                actual.onError(e);
            } finally {
                worker.unsubscribe();
            }
        }

        @Override
        public void onCompleted() {
            try {
                actual.onCompleted();
            } finally {
                worker.unsubscribe();
            }
        }

        @Override
        public void call() {
            Observable<T> src = source;
            source = null;
            t = Thread.currentThread();
            src.unsafeSubscribe(this);
        }

        @Override
        public void setProducer(final Producer p) {
            actual.setProducer(new Producer() {
                @Override
                public void request(final long n) {
                    if (t == Thread.currentThread() || !requestOn) {
                        p.request(n);
                    } else {
                        worker.schedule(new Action0() {
                            @Override
                            public void call() {
                                p.request(n);
                            }
                        });
                    }
                }
            });
        }
    }
}
```

## 

