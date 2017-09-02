### GreedDao 3.2

### Android Studio中使用。

#### 在Project的build.gradle中添加


```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.0'
    }
}
```

#### 在App的build.gradle

```
apply plugin: 'org.greenrobot.greendao'

dependencies {
    compile 'org.green robot:greendao:3.2.0'
}

greendao{
    schemaVersion 1
    daoPackage 'com.yumo.android.database.greendao'
    targetGenDir 'src'
}
```

### 定义实体

GreenDao 默认会为每个实体类
