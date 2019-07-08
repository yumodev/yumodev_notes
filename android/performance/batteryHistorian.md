### Battery Historian

    5.0 引用。
    图形化数据分析工具。
### 1.0
#### 初始化

adb shell dumpsys batterystats --enable full-wake-history
adb shell dumpsys batterystats --reset

#### 生成报告

adb shell dumpsys batterystats > bugreport.txt
adb bugreport > bugreport.txt
adb bugreport bugreport.zip

#### 转换为HTML

python historian.py -a bugreport.txt > battery.html

### 2.0

#### 安装

[https://github.com/google/battery-historian](https://github.com/google/battery-historian)
* 配置go和python开发环境
* go get -d -u github.com/google/battery-historian/...
* cd $GOPATH/src/github.com/google/battery-historian
* go run setup.go(必须在终端能够翻墙)

#### 启动Battery Historian

cd $GOPATH/src/github.com/google/battery-historian
go run cmd/battery-historian/battery-historian.go [--port <default:9999>]


### 分析

Device estimated power use : 电量使用


