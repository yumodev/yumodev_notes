<h1 align="center" >Ubuntu 安装 Tomcat</h1>
### 安装JDK

* 从官网下载JDK，下载路径 http://download.oracle.com/otn-pub/java/jdk/8u101-b13/jdk-8u101-linux-x64.tar.gz

* 将 `jdk-8u101-linux-x64.tar.gz` 移动到 `/usr/lib` 中， 解压到 `/usr/lib/jvm `目录
  
```
mkdir jvm
sudo tar -zxvf jdk-8u101-linux-x64.tar.gz -C /usr/lib/jvm
```
     
     
### 配置JRE路径

* 打开环境变量文件 `sudo vi ~/.bashrc`
* 在文件的末尾添加下面内容
 
```
export JAVA_HOME=/usr/lib/jvm/jdk1.8.0_101
export JRE_HOME=${JAVA_HOME}/jre  
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  
export PATH=${JAVA_HOME}/bin:$PATH  
```

* `source ~/.bashrc`


### 测试JRE 

通过 `java -version`  查看当前的Java路径

```
java version "1.8.0_101"
Java(TM) SE Runtime Environment (build 1.8.0_101-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.101-b13, mixed mode)
```

### 安装tomcat

* 下载tomcat 下载地址：http://www-eu.apache.org/dist/tomcat/tomcat-9/v9.0.0.M9/bin/apache-tomcat-9.0.0.M9.zip

* 将文件移动到 /usr/local 目录下，解压命名为 tomcat9

```
  cp apache-tomcat-9.0.0.M9.zip /usr/local
  uzip apache-tomcat-9.0.0.M9.zip
  mv apache-tomcat-9.0.0.M9 tomcat9
```
  
### 启动tomcat

* 开启tomcat9 权限

```
sudo chomd 755 /usr/local/tomcat9/bin/*.sh
```
  
 * 启动tomcat
 
```
cd /usr/local/tomcat9/bin
./startup.sh

Using CATALINA_BASE:   /usr/local/tomcat9
Using CATALINA_HOME:   /usr/local/tomcat9
Using CATALINA_TMPDIR: /usr/local/tomcat9/temp
Using JRE_HOME:        /usr/lib/jvm/jdk1.8.0_101/jre
Using CLASSPATH:       /usr/local/tomcat9/bin/bootstrap.jar:/usr/local/tomcat9/bin/tomcat-juli.jar
Tomcat started.
```

### 测试访问

在浏览器中输入 `http://localhost:8080` 访问




