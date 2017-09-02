### 混淆失败

`java.io.FileNotFoundException: .../app/build/intermediates/proguard-rules/release/aapt_rules.txt (No such file or directory)`

clean工程

### 打包时提示：Error:(65) Error: Expected resource of type id [ResourceType]

在该类名前添加即可解决：@SuppressWarnings("ResourceType")

### adb shell -> more than one device/emulator

1.获取模拟器和设备列表 `adb devices`
2. 指定的device 来执行adb shell  
    ` adb -s devicename shell `

