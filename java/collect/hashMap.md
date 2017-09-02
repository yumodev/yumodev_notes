[TOC]

### HashMap

Node<K,V>实现Map.entry.是一个键值对。
Node[] table。哈希树桶组
int threshold: 所能容纳你的key-value的极限  length* loadFactor
final float loadFactor : 负载因子 0.75
int modCount ：记录HashMap内部结构发生变化的次数。
int size:

#### 负载因子 0.75、扩容机制、

如果哈希桶数组很大，则比较分散，如果哈希桶数组比较小，那么碰撞的次数就比较多，为了取得空间成本和时间成本的均衡，就有负载因子的的说法。负载因子是0.75.

#### 红黑树

当链表的长度大于8时，将转换为红黑树。

#### 哈希算法


```
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```

取Key的hashCode,高位运算，取模运算。

#### 扩容机制

重新计算容量。
### 面试题目

#### HashMap和HashTable的区别

HashMap:
1. 非线程安全的，如果使用线程安全，使用ConcurrentHashMap
2. 可以存放Null值，当做KEY
3. 初始16，每次扩充2n

HashTable
1. 线程安全的。 Collections.Synchronized方法。
2. 不能存放null值。
3. 初始11，每次扩充2n-1

####  hash值是怎么计算的。
#### 怎么解决冲突和负载因子的问题。
#### HashMap的扩容机制。
#### CocurrentHashMap的桶分割原理。
#### HashMap是否是有序的？是否有有序的Map。
#### TreeMap和LinkedHashMap如何保证顺序的。
#### HashMap 能否用null作为键值。

可以存放的。







