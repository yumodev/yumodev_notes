## 参考


* [google source](https://source.android.com/setup/)
* [清华大学镜像](https://mirror.tuna.tsinghua.edu.cn/help/AOSP/)
* [Android 6.0 源码编译 on Mac](https://www.jianshu.com/p/4b303c6325e3)
* [MacOS 10.13.3 Android源码编译发生的那些事儿](https://blog.csdn.net/qq_25333681/article/details/79838325)
* [Mac 10.12 编译 Android 源码](https://blog.csdn.net/chen930724/article/details/57086494)
* [Android源码编译和调试](https://blog.csdn.net/hwliu51/article/details/75949060)
* [macOS（Sierra 10.12）上Android源码（AOSP）的下载、编译与导入到Android Studio](http://blog.bihe0832.com/macOS-AOSP.html)
* [Xcode 更改默认版本 xcode-select](https://blog.csdn.net/HPYON/article/details/69569102)
* [Android 源码下载、编译、调试、烧写](https://fanzai.me/post/android-kernel/)

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
mkdir android6
cd android6
```

* 初始化


`repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-6.0.1_r46`

`repo init -u https://android.googlesource.com/platform/manifest -b android-6.0.1_r46`
[查看分支](https://source.android.com/source/build-numbers#source-code-tags-and-builds)

`repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-6.0.1_r63`

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
repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-6.0.1_r46
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

`make clobber`
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
  
  * java 版本的问题

  
```
============================================
Checking build tools versions...
************************************************************
You are attempting to build with the incorrect version
of java.
 
Your version is: java version "1.6.0_45" Java(TM) SE Runtime Environment (build 1.6.0_45-b06) Java HotSpot(TM) 64-Bit Server VM (build 20.45-b01, mixed mode).
The required version is: "1.7.x"
 
Please follow the machine setup instructions at
    https://source.android.com/source/initializing.html
************************************************************
build/core/main.mk:171: *** stop。 停止。
```
### 启动

emulator

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
  


