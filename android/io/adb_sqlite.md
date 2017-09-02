### 安装sqlite3
1. 把sqlite3从模拟器中拷贝到mac上。

    adb pull /system/xbin/sqlite3 ~/android
    adb pull /system/lib/libncurses.so ~/android
    adb

2. 连接android手机。 设置/system文件夹读写权限
    mount -o rw,remount,rw /system
3. 将sqlite3 拷贝到Android手机中
    adb push ~/android/sqlite3 /system/xbin
    adb push ~/android/sqlite3 /sdcard/sqlite3
    cp /sdcard/sqlite3 /system/xbin
chmod 4755 /system/xbin/sqlite3
### 通过adb命令查看Sqlite数据 

###打开数据库
通过"Sqlite3 user.db" 打开一个数据库

