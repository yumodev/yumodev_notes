# ApkTool

## ApkTool

ApkTool用于Andorid中解析Apk包，通过ApkTool解包后，可以看到Apk包里面的AndroidManifest.xml文件、布局文件、图片资源文件、smali文件、语言文件，可以将汉化、修改资源和代码，重新打包成新的Apk包。

## ApkTool的下载、安装、运行-mac

ApkTool的下载和安装可以参见官方的网站的说明
[https://ibotpeaches.github.io/Apktool/install/](https://ibotpeaches.github.io/Apktool/install/)

1. 下载ApkTool的jar包，笔者下载的为apktool_2.2.3.jar
2. 将apktool_2.2.3.jar复制到/usr/local/bin/，并将名称修改为 apktool.jar. 

	`cp apktool_2.3.0.jar /usr/local/bin/apktool.jar`
3. 打开[apktool脚本](https://raw.githubusercontent.com/iBotPeaches/Apktool/master/scripts/osx/apktool)连接，右键保存为apktool.txt文件，然后将其拷贝进/usr/local/bin/目录。

   `cp apktool.txt /usr/local/bin/apktool`
  
4. 通过chmod +x命令修改apktool脚本的权限。

  	`sudo chmod +x /usr/local/bin/apktool` 

5. 命令行控制台下输入 `apktool --verison`查看apktool的版本验证是否安装成功


	```
	$ apktool --version
	2.3.0
	```
	
	此外也可以通过`java -jar apktool_2.3.0.jar`的方式来运行apktool，比如
	
	```
	$ java -jar apktool_2.3.0.jar --version
	2.3.0
	```
	
## ApkTool的常用操作
	
### Apk解包

通过 `apktool d`命令将一个Apk文件解包，通常的命令格式为

	`apktool d *.apk -o out`

*.apk为被解包的Apk文件.
out为将Apk文件解包后存放的目录

### Apk重新打包

通过 `apktool b`命令将将解包后的Apk文件目录重新打包，通常的命令格式为

	`apktool b out  *.apk`

out为Apk文件解包后存放的目录
*.apk为被解包的Apk文件.


### AndroidDecompiler

官方地址：https://github.com/dirkvranckaert/AndroidDecompiler
使用方法：
项目clone后，把apk丢进去，直接键入：
decompileAPK.sh -p xxx.apk


## 参考

* [ApkTool](https://ibotpeaches.github.io/Apktool/)
* [Android 反编译初探 应用是如何被注入广告的](http://blog.csdn.net/lmj623565791/article/details/53370414)



