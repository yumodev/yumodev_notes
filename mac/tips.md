### too open many files 
 
```
查看
 sysctl kern.maxfiles
 sysctl kern.maxfilesperproc
 修改
 sudo sysctl -w kern.maxfiles=20480
 sudo sysctl -w kern.maxfilesperproc=18000
```
 


参考：[Is there a fix for the “Too many open files in system” error on OS X 10.7.1?](http://superuser.com/questions/433746/is-there-a-fix-for-the-too-many-open-files-in-system-error-on-os-x-10-7-1)


### 查看目录大小

`du -hs *`

### 触摸板单机无效

`killall Dock`

### 触摸板失效

* [导致 MacBook 触摸板手势突然失效的原因有哪些？](https://www.zhihu.com/question/21868694)
* 重置NVRAM

同时按开机键+Option+Command+P+R 听到3-4声后，松手。

* 重置SMC

  关闭电脑，连接上电源同时按下：左侧Shift+Control+Option+开关机键，十秒后松开。




