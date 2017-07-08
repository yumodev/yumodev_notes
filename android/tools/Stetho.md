### Stetho

    Stetho Facebook开发的一款Andorid开发调试工具，可以在Chrome Dev Tools下查看Android
    App布局，网络请求，Sqlite，preference，并不需要root设备。

### Stetho 使用

* 引入依赖包

  compile 'com.facebook.stetho:stetho:1.3.1'

* 初始化

  在Application的onCreate中进行初始化。
  Stetho.initializeWithDefaults(this);

* 在Chrome中输入 chrome://inspect/#devices 打开使用。

### Stetho 网络使用(基于OkHttp)

* 引入 依赖包
  compile 'com.facebook.stetho:stetho-okhttp3:1.3.1'

* 添加拦截器

OkHttpClient client = new OkHttpClient.Builder()
            .addNetworkInterceptor(new StethoInterceptor())
            .build();
