## Java配置哪些事儿- Mac版

### 查看当前的Java版本

   ` java -version`

### 查看当前所有的JavaHome

  `  /usr/libexec/java_home -V`

### 设置Java环境变量

export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home
export PATH=$JAVA_HOME/bin:$PATH
export CLASS_PATH=$JAVA_HOME/lib$PATH


