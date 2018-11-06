
## platform权限

* 设置userid: `android:sharedUserId="android.uid.system" `
* 设置系统签名: `java -jar signapk.rar platform.x509.pem platform.pk8 a.apk a_signed.apk
`

## bug

* java.lang.RuntimeException: Unable to start activity ComponentInfo{com.yumo.android/com.yumo.android.MainActivity}: java.lang.UnsupportedOperationException: For security reasons, WebView is not allowed in privileged processes

[Android WebView 运行在系统进程引发的异常](https://www.jianshu.com/p/e71761597697)



   

