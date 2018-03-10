# CopyOnWriteArrayList

CopyOnWriteArrayList是一个线程安全集合，原理简单的就是，保证线程安全的前提下，牺牲掉写操作的效率，来保证读操作的高效。通过复制的方式完成对数据的修改，在修改数据的时候，赋值一份熟练出去出来，

## 插入数据



```java

//先获取锁，然后赋值一份当前数组，将数据插入到新数组的尾部，然后新数组修改为老数组。
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}

public void add(int index, E element) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        if (index > len || index < 0)
            throw new IndexOutOfBoundsException("Index: "+index+
                                                ", Size: "+len);
        Object[] newElements;
        int numMoved = len - index;
        if (numMoved == 0)
            newElements = Arrays.copyOf(elements, len + 1);
        else {
            newElements = new Object[len + 1];
            System.arraycopy(elements, 0, newElements, 0, index);
            System.arraycopy(elements, index, newElements, index + 1,
                             numMoved);
        }
        newElements[index] = element;
        setArray(newElements);
    } finally {
        lock.unlock();
    }
}
```

## 参考

* [Java集合干货——CopyOnWriteArrayList源码分析](https://juejin.im/post/5a64688651882573282186d5)

