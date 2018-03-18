# RxJava1源码分析-map

## map

map操作符的主要作用就是数据转换

```java

String[] str = {"a", "b", "c"};
Observable.from(str)
    .map(new Func1<String, Integer>() {
        @Override
        public Integer call(String s) {
            return Integer.valueOf(s);
        }
    })
    .subscribe(new Action1<Integer>() {
        @Override
        public void call(Integer integer) {
            Log.i("map", integer+"");
        }
    });
```

## from操作符

from操作符可以读取一个数组，然后一个一个的发送数据

```java
public static <T> Observable<T> from(T[] array) {
    int n = array.length;
    if (n == 0) {
        return empty();
    } else
    if (n == 1) {
        return just(array[0]);
    }
    return unsafeCreate(new OnSubscribeFromArray<T>(array));
}

public static <T> Observable<T> unsafeCreate(OnSubscribe<T> f) {
    return new Observable<T>(RxJavaHooks.onCreate(f));
}

```

## map

map方面里面新创建了一个Observable和OnSubscribe。

```java
 public final <R> Observable<R> map(Func1<? super T, ? extends R> func) {
    return unsafeCreate(new OnSubscribeMap<T, R>(this, func));
}
```

### OnSubscribeMap

一个OnSubscribe的子类，主要接受了Func1方法，这也是RxJava是函数式编程的由来。
这个类主要功能新建自己接收器，转换数据后，在调原来的接收器去传递数据。

```java
public final class OnSubscribeMap<T, R> implements OnSubscribe<R> {

    //源发射器
    final Observable<T> source;

    //数据转换方法
    final Func1<? super T, ? extends R> transformer;

    public OnSubscribeMap(Observable<T> source, Func1<? super T, ? extends R> transformer) {
        this.source = source;
        this.transformer = transformer;
    }

    //OnSubscripe接口的方法，发射数据。
    @Override
    public void call(final Subscriber<? super R> o) {
        MapSubscriber<T, R> parent = new MapSubscriber<T, R>(o, transformer);
        o.add(parent);
        //源发射器订阅MapSubscriber。
        source.unsafeSubscribe(parent);
    }

    //定义了Map的接收器
    //接收源发射器的数据，然后调用map方面进行转换，然后调用新的接收器处理数据。
    static final class MapSubscriber<T, R> extends Subscriber<T> {

        final Subscriber<? super R> actual;

        final Func1<? super T, ? extends R> mapper;

        boolean done;

        public MapSubscriber(Subscriber<? super R> actual, Func1<? super T, ? extends R> mapper) {
            //源接收器
            this.actual = actual;
            this.mapper = mapper;
        }

        //接收到数据后第数据进行转换。
        //数据接收完成以后，要通过源发发射器在发过去。
        @Override
        public void onNext(T t) {
            R result;

            try {
                result = mapper.call(t);
            } catch (Throwable ex) {
                Exceptions.throwIfFatal(ex);
                unsubscribe();
                onError(OnErrorThrowable.addValueAsLastCause(ex, t));
                return;
            }

            actual.onNext(result);
        }

        @Override
        public void onError(Throwable e) {
            if (done) {
                RxJavaHooks.onError(e);
                return;
            }
            done = true;

            actual.onError(e);
        }


        @Override
        public void onCompleted() {
            if (done) {
                return;
            }
            actual.onCompleted();
        }

        @Override
        public void setProducer(Producer p) {
            actual.setProducer(p);
        }
    }

}

```

