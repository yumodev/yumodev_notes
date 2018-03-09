# 建造者模式

## 定义

建造者模式又成为生成器模式，属于对象创建型模式，它意图将一个复杂的对象构建与它的表示相分离。

## 使用场景

当构造一个对象需要很多参数的时候，并且参数的个数或者类型不固定的时候。

## UML结构图

 ![dd](http://my.csdn.net/uploads/201204/04/1333532291_9501.gif)

## 角色

* Builder: 它为创建一个产品Product对象的各个部件指定抽象接口
* ConcreteBuilder: 实现Builder接口，实现各个部件的具体构造和装配方法
* Product:它是被构建的负责对象，包含多个组成组件
* Director:导演类，负责安排负责复杂对象的建造次序，指挥者与抽象建造者之间存在关联关系

## 优缺点

松散耦合：生成器模式可以同同一个构建算法构建出表现上完全不同的产品。
复用：算法和产品的分离。

产生多余的builder对象，消耗内存；对象的构建过程会暴露。

## Builder在Android的使用




### AlertDialog

```java

class Builder{
		public AlertDialog create(){
		}
}
```

### OkHttp


```java

 OkHttpClient client = getOkHttpClient();
Request request = new Request.Builder().url(url).build();

Response response = client.newCall(request).execute();
```



```java

public final class Request {
  final HttpUrl url;
  final String method;
  final Headers headers;
  final RequestBody body;
  final Object tag;

  private volatile CacheControl cacheControl; // Lazily initialized.

  Request(Builder builder) {
    this.url = builder.url;
    this.method = builder.method;
    this.headers = builder.headers.build();
    this.body = builder.body;
    this.tag = builder.tag != null ? builder.tag : this;
  }
  
  public Builder newBuilder() {
    return new Builder(this);
  }
  
  public Request build() {
    if (url == null) throw new IllegalStateException("url == null");
    return new Request(this);
  }
    
}
```

### Glide




