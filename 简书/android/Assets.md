<h1 align="center">Android Studio 创建assets目录</h1>

###assets目录

assets目录是APK中未被编译的目录，所以我们可以用它来存放一些随APP一起发布的文件。
在Android Studio 创建assets目录有两种情形：

* 直接通过Android Studio 创建的工程。
* 由Eclipse工程转换而来的Android Studio工程。

### 在由Android Studio 创建的工程中创建assets目录

通过 菜单`"File->New->Foler->AssetsFolder"`创建

### 在由Eclipse工程转换而来的Android Studio工程中创建Assets目录

1. 在工程根目录下创建名字为`assets`  文件夹。
2. 在build.gradle文件中添加

```
sourceSets {
        main {
                  assets.srcDirs = ['assets']
        }
  }
```
     





