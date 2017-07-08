### 安装openJDK7

   sudo add-apt-repository ppa:openjdk-r/ppa
   sudo apt-get update  
   sudo apt-get install openjdk-7-jdk
   sudo apt-get install openjdk-8-jdk

### 检测安装是否成功

   sudo apt-get install openjdk-7-jdk

### 检测安装情况

   sudo update-alternatives --display java
   sudo update-alternatives --display javac

### 删除符号链接

   sudo update-alternatives --remove java /opt/jdk1.6.0_43/bin/java
   sudo update-alternatives --remove javac /opt/jdk1.6.0_43/bin/javac

### 重新设置符号链接

  sudo update-alternatives --install /usr/bin/java java /opt/jdk1.6.0_43/bin/java 300
  sudo update-alternatives --install /usr/bin/java java /opt/jdk1.7.0_72/bin/java 400
  sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-7-openjdk-amd64/bin/java 500

  sudo update-alternatives --install /usr/bin/javac javac /opt/jdk1.6.0_43/bin/javac 300
  sudo update-alternatives --install /usr/bin/javac javac /opt/jdk1.7.0_72/bin/javac 400
  sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/java-7-openjdk-amd64/bin/javac 500

  ### 手动切换

  sudo update-alternatives --config java
  sudo update-alternatives --config javac
