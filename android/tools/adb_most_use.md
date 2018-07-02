<h1 align='center'>Adb 常用命令 </h1>


## 基础命令

* 查看版本信息:`adb version`
* 查看帮助:` adb -h`
* 进入shell: `adb shell`
* 退出shell: `exit`
* 重启设备:`adb reboot`
* 终止adb服务:`adb kill-server`
* 启动adb服务:`adb start-server`


## 文件操作

* adb pull <remote> <local> 命令从真机拷贝文件到pc上。
* adb push <local> <remote> 命令从pc上复制一份文件到真机上。


## 截屏

	```
	adb shell screencap -p /sdcard/screen.png
	adb pull /sdcard/screen.png
	adb shell rm /sdcard/screen.png
	```

## 设备相关

* 查看设备序列号

  `adb get-serialno`
  
* 获取CPU信息

	`adb shell cat /proc/cpuinfo`

	
* 查看设备的内存信息

	`adb shell cat /proc/meminfo`
	
* 获取设备名称

	`adb shell cat /system/build.prop`
	
* 获取设备分辨率

	`adb shell wm size`


## 安装APK

### 安装APK

* 安装apk:`adb install <apkfile>`
* 强制安装apk: `adb install -r <apkfile>`
* 安装apk到Sd卡: `adb install -s <apkfile>`

### 卸载APP

* 卸载APP:`adb uninstall <packagename>`
* 卸载APP但是保留文件: `adb uninstall -k <package>`

## pm 命令

* 查看pm命令帮助： `adb shell cmd package`

### 打印应用列表

查看应用列表的格式：
`list packages [-f] [-d] [-e] [-s] [-3] [-i] [-u] [--user USER_ID] [FILTER]`  
  
参数 |  含义
--- | ---
 -f  | see their associated file<br>显示关联的APK文件
 -d | filter to only show disabled packages<br>只显示禁用的包
 -e | filter to only show enabled packages<br>只显示启用的包
 -s | filter to only show system packages<br>只显示系统的包名
 -3 | filter to only show third party packages<br>只显示第三方安装的包
 -i | see the installer for the packages    
 -u | also include uninstalled packages<br>显示已卸载的应用

* 打印所有的包名:`adb shell pm list packages `
* 查看包名某一个字符串的包名

	```  
	adb shell pm list packages com.taobao.taobao
	adb shell pm list packages | grep com.taobao.taobao
	```

### 清除应用数据和缓存 
   
	``` 
	adb shell pm clear <包名>
	adb shell pm clear com.taobao.taobao
	```

## am 命令

* 启动Activity

	`adb shell am start -n <package_name>/.<activity_class_name>`

## dumpsys 打印服务信息

	`adb shell dumpsys <服务名>` 命令打印系统中的服务信息

### 查看服务列表

	`adb shell dumpsys -l` 输出系统正在运行的所有服务。

### activity相关的信息

* 查看前台Activty

	`adb shell dumpsys activity activities | grep mFocusedActivity`

* 查看顶部的Activity

	`adb shell dumpsys activity top`

* 查看所有正在运行的服务

	`adb shell dumpsys activity services `

* 通过字符串过滤正则运行的服务

	`adb shell dumpsys activity services  <过滤字符串>`

* 显示待处理的Intent 

	 `adb shell dumpsys activity intents`
 
* 查看provider信息

 
```
 adb shell dumpsys activity providers
 adb shell dumpsys activity prov
```

## BroadcastRecord-广播

`adb shell dumpsys |grep BroadcastRecord`

## 进程、CPU、内存信息

* cpu信息

	`adb shell dumpsys cupinfo`

* 打印内存信息

	`adb shell dumpsys meminfo`

### 打印进程、CPU、内存信息-top命令

-t 显示进程名称，-s 按指定行排序，-n 在退出前刷新几次，-d 刷新间隔，-m 显示最大数量

* 查看内存和cpu信息

	`adb shell top`

* 制定进程数量

	`adb shell top -m 1`
	
* 刷新进程数据次数
	
	`adb shell top -n 5`
	
### ps命令

* 查看进程状态

  `adb shell ps`
  
* 查看制定id进程信息

	`adb shell ps <pid>`
	
* 过滤进程信息，或者查看一个包下的进程。

	`adb shell ps | grep <字符串>`
	
* 杀死一个进程

	`adb shell kill <pid>`

## 其他

* 生成bug报告

	`adb bugreport`


## 参考

* [Android 调试桥](https://developer.android.com/studio/command-line/adb.html)
* [开发必备---你应该知道的一些 ADB 命令](http://www.jianshu.com/p/0693b841c83b)
* [android中dumpsys命令用法详解](http://blog.csdn.net/zhangjg_blog/article/details/39852309)
* [dumpsys命令用法](http://gityuan.com/2016/05/14/dumpsys-command/)
* [AMS之dumpsys篇](http://gityuan.com/2017/07/04/ams_dumpsys/)

