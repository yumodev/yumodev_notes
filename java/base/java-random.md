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
生成[0,100)之间的整数：(int)(Math.random()*100)
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
public Random()  | 构造方法
public Random(long var1) | 构造方法，设置随机数数的种子
public synchronized void setSeed(long var1) | 设置随机数的种子
public void nextBytes(byte[] var1) | 产生随机的字节数，有参数var1接收。
public int nextInt() | 获取整数。
public int nextInt(int var1) | 获取0-var1范围呢的随机数
public long nextLong() | 获取Long型的随机数
public boolean nextBoolean() | 获取boolean类型的随机数 
public float nextFloat() | 返回float类型的随机数
public double nextDouble()|获取Double类型的随机数，[0,1)
public synchronized double nextGaussian() | 返回乘高斯正态分布的double值。


下面的例子是利用Random生成5个[0,100)内的随机数

```java
Random random = new Random();

//生成5个 0到100之间的数字。
System.out.println("生成5个 0到100之间的数字。 nextInt(100)");
for (int n = 0; n < 5; n++) {
    System.out.print(random.nextInt(100)+", ");
}
```


## Random的使用注意事项

Random产生随机数有两种方式，一种不带种子，一种是带种子的
有下面这样一段代码，定义两个随机数，设定相同的种子，返回100以内的随机整数，两个Random产生的随机数顺序是一样的。

```JAVA
Random random = new Random(100);
Random random1 = new Random(100);

//随机生成5个int数字  next()
System.out.println("随机生成5个int数字  next()");
for (int n = 0; n < 5; n++) {
    System.out.println(random.nextInt(100)+" "+random1.nextInt(100));
}

输出的结果如下：
15,15
50,50
74,74
88,88
91,91
```

Random的随机数其实伪随机数，只要种子确定，那么生成的随机数顺序是一致的，所以避免例子中两个Random实例产生了顺序一致的随机数。一般有两种方式

* 生成Random实例的时候不指定种子。
* 生成一个单例的Random类，然后生成随机数。


	

		




