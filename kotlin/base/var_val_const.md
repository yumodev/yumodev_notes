# Kotlin中的变量、常量、const



## 变量和常量

在Kotlin中使用`val`和`var`关键字来声明变量，其语法如下：

`val|var 变量名[: 变量类型][= 值]`

`val`关键字声明一个常量，赋值以后不再修改。
`var`关键字声明一个变量，可以重复赋值。

下面声明一个字符串变量`a`和一个字符串常量`b`，并设置初始值

```java
var a : String = "a";//声明一个变量a，并设置初始值。
val b : String = "b";//声明一个常量b，并设置初始值。
```

在Kotlin中变量和常量的类型是可以省略的，编译期对自动推断出来变量和常量类型，在上面的列子中，可以声明如下：

```java
var a = "a";
val b = "b";
```

在Kotlin中定义了变量的类型后，就可以省略变量的初始化赋值。


```java
val a : int
val b //This variable must either hava a type annotation or be initialized
```

## `val`定义常量的坑

在Kotlin中通过`val`定义的变量，只有get方法，没有set方法，所以只能读不能写。
但是其get方法可以复写，从而造成val定义的变量，有可能会发生值变化，情况下面的例子：


```java
 val A : Int 
    get()  {
        val rand = Random(System.currentTimeMillis())
        return rand.nextInt()
    }
```

`val`定义的常量`A`的`get()`方法被复写，每次调用常量A都会返回一个随机数，所以不能保证常量A的值不变。

## const关键字

在Kotlin中除了val关键字定义一个常量外，还提供了一个const关键字标识一个常量.
const修饰的val变量相当于java中 `static final`是真正意义上的java常量

const关键有以下特点：

* 只能修改val常量。
* const只能修饰顶级属性

下面是其使用的一个列子


```java
 # const 修饰的常量没有get方法 
# Const 'val' should not hava a getter
const val A : Int 
    get()  {
        val rand = Random(System.currentTimeMillis())
        return rand.nextInt()
    }
    
# 不能修饰var变量 Modifier 'const' is note applicable to 'vars'
const var B : Int = 0
    
# 定义一个常量 C = 0
const val C : Int = 0
```


