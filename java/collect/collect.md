### Collection 接口

Collection是最基本的Java集合框架接口之一，Set和List接口都继承于它。
Collection标识一组元素(Object对象)的集合，通过通过add()添加一个对象，也可以通过addAll()方法添加一组对象

####方法

返回值|定义|描述
------ | ------ | ------
boolean | add(E e) | 添加一个元素到集合中
boolean | addAll(Collection<? extends E> c)|
void | clear() | 清除所有的元素
boolean | contains(Object o) | 是否包含该对象
boolean | containsAll(Collection<?> c) | 是否包含和集合中的所有元素
boolean | equals(Object o)| 比较两个集合是否相等
int | hashCode() | 返回该元素的哈希值
boolean | isEmpty() | 判断该集合中是否有元素
Iterator<E> | Iterator() | 返回一个迭代器
boolean | remove(Object o) |删除一个元素
boolean | removeAll(Collection<?> c)| 删除传入集合的所有元素
boolean | retainAll(Collection<?> c) | 两个集合求交集
int | size() | 返回集合的数目，最大为Integer.MAX_VALUE
Object[] | toArray()| 返回一个数组，该集合包含的所有元素
<T> T[] | toArray(T[] a) |

#### 添加元素

Collection接口定义了add(E) 和addAll(Collection<? extends E> c)  两个方法来添加元素。它允许将一个元素降入到集合中，也允许将另外一个对象加入其中。

Collection接口是一个泛型接口，只能存放制定的类型的元素。

Collection不支持将内置的基本类型类型的元素加入其中。

#### 遍历元素

Collection 继承了 Iterator 接口， 每个继承自Collection接口的子类，都能够通过Iterator() 方法获取一个迭代器进行遍历元素。


### AbstractCollection

它是实现了Collection接口的部分方法的抽象类。









