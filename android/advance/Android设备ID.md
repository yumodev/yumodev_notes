# Android 设备ID

## 参考

* [Android 获取设备唯一ID](https://blog.csdn.net/bobbob32/article/details/73105076)
* [AndroidUniqueDeviceId](https://github.com/lany192/AndroidUniqueDeviceId/blob/f8bfd3567c94dd0d20815c20151121172df752de/library/src/main/java/com/lany/uniqueid/DeviceUtils.java)
## IMEI


## ANDROID

ndroid ID常被认为不可信，因为它有时为null。开发文档中说明了：这个ID会改变如果进行了出厂设置。并且，如果某个Andorid手机被Root过的话，这个ID也可以被任意改变。

`Settings.Secure.getString(getContentResolver(), Settings.Secure.ANDROID_ID)`

