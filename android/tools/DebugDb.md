# DebugDB -在浏览器中查看数据库

## 介绍

项目地址：[](https://github.com/amitshekhariitbhu/Android-Debug-Database)
## 引入依赖

 `debugCompile 'com.amitshekhar.android:debug-db:1.0.0'
` 
## 手机上设置端口


 
```
adb forward tcp:8080 tcp:8080
```

## 查看数据库


```
http://localhost:8080/#
```


