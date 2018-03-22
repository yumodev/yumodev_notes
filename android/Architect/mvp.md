# MVP

## UML图
![](https://upload-images.jianshu.io/upload_images/1792359-43982175dfe01c0d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/537)

## 定义

View:定义View接口比如IView，显示View的状态
Modul:定义接口IModule，处于业务的逻辑
Presenter:意思的而是主持人，定义IPresenter,持有IView和IModule。 Module发生改变时，通过IPresenter通过View发生改变；初步View的点击时，通过IPresenter触发Module进行操作。

## 特点

### 优点

将各组件进行解耦，带来良好的扩展性，测试性和稳定性。
### 缺点

定义了大量的类，类的加载需要开销。

## MVP内存泄露。

加入Module进行长时间操作，而View要销毁或者关闭后，该如何避免内存泄露呢。


Presenter通过弱引用的方式持有View。

## Demo - 按钮的点击，获取数据然后显示的MVP手机.


## 参考

* [Android MVP 十分钟入门！](https://juejin.im/post/58870cc2128fe1006c46e39c)
* [MVP模式的经典封装](https://juejin.im/post/5a61559051882573351a5fb6)
* [基于Activity、Fragment的生命周期避免MVP模式内存泄露的问题](http://blog.csdn.net/s003603u/article/details/56670819)
* [App架构之旅](https://www.jianshu.com/p/ae831e01dc54)
* [MVP架构开发，一篇让你从看懂到会使用](https://mp.weixin.qq.com/s?__biz=MzIwMzYwMTk1NA==&mid=2247485298&idx=1&sn=2a224af0e09e9f4fa36089fcc258a5d8&chksm=96cda63fa1ba2f29df8e543f4e2851de6873c1124c0c69370ec97bd4bd4ecad92c653be264da#rd)

