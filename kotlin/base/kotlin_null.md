# Kotlin中变量、常量以及空安全

## Kotlin的可空类型

在以往的Java开发中有一个著名的异常NullPointerException，俗称NPE，它由访问为空的属性引发，为此Kotlin中引入了可空类型，旨在有效消除NPE异常的发生。

在定义变量的的时候在其类型的后面加上一个问号表示该变量是可空类型，可以赋予其null值。比如


```java
//定义可空变量，并赋予null值
var a: String?
a = null
    
//定义一个非空变量，赋予null值的发生错误
var b: String
b = null // Null can not be a value of a non-null type String
```

## 安全调用可空类型

假定通过` var a: String? = "abc"`创建了一个可空的变量a，那么该如何调用变量。



### Kotlin控安全

## 参考


