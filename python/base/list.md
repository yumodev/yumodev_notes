# Python入门-列表和元组

## 列表-list

在Python中list是一种数据类型，用方括号`[]`标识一个list，里面可以存放任意的元素。

它具有一下特点

* list是可变的序列，
* list可以存放任意的元素，元素类型不必相同。
* list可以为空。
* list可以为无限大，存放任意多个元素。

##列表的索引和切片

list是一种序列，序列的所有索引和切片等操作对于列表是适用。

### 索引

```python
listA = [] #定义空列表
listA = ['a', 'b', 'c'] # 定义列表含有3个元素 a,b,c
print listA # 输出为 ['a', 'b', 'c']
print listA[0] # 输出为 a
print listA[1] # 输出为 b
print listA[2] # 输出为 c
print listA[-1] # 输出为 c
print listA[-2] # 输出为 b
print listA[-3] # 输出为 a
```

### 切片


```python
listOne = range(10)
# 获取前两个元素
print listOne[0:2]
# 冒号前面的索引起始位置不写，默认从0开始索引。
print listOne[:2]  # [0, 1]
# 冒号后面的索引不写，默认一直到索引结束
print listOne[0:]  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
# 冒号前后的索引都省略就获得所有元素
print listOne[:]  # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
# 第二个冒号是步长，默认为1。
print listOne[::2]  # [0, 2, 4, 6, 8]
```

## list常用方法或者操作的示例

### list方法

方法名|含义
------|------
append(x)|将一个元素追加到列表的尾部
count(x) | 返回x在列表中出现的次数
extend(L) | 将一个给定列表中的所有元素都添加到另一个列表中。
index(x) | 返回列表中第一个值为x的元素的索引。
insert(i,x) | 将一个元素插入到列表的指定位置.第一个参数时索引。
pop([i])|删除制定索引位置的元素并返回该元素的值，<br>如果不指定索引位置，就删除最后一个元素
remove(x) | 删除链表中值为x的第一个元素。
reverse() | 反转列表中的元素，不生成新的列表
sort(cmp=None, key=None, reverse=False) | 排序列表中元素，可以自定义排序规则。


### 遍历列表

可以通过for循环

```python

listOne = ['a', 'b', 'c']
# 通过for循环遍历列表的元素
for x in listOne:
    print x
    
输出为:
a
b
c
```

通过内置函数enumerate可以同时访问列表的值和索引

```python

listOne = ['a', 'b', 'c']
# 通过enumerate循环遍历列表的元素
for index, value in enumerate(listOne):
    print index, value    
输出为:
0 a
1 b
2 c
```

可以通过迭代器进行遍历列表


```python
listOne = ['a', 'b', 'c']
list_iter = iter(listOne)
while True:
    print list_iter.next()

输出为:
a
b
c
```

### 获取列表长度

通过内置函数len()获取获取列表的长度


```python

listOne = [1, 2, 3]
print len(listOne)  # 3
```

### 追加元素 append(x)

通过list的append()方法可以在列表的尾部插入一个元素

```python
listOne = [1, 2, 3]
listOne.append(4)
print listOne  # [1, 2, 3, 4]
```

除了append()方法以后还有以下几种方法在尾部插入一个元素



## 元组-tuple

### tuple

tuple和list相似，但是tuple一旦创建就不在允许改变。通过`()`创建一个一个tuple。
`t = （1，2，3，4，5，6，7，8，9，10）`

### tuple 特点

### 创建单个元素的tuple

通过`t = (1)`创建只含有一个元素的tuple，但是最后`t`只是一个整形数据。因为（）既可以标识tuple，也可以标识运算的优先级，是含有歧义的，所以要在单个元素后面添加`,`来避免歧义。`t = (1,)`


## 参考

* [【循序渐进学Python】2. Python中的序列——列表和元组](http://www.cnblogs.com/IPrograming/p/Python_list_tuple.html)
* [Python之旅-列表](http://wiki.jikexueyuan.com/project/explore-python/Datatypes/list.html)
* [Python之旅-元组](http://wiki.jikexueyuan.com/project/explore-python/Datatypes/tuple.html)
* [从零开始学Python-列表(1)](http://wiki.jikexueyuan.com/project/start-learning-python/111.html)
* [从零开始学Python-列表(2)](http://wiki.jikexueyuan.com/project/start-learning-python/112.html)
* [从零开始学Python-列表(3)](http://wiki.jikexueyuan.com/project/start-learning-python/113.html)
* [从零开始学Python-元组](http://wiki.jikexueyuan.com/project/start-learning-python/115.html)


