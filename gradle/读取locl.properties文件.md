# 读取local.properties文件

## 什么是local.properties文件

通过Android Studio创建一个Android工程后，在工程的根目录中都会生成一个名称为:`local.properties `文件，该文件一般用来存放该Android工程私有的属性配置，比如Android的sdk路径等等。local.properties文件一般都是存储到本地，禁止提交到服务器上。

## 读取local.properties文件内容


```
def readLocalProperties(){
    File file = rootProject.file('local.properties')
    if(file.exists()){
        //加载资源
        InputStream inputStream = rootProject.file('local.properties').newDataInputStream();
        Properties properties = new Properties()
        properties.load(inputStream)

        if (properties.containsKey("sdk.dir")){
            println properties.getProperty("sdk.dir")
        }
    }
}
```

## local.properties的应用

利用local.properties文件存储到本地不提交的服务器的使用习惯，那么可以将一个项目的私有配置属性，存储到该文件中，比如：应用的包名、应用名称等

下面是一个在local.properties中配置项目的包名的demo

### 创建local.properties文件中添加包名属性


```
ndk.dir=/Users/trunx/android/android-sdk/ndk-bundle
sdk.dir=/Users/trunx/android/android-sdk
pakcagename=com.yumodev.angradle.test
```

### 在项目的build.gradle中解析local.properties

* 首先定义函数parseLocalProperties解析出来应用包名

```

def parseLocalProperties(){
    File file = rootProject.file('local.properties')
    if(file.exists()){

       
        InputStream inputStream = rootProject.file('local.properties').newDataInputStream();
        Properties properties = new Properties()
        properties.load(inputStream)

        if(properties.containsKey("packagename")){
            println 'packageName:'+properties.getProperty("packagename")
            ext.packagename = properties.getProperty("packagename")
            println 'packageName:'+project["packagename"]
        }
    }
}

```

* 执行parseLocalProperties函数


```

allprojects {
    repositories {
        jcenter()
    }
    parseLocalProperties()
}
```

### 在Module的build.gradle中修改应用的包名


```
android {
    compileSdkVersion 25
    buildToolsVersion "25.0.2"
    defaultConfig{
        minSdkVersion 14
        targetSdkVersion 25
        versionCode 1
        versionName "1.0"
        testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"

        if(project.hasProperty('packagename')){
            println project['packagename']
            applicationId project['packagename']
        }else{
            println "has not package_name"
            applicationId "com.yumodev.angradle"
        }
    }
 	...   
}
```


