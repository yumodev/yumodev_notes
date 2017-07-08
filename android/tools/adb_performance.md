### CPU

### adb shell dumpsys cpuinfo

adb shell dumpsys cpuinfo
adb shell dumpsys cpuinfo | grep packagename

### top

adb shell top-m 10 -s cpu
adb shell top -n 1 | grep packgename


-t 显示进程名称，-s 按指定行排序，-n 在退出前刷新几次，-d 刷新间隔，-m 显示最大数量

adb shell top -t -s cpu -d 2 -m 5

#### 选项解释

-m num : 最多显示多少个进程
-n num : 刷新次数
-d num : 刷新的间隔时间，单位是秒 默认是5秒
-s col : 按照那一列排序，比如 -s cpu 标识按照CPU排序。
-t :显示线程信息，而不是进程。
-h :显示帮助文档。
#### 使用方法

* 打印到文件

   adb shell top | findstr tmall > cpu.txt

* 只显示特定的包名的cpu信息

  adb shell top | grep 'com.UCMobile'


#### top 命令参数解释

* mac 下面的输出


  User 4%, System 4%, IOW 0%, IRQ 0%
  User 85 + Nice 1 + Sys 88 + Idle 1667 + IOW 0 + IRQ 3 + SIRQ 10 = 1854

    PID USER     PR  NI CPU% S  #THR     VSS     RSS PCY Name
    409 system   -2   0   1% S    14 168236K   8236K unk /system/bin/surfaceflinger
    888 system   18  -2   1% S   100 2464200K 232988K  fg system_server
  22267 u0_a96   10 -10   1% S   154 2543740K 328216K  ta com.UCMobile
   3119 shell    20   0   0% S     6  23820K   1124K  fg /sbin/adbd
  12765 shell    20   0   0% R     1   9116K   2128K  fg top

##### 第一组数据的含义

* USER 处于用户态的运行时间，不包含优先值未负的进程。
* Nice 优先值为负的进程所占用的CPU时间
* Sys 处于核心态的运行时间
* IOW :IO等待时间
* IRQ : 硬中断时间
* SIRQ : 软中断的含义


##### 第二组的含义

* PID ： 进程ID
* USER(UID) ：进程所有者的ID
* PR :进程优先级
* NI,niced : 进程的nice值，越小的值意味着越高的优先级。
* CPU% : CPU占用率。
* S : 进程状态 D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程
* #THR : 程序当前所用的线程数
* VSS : Virtual Set Size 虚拟好用内存
* RSS : Resident Set Size 虚拟使用的物理内存包含共享库的占用的内存。
* PCY : 进程所有者的优先级 fg： bg：
* Name : 进程名字
