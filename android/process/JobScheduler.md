# JobScheduler

## 什么是JobScheduler

JobScheduler是Android L新增的特性，用于把一些不是特别紧急的任务放到更合适的时机去批量处理，可以更加节省电量。

## 建立JobService

JobService是抽象类，需要实现JobService，实现onStartJob和onStopJob方法。

* onStartJob

   该方法返回boolean类型的值。返回false表示任务已经执行完毕，如果返回true，那么任务正在执行。
   当任务执行完毕时，要调用jobFinished来通知系统。
   
* onStopJob

  在job没有执行完，别cancel掉的时候会掉。
  
  如果返回false就直接忽略掉，如果返回true，那么表示会重新放到JobScheduler的reSheduler中。xs
  

## 创建JobInfo对象


* setMinimumLatency

   设置任务的延迟执行时间，单位毫秒。

* setOverrideDeading

   设置任务的最晚执行时间。

* setPersited

   告诉系统当设备重启之后是否还执行。

* setRequiredNetworkType

设置网络条件，只有满足制定的网络条件时，才会执行。

* setRequiresCharging

只有设备在充电的时候，才会调用。

* setRequiresDeviceIdle

只有当用户没有使用该设备且只有一段时间没有使用时才会启动该任务。

## 创建调度任务

