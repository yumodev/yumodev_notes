[TOC]
# Lifecycle

## 什么Lifecycle

安卓中Activity和Fragment等等一些组件是具有生命周期的, 在使用自MVP模式中或者自定义的View的时候,也需要使用周期. Lifecycle作为google架构组件的一部分可以让一个对象拥有生命周期.


## Lifecycle的使用

## Lifecycle涉及到的类

* Lifecycle



* LifecycleOwner
    
    Lifecycle的持有者,实现该接口的类持有生命周期,该接口的生命周期的改变被其注册的观察者LifeCycle对象, LifecycleOwner通过其Lifecycle getLifecycle()接口获取内部的Lifecycle对象.
    
* LifecycleObserver
    
    Lifecycle观察者,实现该接口的类,实现该接口的类,通过注解的方式,可以通过被LifecycleOwner类的addObserver方法注册, 被注册后LifecycleObserver便可以观察到LifecycleOwner的生命周期事件.
    

## 定义一个观察者


### LifecycleObserver接口的定义


```java
package android.arch.lifecycle;

/**
 * Marks a class as a LifecycleObserver. It does not have any methods, instead, relies on
 * {@link OnLifecycleEvent} annotated methods.
 * <p>
 * @see Lifecycle Lifecycle - for samples and usage patterns.
 */
@SuppressWarnings("WeakerAccess")
public interface LifecycleObserver {
}

```

这是一个空的接口类.

### 实现LifecycleObserver接口



```java
public class YmLifeCycle implements LifecycleObserver {
    private static final String TAG = YmLifeCycle.class.getSimpleName();

    @OnLifecycleEvent(Lifecycle.Event.ON_CREATE)
    public void create(LifecycleOwner lifecycleOwner) {
        Lifecycle.State currentState = lifecycleOwner.getLifecycle().getCurrentState();
        Log.i(TAG, "create: " + currentState);
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_START)
    public void start(LifecycleOwner lifecycleOwner) {
        Log.i(TAG, "start: " + lifecycleOwner.getLifecycle().getCurrentState());
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
    public void resume(LifecycleOwner lifecycleOwner) {
        Log.i(TAG, "resume: " + lifecycleOwner.getLifecycle().getCurrentState());
    }


    @OnLifecycleEvent(Lifecycle.Event.ON_PAUSE)
    public void pause(LifecycleOwner lifecycleOwner) {
        Log.i(TAG, "pause: " + lifecycleOwner.getLifecycle().getCurrentState());
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_STOP)
    public void stop(LifecycleOwner lifecycleOwner) {
        Log.i(TAG, "stop: " + lifecycleOwner.getLifecycle().getCurrentState());
    }


    @OnLifecycleEvent(Lifecycle.Event.ON_DESTROY)
    public void destroy(LifecycleOwner lifecycleOwner) {
        Log.i(TAG, "destroy: " + lifecycleOwner.getLifecycle().getCurrentState());
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_ANY)
    public void any(LifecycleOwner lifecycleOwner) {
        Log.i(TAG, "any: " + lifecycleOwner.getLifecycle().getCurrentState());
    }
}
```

## 注册LifecycleObserver


```java
public class TestLifeCycle extends YmTestFragment {

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        getLifecycle().addObserver(new YmLifeCycle());
    }
```

### Fragment

Fragment实现了LifecycleOwner接口,该类只有一个返回Lifecycle对象的方法 getLifecycle()
Fragment中getLifecycle()方法返回的是继承了抽象类Lifecycle的LifecycleRegistry。

```java
public class Fragment implements ComponentCallbacks, OnCreateContextMenuListener, LifecycleOwner {

    LifecycleRegistry mLifecycleRegistry = new LifecycleRegistry(this);
    
    @Override
    public Lifecycle getLifecycle() {
        return mLifecycleRegistry;
    }
    
    void performCreate(Bundle savedInstanceState) {
           
        ...
        mLifecycleRegistry.handleLifecycleEvent(Lifecycle.Event.ON_CREATE);
    }
    
    void performStart(Bundle savedInstanceState) {
           
        ...
        mLifecycleRegistry.handleLifecycleEvent(Lifecycle.Event.ON_START);
    }
    
    void performResume(Bundle savedInstanceState) {
           
        ...
        mLifecycleRegistry.handleLifecycleEvent(Lifecycle.Event.ON_RESUME);
    }
    
    void performPause(Bundle savedInstanceState) {
            
        mLifecycleRegistry.handleLifecycleEvent(Lifecycle.Event.ON_PAUSE);
        ...
    }
    
    void performStop(Bundle savedInstanceState) {
            
        mLifecycleRegistry.handleLifecycleEvent(Lifecycle.Event.ON_STOP);
        ...
    }
    
    
    void performDestroy(Bundle savedInstanceState) {
            
        mLifecycleRegistry.handleLifecycleEvent(Lifecycle.Event.ON_DESTROY);
        ...
    }
}
```

### LifecycleOwner


```java

package android.arch.lifecycle;
@SuppressWarnings({"WeakerAccess", "unused"})
public interface LifecycleOwner {
    Lifecycle getLifecycle();
}

```

### Lifecycle

```java

package android.arch.lifecycle;
import android.support.annotation.MainThread;

public abstract class Lifecycle {
    /**
     * Adds a LifecycleObserver that will be notified when the LifecycleOwner changes
     * @param observer The observer to notify.
     */
    @MainThread
    public abstract void addObserver(LifecycleObserver observer);

    /**
     * Removes the given observer from the observers list.
     * @param observer The observer to be removed.
     */
    @MainThread
    public abstract void removeObserver(LifecycleObserver observer);

    /**
     * Returns the current state of the Lifecycle.
     * @return The current state of the Lifecycle.
     */
    @MainThread
    public abstract State getCurrentState();

    @SuppressWarnings("WeakerAccess")
    public enum Event {
        /**
         * Constant for onCreate event of the {@link LifecycleOwner}.
         */
        ON_CREATE,
        /**
         * Constant for onStart event of the {@link LifecycleOwner}.
         */
        ON_START,
        /**
         * Constant for onResume event of the {@link LifecycleOwner}.
         */
        ON_RESUME,
        /**
         * Constant for onPause event of the {@link LifecycleOwner}.
         */
        ON_PAUSE,
        /**
         * Constant for onStop event of the {@link LifecycleOwner}.
         */
        ON_STOP,
        /**
         * Constant for onDestroy event of the {@link LifecycleOwner}.
         */
        ON_DESTROY,
        /**
         * An {@link Event Event} constant that can be used to match all events.
         */
        ON_ANY
    }

    /**
     * Lifecycle states. You can consider the states as the nodes in a graph and
     * {@link Event}s as the edges between these nodes.
     */
    @SuppressWarnings("WeakerAccess")
    public enum State {
        /**
         * Destroyed state for a LifecycleOwner. After this event, this Lifecycle will not dispatch
         * any more events. For instance, for an {@link android.app.Activity}, this state is reached
         * <b>right before</b> Activity's {@link android.app.Activity#onDestroy() onDestroy} call.
         */
        DESTROYED,

        /**
         * Initialized state for a LifecycleOwner. For an {@link android.app.Activity}, this is
         * the state when it is constructed but has not received
         * {@link android.app.Activity#onCreate(android.os.Bundle) onCreate} yet.
         */
        INITIALIZED,

        /**
         * Created state for a LifecycleOwner. For an {@link android.app.Activity}, this state
         * is reached in two cases:
         * <ul>
         *     <li>after {@link android.app.Activity#onCreate(android.os.Bundle) onCreate} call;
         *     <li><b>right before</b> {@link android.app.Activity#onStop() onStop} call.
         * </ul>
         */
        CREATED,

        /**
         * Started state for a LifecycleOwner. For an {@link android.app.Activity}, this state
         * is reached in two cases:
         * <ul>
         *     <li>after {@link android.app.Activity#onStart() onStart} call;
         *     <li><b>right before</b> {@link android.app.Activity#onPause() onPause} call.
         * </ul>
         */
        STARTED,

        /**
         * Resumed state for a LifecycleOwner. For an {@link android.app.Activity}, this state
         * is reached after {@link android.app.Activity#onResume() onResume} is called.
         */
        RESUMED;

        /**
         * Compares if this State is greater or equal to the given {@code state}.
         *
         * @param state State to compare with
         * @return true if this State is greater or equal to the given {@code state}
         */
        public boolean isAtLeast(State state) {
            return compareTo(state) >= 0;
        }
    }
}
```

### LifecycleRegistry

* LifecycleRegistry中定义嵌套类ObserverWithState，该类持有GenericLifecycleObserver对象，而GenericLifecycleObserver是继承了LifecycleObserver的接口。
* 我们在Fragment（AppCompatActivity也一样）中调用getLifecycle()方法得到LifecycleRegistry对象，然后调用addObserver()方法并将实现了LifecycleObserver接口的对象作为参数传进去。这样一个过程就完成了注册监听的过程。

* 后续就是Fragment生命周期变化时，通知LifecycleObserver的过程：Fragment的performXXX()、onXXX()方法；LifecycleRegistry的handleLifecycleEvent()方法；LifecycleObserver的onXXX()方法。

* 如果你细心点看上面的时序图，你会发现Fragment中performCreate()、performStart()、performResume()会先调用自身的onXXX()方法，然后再调用LifecycleRegistry的handleLifecycleEvent()方法；而在performPause()、performStop()、performDestroy()中会先LifecycleRegistry的handleLifecycleEvent()方法 ，然后调用自身的onXXX()方法。比如上面的例子中，打印出来的结果也确实符合我们的分析，打印结果如下：


```java
/**
 * An implementation of {@link Lifecycle} that can handle multiple observers.
 * <p>
 * It is used by Fragments and Support Library Activities. You can also directly use it if you have
 * a custom LifecycleOwner.
 */
public class LifecycleRegistry extends Lifecycle {

    /**
     * Custom list that keeps observers and can handle removals / additions during traversal.
     *
     * Invariant: at any moment of time for observer1 & observer2:
     * if addition_order(observer1) < addition_order(observer2), then
     * state(observer1) >= state(observer2),
     */
    private FastSafeIterableMap<LifecycleObserver, ObserverWithState> mObserverMap =
            new FastSafeIterableMap<>();
    /**
     * Current state
     */
    private State mState;
    /**
     * The provider that owns this Lifecycle.
     */
    private final LifecycleOwner mLifecycleOwner;

    private int mAddingObserverCounter = 0;

    private boolean mHandlingEvent = false;
    private boolean mNewEventOccurred = false;

    // we have to keep it for cases:
    // void onStart() {
    //     mRegistry.removeObserver(this);
    //     mRegistry.add(newObserver);
    // }
    // newObserver should be brought only to CREATED state during the execution of
    // this onStart method. our invariant with mObserverMap doesn't help, because parent observer
    // is no longer in the map.
    private ArrayList<State> mParentStates = new ArrayList<>();

    /**
     * Creates a new LifecycleRegistry for the given provider.
     * <p>
     * You should usually create this inside your LifecycleOwner class's constructor and hold
     * onto the same instance.
     *
     * @param provider The owner LifecycleOwner
     */
    public LifecycleRegistry(@NonNull LifecycleOwner provider) {
        mLifecycleOwner = provider;
        mState = INITIALIZED;
    }

    /**
     * Only marks the current state as the given value. It doesn't dispatch any event to its
     * listeners.
     *
     * @param state new state
     */
    @SuppressWarnings("WeakerAccess")
    public void markState(State state) {
        mState = state;
    }

    /**
     * Sets the current state and notifies the observers.
     * <p>
     * Note that if the {@code currentState} is the same state as the last call to this method,
     * calling this method has no effect.
     *
     * @param event The event that was received
     */
    public void handleLifecycleEvent(Lifecycle.Event event) {
        mState = getStateAfter(event);
        if (mHandlingEvent || mAddingObserverCounter != 0) {
            mNewEventOccurred = true;
            // we will figure out what to do on upper level.
            return;
        }
        mHandlingEvent = true;
        sync();
        mHandlingEvent = false;
    }

    private boolean isSynced() {
        if (mObserverMap.size() == 0) {
            return true;
        }
        State eldestObserverState = mObserverMap.eldest().getValue().mState;
        State newestObserverState = mObserverMap.newest().getValue().mState;
        return eldestObserverState == newestObserverState && mState == newestObserverState;
    }

    private State calculateTargetState(LifecycleObserver observer) {
        Entry<LifecycleObserver, ObserverWithState> previous = mObserverMap.ceil(observer);

        State siblingState = previous != null ? previous.getValue().mState : null;
        State parentState = !mParentStates.isEmpty() ? mParentStates.get(mParentStates.size() - 1)
                : null;
        return min(min(mState, siblingState), parentState);
    }

    @Override
    public void addObserver(LifecycleObserver observer) {
        State initialState = mState == DESTROYED ? DESTROYED : INITIALIZED;
        ObserverWithState statefulObserver = new ObserverWithState(observer, initialState);
        ObserverWithState previous = mObserverMap.putIfAbsent(observer, statefulObserver);

        if (previous != null) {
            return;
        }

        boolean isReentrance = mAddingObserverCounter != 0 || mHandlingEvent;

        State targetState = calculateTargetState(observer);
        mAddingObserverCounter++;
        while ((statefulObserver.mState.compareTo(targetState) < 0
                && mObserverMap.contains(observer))) {
            pushParentState(statefulObserver.mState);
            statefulObserver.dispatchEvent(mLifecycleOwner, upEvent(statefulObserver.mState));
            popParentState();
            // mState / subling may have been changed recalculate
            targetState = calculateTargetState(observer);
        }

        if (!isReentrance) {
            // we do sync only on the top level.
            sync();
        }
        mAddingObserverCounter--;
    }

    private void popParentState() {
        mParentStates.remove(mParentStates.size() - 1);
    }

    private void pushParentState(State state) {
        mParentStates.add(state);
    }

    @Override
    public void removeObserver(LifecycleObserver observer) {
        // we consciously decided not to send destruction events here in opposition to addObserver.
        // Our reasons for that:
        // 1. These events haven't yet happened at all. In contrast to events in addObservers, that
        // actually occurred but earlier.
        // 2. There are cases when removeObserver happens as a consequence of some kind of fatal
        // event. If removeObserver method sends destruction events, then a clean up routine becomes
        // more cumbersome. More specific example of that is: your LifecycleObserver listens for
        // a web connection, in the usual routine in OnStop method you report to a server that a
        // session has just ended and you close the connection. Now let's assume now that you
        // lost an internet and as a result you removed this observer. If you get destruction
        // events in removeObserver, you should have a special case in your onStop method that
        // checks if your web connection died and you shouldn't try to report anything to a server.
        mObserverMap.remove(observer);
    }

    /**
     * The number of observers.
     *
     * @return The number of observers.
     */
    @SuppressWarnings("WeakerAccess")
    public int getObserverCount() {
        return mObserverMap.size();
    }

    @Override
    public State getCurrentState() {
        return mState;
    }

    static State getStateAfter(Event event) {
        switch (event) {
            case ON_CREATE:
            case ON_STOP:
                return CREATED;
            case ON_START:
            case ON_PAUSE:
                return STARTED;
            case ON_RESUME:
                return RESUMED;
            case ON_DESTROY:
                return DESTROYED;
            case ON_ANY:
                break;
        }
        throw new IllegalArgumentException("Unexpected event value " + event);
    }

    private static Event downEvent(State state) {
        switch (state) {
            case INITIALIZED:
                throw new IllegalArgumentException();
            case CREATED:
                return ON_DESTROY;
            case STARTED:
                return ON_STOP;
            case RESUMED:
                return ON_PAUSE;
            case DESTROYED:
                throw new IllegalArgumentException();
        }
        throw new IllegalArgumentException("Unexpected state value " + state);
    }

    private static Event upEvent(State state) {
        switch (state) {
            case INITIALIZED:
            case DESTROYED:
                return ON_CREATE;
            case CREATED:
                return ON_START;
            case STARTED:
                return ON_RESUME;
            case RESUMED:
                throw new IllegalArgumentException();
        }
        throw new IllegalArgumentException("Unexpected state value " + state);
    }

    private void forwardPass() {
        Iterator<Entry<LifecycleObserver, ObserverWithState>> ascendingIterator =
                mObserverMap.iteratorWithAdditions();
        while (ascendingIterator.hasNext() && !mNewEventOccurred) {
            Entry<LifecycleObserver, ObserverWithState> entry = ascendingIterator.next();
            ObserverWithState observer = entry.getValue();
            while ((observer.mState.compareTo(mState) < 0 && !mNewEventOccurred
                    && mObserverMap.contains(entry.getKey()))) {
                pushParentState(observer.mState);
                observer.dispatchEvent(mLifecycleOwner, upEvent(observer.mState));
                popParentState();
            }
        }
    }

    private void backwardPass() {
        Iterator<Entry<LifecycleObserver, ObserverWithState>> descendingIterator =
                mObserverMap.descendingIterator();
        while (descendingIterator.hasNext() && !mNewEventOccurred) {
            Entry<LifecycleObserver, ObserverWithState> entry = descendingIterator.next();
            ObserverWithState observer = entry.getValue();
            while ((observer.mState.compareTo(mState) > 0 && !mNewEventOccurred
                    && mObserverMap.contains(entry.getKey()))) {
                Event event = downEvent(observer.mState);
                pushParentState(getStateAfter(event));
                observer.dispatchEvent(mLifecycleOwner, event);
                popParentState();
            }
        }
    }

    // happens only on the top of stack (never in reentrance),
    // so it doesn't have to take in account parents
    private void sync() {
        while (!isSynced()) {
            mNewEventOccurred = false;
            // no need to check eldest for nullability, because isSynced does it for us.
            if (mState.compareTo(mObserverMap.eldest().getValue().mState) < 0) {
                backwardPass();
            }
            Entry<LifecycleObserver, ObserverWithState> newest = mObserverMap.newest();
            if (!mNewEventOccurred && newest != null
                    && mState.compareTo(newest.getValue().mState) > 0) {
                forwardPass();
            }
        }
        mNewEventOccurred = false;
    }

    static State min(@NonNull State state1, @Nullable State state2) {
        return state2 != null && state2.compareTo(state1) < 0 ? state2 : state1;
    }

    static class ObserverWithState {
        State mState;
        GenericLifecycleObserver mLifecycleObserver;

        ObserverWithState(LifecycleObserver observer, State initialState) {
            mLifecycleObserver = Lifecycling.getCallback(observer);
            mState = initialState;
        }

        void dispatchEvent(LifecycleOwner owner, Event event) {
            State newState = getStateAfter(event);
            mState = min(mState, newState);
            mLifecycleObserver.onStateChanged(owner, event);
            mState = newState;
        }
    }
}
```




