## 查看APK的签名

* 解压APK
* 找到META-INF 下的.RSA文件；
* 进入cmd环境，进入.RSA文件文件所在路径，命令：keytool -printcert -file XXX.RSA即可查看签名信息。

