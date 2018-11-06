## 参考

* [Android安全开发之通用签名风险](https://segmentfault.com/a/1190000006205334)
* [原android源码预置apk](https://blog.csdn.net/mockingbirds/article/details/54671588)
* [App下载、静默安装并自启动](https://blog.csdn.net/zhimingshangyan/article/details/79770255)
* [Android OTA静默升级](https://blog.csdn.net/foolish0421/article/details/79074366)
* [在android系统源码中预置apk](https://www.cnblogs.com/seven-sky/p/6500659.html)
* [让Android Studio支持系统签名(证书)](https://www.jianshu.com/p/47265c8899b5)
* [编译调试Android系统原生App - 以Settings为例](https://www.jianshu.com/p/691b2ad46e62)
* [android system app 开发](https://www.jianshu.com/p/63e4f0eb7386)
## 系统签名

1.1.进入\build\target\product\security,找到【platform.pk8】和【platform.x509.pem】系统密钥。 
1.2.进入\build\tools\signapk找到SignApk.java，运行 javac编译成SignApk.class 
1.3.执行命令java com.android.signapk.SignApk platform.x509.pem platform.pk8 input.apk output.apk

## 将APK复制到system/app

## 启动APP


```
adb shell am force-stop your.package.name
```


