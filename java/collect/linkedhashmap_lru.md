# LinkedHashMap的使用以及LRU算法的实现

## 什么是LinkedHashMap

LinkedHashMap是HashMap的子类，能够记住插入数据的顺序，由于HashMap并不能保证插入数据的顺序，如果我们需要记住插入的顺序的就需要使用LinkedHashMap.

## LinkedHashMap的顺序

LinkedHashMap提供两种数据排序的方式，一种是按照插入顺序进行排序，一种按照访问顺序进行排序。

在LinkedHashMap中定义了变量`final boolean accessOrder;`来标记使用哪种访问方式。由于是final类型的变量，所以它只能在构造方法中赋值，然后就不能再改变数据的排序方式。

下面是一个例子来演示这两种排序方式。输入输入`{0,0}...{9,9}`,然后访问数据，查看数据的排序方式。

### 按照插入顺序排序


```
  System.out.println("开始插入顺序的测试");
   //accessOrder 为true，按照访问顺序排序，false：按照插入顺序排序
   LinkedHashMap<Integer, Integer> map = new LinkedHashMap<>(0, 0.75f, false);
   for (int i = 0; i < 10; i ++){
       map.put(i, i);
   }

   System.out.println(map.toString());

   System.out.println(map.get(6));

   System.out.println(map.toString());
   System.out.println("结束插入顺序的测试");
```

输出结果如下：

```
{0=0, 1=1, 2=2, 3=3, 4=4, 5=5, 6=6, 7=7, 8=8, 9=9}
6
{0=0, 1=1, 2=2, 3=3, 4=4, 5=5, 6=6, 7=7, 8=8, 9=9}
```

### 按照访问顺序排序


```
  System.out.println("开始访问顺序的测试");
   //accessOrder 为true，按照访问顺序排序，false：按照插入顺序排序
   LinkedHashMap<Integer, Integer> map = new LinkedHashMap<>(0, 0.75f, true);
   for (int i = 0; i < 10; i ++){
       map.put(i, i);
   }

   System.out.println(map.toString());

   System.out.println(map.get(6));

   System.out.println(map.toString());
   System.out.println("结束访问顺序的测试");
```

输出结果如下:

```
开始访问顺序的测试
{0=0, 1=1, 2=2, 3=3, 4=4, 5=5, 6=6, 7=7, 8=8, 9=9}
6
{0=0, 1=1, 2=2, 3=3, 4=4, 5=5, 7=7, 8=8, 9=9, 6=6}
结束访问顺序的测试
```

## 什么是LRU算法

LRU是Lease Recently User的缩写，意思是最近 最少使用。比如设计一个文件缓存系统，每个文件有自己的大小和访问时间，文件缓存系统有总的大小，当往这个文件系统中放入新的文件时，如果发现超出文件缓存系统的容量，那么把访问时间最旧的文件删掉。所有定义这样一个文件系统的需要记录文件的访问顺序，而通过上面的例子我们知道LinkedHashMap可以轻松实现这个功能。


## 实现一个最简单的LRU算法

这个算法实现最多存放maxSize个数据，当存放数据的个数超出maxSize时，删除最近最少使用的数据。

```
//map的最大容量是10
final int maxSize = 10;
Map<Integer, Integer> map = new LinkedHashMap<Integer, Integer>(0, 0.75f, true) {
  @Override
  protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
      return size() > maxSize;
  }
};

System.out.println(map.size());
for (int i = 0; i < 10; i ++){
  map.put(i, i);
}
System.out.println("当前有10个数据："+map.toString());
System.out.println(map.get(6));
System.out.println("访问key为6的数据后："+map.toString());
map.put(11,11);
System.out.println("插入第11个数据后："+map.toString());
```

输出结果如下：

```
当前有10个数据：{0=0, 1=1, 2=2, 3=3, 4=4, 5=5, 6=6, 7=7, 8=8, 9=9}
6
访问key为6的数据后：{0=0, 1=1, 2=2, 3=3, 4=4, 5=5, 7=7, 8=8, 9=9, 6=6}
插入第11个数据后：{1=1, 2=2, 3=3, 4=4, 5=5, 7=7, 8=8, 9=9, 6=6, 11=11}
```

## 实现一个通用的LRU缓存算法。

上面的实现的LRU算法中默认的每个数据的大小是为1，如果是文件存储系统，每个文件大小是不一样的，所以一个通用的LRU缓存算法还有提供一个计算数据方法的大小，下面一个最小化的
通用LRU缓存算法实现，该算法参照了Android中LruCache类的实现。

```
public class LruCache<K,V> {
    private LinkedHashMap<K,V> map;
    //最大缓存容量
    private int maxSize;
    //当前容量
    private int size;


    public LruCache(int maxSize){
        this.maxSize = maxSize;
        map = new LinkedHashMap<>(0, 0.75f, true);
    }

    /**
     * 插入一条数据，更新当前容量大小，并检测是否已超出容量
     * @param key
     * @param value
     */
    public void put(K key, V value){
        size += sizeOf(key, value);
        map.put(key, value);
        trimSize(maxSize);
    }

    /**
     * 获取一条数据
     * @param key
     * @return
     */
    public V get(K key){
        V v;
        synchronized (this){
            v = map.get(key);
        }
        return v;
    }

    /**
     * 删除一条数据，并更新当前容量
     * @param key
     * @return
     */
    public V remote(K key){
        V v = map.remove(key);
        size -= sizeOf(key, v);
        return v;
    }

    /**
     * 检测当前容量是否已经超过最大容量，如果超过就开始清除数据，知道size小于maxSize为止。
     * @param maxSize
     */
    public void trimSize(int maxSize){
        while (true) {
            K key;
            V value;
            synchronized (this) {
                if (size < 0 || (map.isEmpty() && size != 0)) {
                    throw new IllegalStateException(getClass().getName()
                            + ".sizeOf() is reporting inconsistent results!");
                }

                if (size <= maxSize || map.isEmpty()) {
                    break;
                }

                Map.Entry<K, V> toEvict = map.entrySet().iterator().next();
                key = toEvict.getKey();
                value = toEvict.getValue();
                map.remove(key);
                size -= sizeOf(key, value);
            }
        }
    }

    /**
     * 重新调整缓存总大小
     * @param maxSize
     */
    public void resize(int maxSize){
        if (maxSize <= 0){
            throw new IllegalArgumentException("MaxSize 不能小于等于0！");
        }
        synchronized (this){
            this.maxSize = maxSize;
        }
        resize(maxSize);
    }

    /**
     * 数据大小.默认为1，想要修改数据大小，还需要子类实现
     * @param key
     * @param value
     * @return
     */
    protected int sizeOf(K key, V value) {
        return 1;
    }

    /**
     * 清除
     * @return
     */
    public void clear(){
        synchronized (this){
            map.clear();
        }
    }

    @Override
    public String toString() {
        return "size:"+size+";maxSize:"+maxSize+" "+map.toString();
    }
}

```

### 使用LruCache.

* 定义最大容量为10，每个数据的大小为1


```
 final int maxSize = 10;
LruCache<Integer,Integer> lruCache = new LruCache<Integer, Integer>(maxSize){
  @Override
  protected int sizeOf(Integer key, Integer value) {
      return 1;//数据大小为1
  }
};

for (int i = 0; i < maxSize; i ++){
  lruCache.put(i, i);
}
System.out.println("当前数据："+lruCache.toString());
System.out.println("key为6的数据"+lruCache.get(6));
System.out.println("访问key为6的数据后："+lruCache.toString());
lruCache.put(11,11);
System.out.println("插入value为11的数据后："+lruCache.toString());
```

输出结果为：

```
当前数据：size:10;maxSize:10 {0=0, 1=1, 2=2, 3=3, 4=4, 5=5, 6=6, 7=7, 8=8, 9=9}
key为6的数据6
访问key为6的数据后：size:10;maxSize:10 {0=0, 1=1, 2=2, 3=3, 4=4, 5=5, 7=7, 8=8, 9=9, 6=6}
插入value为11的数据后：size:10;maxSize:10 {1=1, 2=2, 3=3, 4=4, 5=5, 7=7, 8=8, 9=9, 6=6, 11=11}
```

* 定义最大容量为10，每个数据的大小为2


```
 final int maxSize = 10;
LruCache<Integer,Integer> lruCache = new LruCache<Integer, Integer>(maxSize){
  @Override
  protected int sizeOf(Integer key, Integer value) {
      return 1;//数据大小为2
  }
};

for (int i = 0; i < maxSize; i ++){
  lruCache.put(i, i);
}
System.out.println("当前数据："+lruCache.toString());
System.out.println("key为6的数据"+lruCache.get(6));
System.out.println("访问key为6的数据后："+lruCache.toString());
lruCache.put(11,11);
System.out.println("插入value为11的数据后："+lruCache.toString());
```

输出结果为：

```
当前数据：size:10;maxSize:10 {5=5, 6=6, 7=7, 8=8, 9=9}
key为6的数据6
访问key为6的数据后：size:10;maxSize:10 {5=5, 7=7, 8=8, 9=9, 6=6}
插入value为11的数据后：size:10;maxSize:10 {7=7, 8=8, 9=9, 6=6, 11=11}
```


* 定义最大容量为10，每个数据的大小为value


```
final int maxSize = 10;
LruCache<Integer,Integer> lruCache = new LruCache<Integer, Integer>(maxSize){
  @Override
  protected int sizeOf(Integer key, Integer value) {
      return value;//数据大小为value
  }
};

for (int i = 0; i < maxSize; i ++){
  lruCache.put(i, i);
}
System.out.println("当前数据："+lruCache.toString());
System.out.println("key为6的数据"+lruCache.get(6));
System.out.println("访问key为6的数据后："+lruCache.toString());
lruCache.put(11,11);
System.out.println("插入value为11的数据后："+lruCache.toString());
```

输出结果为：

```
当前数据：size:9;maxSize:10 {9=9}
key为6的数据null
访问key为6的数据后：size:9;maxSize:10 {9=9}
插入value为11的数据后：size:0;maxSize:10 {}
```




