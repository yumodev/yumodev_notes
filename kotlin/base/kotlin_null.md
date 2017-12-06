# Kotlin中变量、常量以及空安全

## Kotlin的可空类型

在以往的Java开发中有一个著名的异常NullPointerException，俗称NPE，它由访问为空的属性引发，为此Kotlin中引入了可空类型，旨在有效消除NPE异常的发生。

在定义变量的时候在其类型的后面加上一个`?`问号表示该变量是可空类型，可以赋予其null值。比如


```java
//定义可空变量，并赋予null值
var a: String?
a = null
    
//定义一个非空变量，赋予null值的发生错误
var b: String
b = null // Null can not be a value of a non-null type String
```

## 可空类型的特点

* 可空类型变量可以存放null值。
* 可空类型变量不能赋值为不可空变量，包括非空类型的形参
* 不能直接调用可空类型的方法和属性

## 安全调用可空类型: `?.`

创建一个`var a: String? = "abc"`创建了一个可空的字符串类型变量`a`
那么调用变量`a`的方法前需要判断其是否为null，比如使用if..else判断
`println(if(a != null) a.toUpperCase() else a)`

在Kotlin中提供了一个非常方便的语法糖，可以将上面的if..else语句，使用`?.`
比如`println(a?.toUpperCase())`

## Elvis运算符:`?:`

看一个Elvis运算符的例子：

```java
//定义一个字符串变量a，如果不为空输出其值，如果为空输出“aa”
var a: String? = "a"
val b = "aa"
println(a?:b) //输出为a
a = null
println(a?:b) //输出为aa
```

通过上面的例子可以看出Elvis运算符的函数就是当变量`a`不为空，表达式的值就是`a`,如果为空表达式的值就是`b`

##  非空断言：`!!` 操作符

可空类型的变量不能直接调用，一般要么进行if..else判断，要么使用`?.`操作符进行安全调用，除此以外
Kotlin中提供了`!!`操作符，将一切可空的值转换为不可空的值，从而直接调用变量的方法和属性，
如果可控变量的值为null，将发生NPE异常。

## 参考

*《Kotlin实战》
* [空安全-Kotlin语言中文站](http://www.kotlincn.net/docs/reference/null-safety.html)


