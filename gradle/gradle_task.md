# 常用Gradle的一些操作

## `Gradle` 基本操作

* 查看版本 

    `./gradlew -v 活着 ./gradlew --version`

* 查看帮助 

    
```
./gradlew -? 
./gradlew -h
./gradlew --help
```

* 编译

`./gradlew build`

* clean

`./gradlew clean`

## 任务

* 列出当前所有可被使用的task

`./gradlew tasks` 

* 列出当前所有可被使用的task, 并显示依赖关系

`./gradlew tasks` 

## 查看依赖


```
./gradlew  dependencies
./gradlew :app:dependencies
```

## 重新下载依赖库

`./gradlew assemble --refresh-dependencies`

