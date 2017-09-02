### Contentprovider是什么

维护特定的应用数据，可以让其它的应用轻松的访问。它提供统一的接口以便对数据进行操作。提供对外部应用共享本地数据的一种机制。它屏蔽的了内部数据的存储细节，向外提供统一的借口模型，大大简化上层应用
每个Contentprovider都有一个为知的Uri来标示。content://authority/path#value

### ContentProvider实现监听 内容观察者

注册一个ContentObderver实现监听。接受onChange方法。
     
### 为什么要使用contentProvider 和sqlite的区别是什么呢

### ContentProvider是如何实现共享的。 
   一个程序可以通过实现一个ContentProvider的抽象接口将自己的数据完全暴露出去，而且ContentProviders是以类似数据库表的方式将数据暴露。Contentproviders存储和检索数据，通过它可以让所有的应用程序访问到，这是应用程序之间为宜共享数据库的犯法。

