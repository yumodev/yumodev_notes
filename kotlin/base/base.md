# Kotlin基础部分

## HelloWorld


```

fun main(args:Array<String>){
    println("hello world!")
}
```

## 函数

### 一个完整函数实现

```Kotlin
/**
 * 返回a，b中的最大值
 */
fun max(a : Int, b : Int) : Int{
    return if(a > b) a else b
}
```

* `fun` 定义函数。
* `fun` 后面是函数的名字
* 之后是参数列表，参数名写在参数类型前面，中间用冒号分割。
* 最后是方法的放回类型，中间有冒号风格。返回类型可以省略。
* 函数的语句和表达式后面的分号，可以省略。

### 表达式体函数


```Kotlin
fun max(a : Int, b : Int) : Int = if( a > b) a else b
```

省略返回类型的表达式体

```Kotlin
fun max2(a : Int, b : Int) = if( a > b) a else b
```

### 语句和表达式

语句和表达式的区别是语句没有值，表达式有值。
在Kotlin中除了循环(for,do,do/while)外的大多数控制结构都是表达式。
在Java中所有的控制结构都是语句。

## Kotlin数据类型

类型 | 位长度
------|------
Long | 64
Double | 64
Float | 32
Int | 32
Short | 16
Byte | 8

Kotlin中字符不是数字。

## 变量

## if

## for

## while


