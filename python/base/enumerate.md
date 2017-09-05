#enumerate

## enumerate说明

* enumerate是Python的内置函数
* 对于一个可迭代的（iterable）/可遍历的对象（如列表、字符串），enumerate将其组成一个索引序列，利用它可以同时获得索引和值

## enumerate基本使用

主要用于for循环中。


```
list1 = ['a','b','c','d']
for index, item in enumerate(list1):
    print index, item
```

输出为：

```
0 a
1 b
2 c
3 d
```

## enumerate参数
enumerate还可以指定第二个参数，用于指定索引起始值，如：


```
list1 = ['a','b','c','d']
for index, item in enumerate(list1,2):
    print index, item
```

输出为：

```
2 a
3 b
4 c
5 d
```



