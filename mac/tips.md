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

du -hs *



