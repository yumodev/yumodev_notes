# Kotlin中循环、判断、中断

## if

在Kotlin中条件判断和Java一样使用的`if..else`判断，其语法如下，但是和Java不同的是在kotlin中if是一个表达式它会返回一个值。

```
if (used by atomicExpression)
  : "if" "(" expression ")" controlStructureBody SEMI? ("else" controlStructureBody)?
  ;
```

比如比较获取两个整型变量的最大值，利用Java传统编程方式如下：

```java
val a = 1
val b = 2
var max : Int?

if (a > b){
   max = a
}else{
   max = b;
}
    
//通过?:三元运算符来判断
max = a > b ? a : b;
```

在Kotlin中我们可以使用下面方法实现


```java
 val a = 1
 val b = 2
 var max = if (a > b) a else b
```

在Kotlin中没有三元运算符(?:),普通的if..else既可以实现该功能，所以三元运算符就没有必要存在了。if和else的分支不仅是可以是一个表达式，也可以是一个代码块，if分支如果是代码块，那么if的分支的值就是其代码块中最后一个表达式的值，比如下面的一段代码输出的值为2.


```
val a = 1
val b = 2
var max = if (a > b){
   a + 1
   a
}else{
   b+1
   b
}
print(max);
```

## when

`when`表达式取代了java中的`switch`语句，提供了比switch语句更强大的功能。`when` 将它的参数和所有的分支条件顺序比较，直到某个分支满足条件。`when` 即可以当做一个表达式，也可以当做一个语句。如果当做表达式，其值就是就是符合条件分支的值。如果所有的分支都没有满足条件，就会跳到else分支里面，如果想要多个分支具有相同的处理，可以将多个分支放到一起，用逗号分隔。

```
val ch = 'a'
when(ch) {
   'a' -> println(ch.toUpperCase())
   'b' -> println(ch.toLowerCase())
   'c','d' -> println(ch.toUpperCase())
   else -> println(ch)
}
```

在Kotlin中分支条件可以是一个值，也可以是一个表达式。下面是一个列子

```
val gradle = 100
when(gradle) {
    100 -> println("满分")
    in 85..100 -> println("优秀") //使用in检测是否在一个区间中
    100-16 -> print("良好") // 分支条件可以是一个表达式
    in 60..75 -> println("及格")
    else -> {
        println("不及格")
    }
}
```

如果不给when提供参数，但是when就可以取代if..else进行条件判断


```
val gradle = 100
when {
   gradle == 100 -> println("满分")
   gradle in 85..100 -> println("优秀") //使用in检测是否在一个区间中
   gradle in 60..75 -> println("及格")
   else -> {
       println("不及格")
   }
}
```
## for

## while

## do while

