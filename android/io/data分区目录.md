#### 如何获取data分区的大小

* adb命令方式
   通过`adb shell df` 命令查看系统的分区的大小
   
   
```
Filesystem                  Size     Used     Free   Blksize
/dev                        1.3G   608.0K     1.3G      4.0K
/sys/fs/cgroup              1.3G    12.0K     1.3G      4.0K
/mnt/secure                 1.3G     0.0K     1.3G      4.0K
/mnt/asec                   1.3G     0.0K     1.3G      4.0K
/mnt/obb                    1.3G     0.0K     1.3G      4.0K
/firmware-modem            95.0M    57.6M    37.3M     16.0K
/firmware                  77.0M    18.7M    58.3M     16.0K
/system                     3.3G     2.8G   515.3M      4.0K
/data                      10.6G     9.4G     1.1G      4.0K
/cache                     20.6M     1.4M    19.2M      4.0K
/persist                    3.9M    12.0K     3.8M      4.0K
/efs                        9.8M   264.0K     9.5M      4.0K
/persdata/absolute          4.8M    76.0K     4.8M      4.0K
/mnt/shell/knox-emulated   10.6G     9.4G     1.1G      4.0K
/mnt/shell/privatemode     10.6G     9.4G     1.1G      4.0K
/mnt/shell/emulated        10.5G     9.4G     1.1G      4.0K
```

* 代码方式


```
/**
     * 获取手机内部空间大小
     * @return
     */
    public static long getTotalInternalStorgeSize() {
        File path = Environment.getDataDirectory();
        StatFs mStatFs = new StatFs(path.getPath());
        long blockSize = mStatFs.getBlockSize();
        long totalBlocks = mStatFs.getBlockCount();
        return totalBlocks * blockSize ;
    }
    
    /**
     * 获取手机内部可用空间大小
     * @return
     */
    public static long getAvailableInternalStorgeSize() {
        File path = Environment.getDataDirectory();
        StatFs mStatFs = new StatFs(path.getPath());
        long blockSize = mStatFs.getBlockSize();
        long availableBlocks = mStatFs.getAvailableBlocks();
        return availableBlocks * blockSize;
    }
```


#### 关于单个APP在Data目录下的存储的问题

通过在 `/data/data/APP包名/`目录里面不停的存放文件，可以一直讲Data分区的空间用完，
因此可以得出安卓系统未对单个APP在data目录下的存储加以限制。

#### 关于有外置存储卡的安卓手机

无论是否一款手机是否含有外置存储卡，都可以按照上述的方法获取Data分区的大小，以及
单个APP在Data分区下的存储未做显示。






