# 使用Object定义静态方法、静态属性

## 定义静态方法和静态属性的方法完全一致。

### 类中所有的方法就是静态方法

使用`object`关键字替代`class`声明一个类，那么该类中的所有的方法都是静态方法，属性都是静态属性。


```java
object TestStatic {
   val staticVal = 0
   var staticVar = 0
    fun staticFun(){
        print("静态方法：staticFun")
    }
}
```

### 使用class关键字声明的类，创建静态方法和属性

如果类中只有一部分方法为静态方法或者静态属性，那么使用`companion object`关键字将静态方法包括起来就可以了。


```java
class TestStatic1 {
    companion object{
        val staticVal = 0
        var staticVar = 0

        fun staticFun(){
            print("静态方法：staticFun")
        }
    }
}
```

