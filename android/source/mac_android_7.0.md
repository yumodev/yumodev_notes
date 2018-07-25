## 参考


* [google source](https://source.android.com/setup/)
* [MacBook Pro android 7.0 编译环境搭建](https://blog.csdn.net/atmars_blue/article/details/55669670)
* [https://www.jianshu.com/p/0792aef20448](mac10.12 编译 android-7.0 源码)
* [macOS（Sierra 10.12）上Android源码（AOSP）的下载、编译与导入到Android Studio](http://blog.bihe0832.com/macOS-AOSP.html)

## 准备工作

由于mac系统文件系统是大小写不敏感的系统，所以需要在mac中创建一个支持大小写敏感的文件系统。

### 创建分区

* 创建分区

  ` sudo hdiutil create -type SPARSE -fs 'Case-sensitive Journaled HFS+' -size 100g ~/android.dmg`

* 挂载分区

 ` sudo hdiutil attach ~/android.dmg.sparseimage -mountpoint /Volumes/android`

* 卸载分区

 `hdiutil detach /Volumes/android`

* 进入分区

  ` cd /Volumes/android`
  
* 改变分区大小

  `hdiutil resize -size <new-size-you-want>g ~/android.dmg.sparseimage`

### 安装Repo工具

* 创建目录 ： `mkdir ～/bin`
* 写入到path中 ： `PATH=～/bin:$PATH`
* 下载Repo


```
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

* 将Repo中的下载地址改为清华大学的镜像源。 
  编辑`~/bin/repo` 文件，修改REPO_URL为的源地址
  
  `REPO_URL = 'https://gerrit-googlesource.lug.ustc.edu.cn/git-repo'`

### 下载

* 进入分区

  ` cd /Volumes/android `

* 创建目录

  
```
mkdir android8
cd android8
```

* 初始化


`repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-8.0.0_r13`

`repo init -u https://android.googlesource.com/platform/manifest -b android-8.0.0_r17`
[查看分支](https://source.android.com/source/build-numbers#source-code-tags-and-builds)

`repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-8.0.0_r13`

提示警告：


```
warning: gpg (GnuPG) is not available.
warning: Installing it is strongly encouraged.
```

* 下载

`repo sync`


* 创建下载中断后自动下载的脚步


```
#!/bin/bash
PATH=~/bin:$PATH
repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-7.0.0_r1
repo sync -j4
while [ $? = 1 ]; do
echo "================sync failed, re-sync again ====="
sleep 3
repo sync -j4
done
```


```
#!/bin/bash
PATH=~/bin:$PATH
repo init -u https://android.googlesource.com/platform/manifest -b android-8.0.0_r17
repo sync -j4
while [ $? = 1 ]; do
echo "================sync failed, re-sync again ====="
sleep 3
repo sync -j4
done
```

保存为 `sync.sh`
提升权限:`chmod a+x sync.sh`
执行脚本：`./sync.sh`

* 下载完成

可以删除.repo文件

## 编译

### 配置JDK
 
 * 查看Java的home位置：`/usr/libexec/java_home`
 * 在~/.bash_profile文件中配置JAVA_HOME
 
 
```
JAVA_HOME=`/usr/libexec/java_home`
export JAVA_HOME
```

* 使配置文件生效：`source ~/.bash_profile`
* 查看配置：`echo $JAVA_HOME`

###编译

* 设置环境变量
  在`/Volumes/android/android8` 执行：`source build/envsetup.sh`
  
* 选择设备

  输入`lunch`
  
  
```
yumodeMacBook-Pro:android8 yumo$ lunch

You're building on Darwin

Lunch menu... pick a combo:
     1. aosp_arm-eng
     2. aosp_arm64-eng
     3. aosp_mips-eng
     4. aosp_mips64-eng
     5. aosp_x86-eng
     6. aosp_x86_64-eng
     7. full_fugu-userdebug
     8. aosp_fugu-userdebug
     9. car_emu_arm64-userdebug
     10. car_emu_arm-userdebug
     11. car_emu_x86-userdebug
     12. car_emu_x86_64-userdebug
     13. mini_emulator_arm64-userdebug
     14. m_e_arm-userdebug
     15. m_e_mips-userdebug
     16. m_e_mips64-eng
     17. mini_emulator_x86-userdebug
     18. mini_emulator_x86_64-userdebug
     19. aosp_dragon-userdebug
     20. aosp_dragon-eng
     21. aosp_marlin-userdebug
     22. aosp_marlin_svelte-userdebug
     23. aosp_sailfish-userdebug
     24. aosp_angler-userdebug
     25. aosp_bullhead-userdebug
     26. aosp_bullhead_svelte-userdebug
     27. hikey-userdebug

Which would you like? [aosp_arm-eng]
```

这里选择1

* 查看CPU和线程数

`sysctl machdep.cpu`

```
......
machdep.cpu.core_count: 4
machdep.cpu.thread_count: 8
......
```

* 开始编译

`make -j16`


### 编译过程中错误

* mac 版本


```
FAILED: out/soong/build.ninja
out/soong/.bootstrap/bin/soong_build -t -b out/soong -d out/soong/build.ninja.d -o out/soong/build.ninja Android.bp
internal error: Could not find a supported mac sdk: ["10.10" "10.11" "10.12"]
ninja: build stopped: subcommand failed.
12:43:18 soong bootstrap failed with: exit status 1
make: *** [run_soong_ui] Error 1
```


```
Then changed build/soong/cc/config/x86_darwin_host.go to:

darwinSupportedSdkVersions = []string{
    "10.8",
    "10.9",
    "10.10",
    "10.11",
    "10.12",
    "10.13",
}

```

* 2


```
ninja: build stopped: subcommand failed.
13:08:41 ninja failed with: exit status 1
make: *** [run_soong_ui] Error 1
```


```
解决方案如下：

Patch [bison fix](https://android-review.googlesource.com/c/platform/external/bison/+/517740) for High Sierra and build bison:

    1. cd /Volumes/android8/external/bison
    2. git cherry-pick c0c852bd6fe462b148475476d9124fd740eba160
    3. mm

Replace prebuilt bison binary with patched binary

    1. cp /Volumes/android/android8/out/host/darwin-x86/bin/bison /Volumes/android/android8/prebuilts/misc/darwin-x86/bison/

Build

    1. make  -j4
如果mm命令无法执行，需要回去执行以下命令

1. source build/envsetup.sh
2. lunch 之后回到external/bison目录
3. mm

```

* 切换SDK

  下载[sdk11](https://github.com/phracker/MacOSX-SDKs/releases)
  $xz -d ***.tar.xz
  tar -xvf  ***.tar
  mkdir ~/ymlib
  sudo ln -s ~/ymlib/MacOSX10.11.sdk /Applications/XCode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk
### 启动

emulator

./out/host/darwin-x86/bin/emulator -partition-size 2048 -kernel ./prebuilts/qemu-kernel/arm/kernel-qemu-armv7

./prebuilts/android-emulator/darwin-x86_64/emulator -partition-size 2048 -kernel ./prebuilts/qemu-kernel/arm/kernel-qemu-armv7

./prebuilts/android-emulator/darwin-x86_64/emulator -partition-size 2560 -kernel ./prebuilts/qemu-kernel/arm64/3.18/kernel-qemu2

./prebuilts/android-emulator/darwin-x86_64/emulator -partition-size 2048 -kernel ./prebuilts/qemu-kernel/arm/3.18/kernel-qemu2

emulator -system system.img -data userdata.img -ramdisk ramdisk.img -partition-size 2048 -skin WVGA854

emulator64-arm -system system.img -data userdata.img -ramdisk ramdisk.img -partition-size 2560 -skin WVGA854

#### bug

* emulator: WARNING: system partition size adjusted to match image file (2048 MB > 200 MB)

 emulator -partition-size 2048

### 导入Android Studio

* 进入源码根目录执行

  
```
  source build/envsetup.sh
  mmm development/tools/idegen/
  out/host/linux-x86/frameworks/目录下生成了idegen.jar文件
  development/tools/idegen/idegen.sh
  在源码根目录下生成android.ipr和android.iml
```

* 倒入AndroidStudio
  
## bug
  
### Gnupg 过时


```
POSIXLY_CORRECT=1 sudo port uninstall gnupg1
POSIXLY_CORRECT=1 sudo port install gmake libsdl git gnupg2
```


### 切换版本的清理工作


1、在源码目录的根目录下，make clean;

2、进到源码的\linux\kernel\目录下，执行make mrproper；

3、再退回到根目录，执行./sourcebuild/envsetup.sh,lunch,make.
  


