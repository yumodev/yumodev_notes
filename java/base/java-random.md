<h1 align="center" >java 随机数</h1>

## Java中生成随机数的方式

很多时候我们需要一些随机数，尤其是做测试的时候。
在Java中生成随机数有几种方式

## Java.util.UUID

可以通过UUID类生成唯一的字符串，并且是全局唯一所有的电脑产生的字符串都是不同的。它的使用方式如下：


```java
String uuid  =  UUID.randomUUID().toString(); 
```

## Math.random()

通过Math.random()方法可以生成[0,1)区间中double类型的数字，可以为0，但是小于1。其他通常的使用方式如下

```java
生成double随机数：Math.random()
生成[0,100]之间的整数：(int)(Math.random()*100)
```

进入Math.random()的源码中它的具体使用是调用的Random的nextDouble()方法。


```java
public static double random() {
        return RandomNumberGeneratorHolder.randomNumberGenerator.nextDouble();
}
    
private static final class RandomNumberGeneratorHolder {
   static final Random randomNumberGenerator = new Random();
}
```

## Random类
      
Random类生成随机的数，它的主要方法如下

方法 | 含义
--- | ---
	

		

