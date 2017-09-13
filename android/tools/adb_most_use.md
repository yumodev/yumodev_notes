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

## 安装APK

### 安装APK

* 安装apk：`adb install <apkfile>`
* 强制安装apk: `adb install -r <apkfile>`

### 卸载APP

* 卸载APP：`adb uninstall <packagename>`
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

* 打印所有的包名：`adb shell pm list packages `
* 查看包名某一个字符串的包名：可以使用`grep`命名，也可以在直接写在后面：`adb shell pm list packages` 

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

## dumpsys 打印服务信息

`adb shell dumpsys <服务名>` 命令打印系统中的服务信息

### 查看服务列表

`adb shell dumpsys -l` 输出系统正在运行的所有服务。下面是我手机上一些输出，服务非常多。

```
  BastetService
  BdatService
  Binder.Pged
  DockObserver
  HsmStat
  Hwmedia.monitor
  IAwareCMSService
  SurfaceFlinger
  accessibility
  account
  activity
  alarm
  android.hardware.fingerprint.IFingerprintDaemon
  android.security.keystore
  appops
  appwidget
  assetatlas
  attestation_service
  audio
  authentication_service
  backup
  battery
  batteryproperties
  batterystats
  bluetooth_manager
  carrier_config
  chr_service
  clipboard
  com.huawei.bd.BDService
  com.huawei.harassmentinterception.service.HarassmentInterceptionService
  com.huawei.netassistant.binder.notificationcallbackbinder
  com.huawei.netassistant.service.netassistantservice
  com.huawei.permissionmanager.service.holdservice
  com.huawei.systemmanager.netassistant.netnotify.policy.NatTrafficNotifyService
  com.huawei.systemmanager.netassistant.netpolicy.NatNetworkPolicyService
  com.huawei.systemmanager.preventmode.PreventModeService
  com.huawei.systemmanager.rainbow.service
  commontime_management
  connectivity
  connectivity_metrics_logger
  consumer_ir
  content
  contexthub_service
  country_detector
  cover
  cpuinfo
  dbinfo
  device_policy
  deviceidle
  devicestoragemonitor
  diskstats
  display
  dns_listener
  dreams
  drm.drmManager
  dropbox
  ethernet
  fido_authenticator
  fingerprint
  gfxinfo
  gpu
  graphicsstats
  hardware_properties
  huawei.audioalgoservice
  huawei.camera.cfgsvr
  hwAlarmService
  hwAntiTheftService
  hwConnectivityExService
  hwGeneralService
  hwPfwService
  hwUsbExService
  hwext_device_service
  hwfm_service
  hwsysresmanager
  ihwvsim
  imms
  ims
  ims_config
  ims_ut
  input
  input_method
  input_method_secure
  ioinfo
  iostatsservice
  iphonesubinfo
  isms
  isms_interception
  isub
  jank
  jankshield
  jobscheduler
  launcherapps
  location
  lock_settings
  media.audio_flinger
  media.audio_policy
  media.camera
  media.camera.proxy
  media.codec
  media.drm
  media.extractor
  media.log
  media.player
  media.radio
  media.resource_manager
  media.sound_trigger_hw
  media_projection
  media_router
  media_session
  meminfo
  midi
  mount
  multi_task
  netpolicy
  netstats
  network_management
  network_score
  network_time_update_service
  nonhardaccelpkgs
  notification
  otadexopt
  package
  perfhub
  permission
  pgservice
  phone
  phone_apdu
  phone_huawei
  pinner
  power
  powergenius
  powerprofile
  print
  processinfo
  procstats
  recovery
  restrictions
  rttmanager
  samplingprofiler
  scheduling_policy
  search
  securityserver
  sensorservice
  serial
  servicediscovery
  shortcut
  simphonebook
  smartcardservice
  soundtrigger
  statusbar
  system.hsmcore
  telecom
  telephony.registry
  textservices
  trust
  tui
  uimode
  updatelock
  usagestats
  usb
  user
  vibrator
  voiceinteraction
  vowifi.service
  vr_display
  vr_system
  vrmanager
  wallpaper
  webviewupdate
  wifi
  wifip2p
  wifiscanner
  window
```

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

## 进程、CPU、内存信息

* cpu信息

`adb shell dumpsys cupinfo`

* 打印内存信息

`adb shell dumpsys meminfo`

### top命令

* 查看内存和cpu信息：`adb shell top`

### ps命令

## 下载文件

## 参考

* [开发必备---你应该知道的一些 ADB 命令](http://www.jianshu.com/p/0693b841c83b)
* [android中dumpsys命令用法详解](http://blog.csdn.net/zhangjg_blog/article/details/39852309)
* [dumpsys命令用法](http://gityuan.com/2016/05/14/dumpsys-command/)
* [AMS之dumpsys篇](http://gityuan.com/2017/07/04/ams_dumpsys/)

