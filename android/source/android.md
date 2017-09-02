### ubuntu 16.04

#### depot tools

     git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git


     ~/.bashrc

     export PATH=$PATH:~/android/depot_tools

#### 下载源码

    新建一个目录：chromium,进入到这个目录

    执行语句：fetch --nohooks --no-history android

    下面是输出的内容：
    Running: gclient root
    Running: gclient config --spec 'solutions = [
      {
        "url": "https://chromium.googlesource.com/chromium/src.git",
        "managed": False,
        "name": "src",
        "deps_file": ".DEPS.git",
        "custom_deps": {},
      },
    ]
    target_os = ["android"]
    '
    Running: gclient sync --nohooks --no-history
    Running: git submodule foreach 'git config -f $toplevel/.git/config submodule.$name.ignore all'
    Running: git config --add remote.origin.fetch '+refs/tags/*:refs/tags/*'
    Running: git config diff.ignoreSubmodules all


    如果在已存在的linux工程中，下载Android源码：直接添加

    $ echo "target_os = [ 'android' ]" >> ../.gclient

    下载源码
    $ gclient sync

    如果中断则重新下载

### 安装依赖

    $ src/build/install-build-deps-android.sh

    安装 Google PLay 服务

    $ src/third_party/android_tools/sdk/tools/android update sdk --no-ui --filter 57
1


### 安装OPEN JDK7

    sudo apt-get install openjdk-7-jdk
    确认配置为 OPEN JDK7

    $ sudo update-alternatives --config javac
    $ sudo update-alternatives --config java
    $ sudo update-alternatives --config javaws
    $ sudo update-alternatives --config javap
    $ sudo update-alternatives --config jar
    $ sudo update-alternatives --config jarsigner

    which java

### GN 编译

    cd  src
    gn args /out/Default

    在弹出的文件中填写如下

    target_os = "android"
    target_cpu = "arm"  # (default)
    is_debug = true  # (default)

    # Other args you may want to set:
    is_component_build = true
    is_clang = true
    symbol_level = 1  # Faster build with fewer symbols. -g1 rather than -g2
    enable_incremental_javac = true  # Much faster; experimental

### Chrome

    ninja -C out/Default chrome_public_apk
