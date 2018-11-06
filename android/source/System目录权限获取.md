## 参考

* [android-problem——remount of /system failed: Read-only file system](http://www.cnblogs.com/zl1991/p/6378033.html)
* [Android系统system用户权限和root权限的获取](https://blog.csdn.net/wf_fln/article/details/78687300)

## 查看是否具有system 目录是否具有权限


输入`mount | grep system`命令
在打印的信息中找到system相关的信息。

`/dev/block/sda6 /system ext4 rw,noatime,data=ordered 0 0`
如果是rw就具有写权限，如果是ro就只具备读权限

## adb 命令


```
adb root
adb adb disable-verity 
adb root
adb reboot
adb root
adb remount


adb root
adb disable-verity
adb reboot
adb remount
adb shell mount -o rw,remount /system
```
Genymotion可以获取目录权限

 
## remount

`mount -o rw,remount /system`
`mount -o rw,remount -t auto /system
`
## 指定分区


```
mount -o remount /dev/block/mtdblock0 /system
mount -o remount /dev/block/vda /system
mount -o rw,remount -t ext4 /dev/block/vda
```



## Android Studio模拟器获取System 目录权限



