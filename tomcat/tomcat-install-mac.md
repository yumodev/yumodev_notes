<h1 align="center" >Mac下安装Tomcat</h1>
### 一、下载tomcat

打开 tomcat官网 [http://tomcat.apache.org/](http://tomcat.apache.org/) 选择合适的版本下载
mac下下载zip和targz都行.这次我选择的是最新的9.0版本下载的。

### 二、安装

1. 解压刚才下载的文件，命名为tomcat9
2. 将tomcat9 移动到 `/Library`目录下
     
     `sudo mv tomcat9 /Library`
3. 为tomcat9 添加权限 `sudo chmod 755 /Library/tomcat9/bin/*.sh`

### 三、开启和关闭tomcat 

1. 启动tomcat `/Library/tomcat9/bin/startup.sh`
2. [http://localhost:8080](http://localhost:8080/) 打开tomcat 首页
3. 关闭 tomcat `/Library/tomcat9/bin/shutdown.sh`

### 四、配置环境变量

1. 打开.bash_profile `vi ~/.bash_profile`
2. 输入路径：`export PATH=$PATH:/Library/tomcat9/bin`
3. 输入 $PATH 查看刚才输入的路径
4. 输入` source ~/.bash_profile` 使配置生效
5. 测试 配置是否成功
	在终端输入 `startup.sh` 和 `shutdown.sh` 是否启动和关闭tomcat服务器

