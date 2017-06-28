### 移动APK的位置

```
afterEvaluate {
    tasks.getByName("assembleDebug").doLast {
        println 'test-debug.apk 到 ymdev/src/main/assets/目录下, 便于测试'
        copy {
            from(buildDir.absolutePath + '/outputs/apk/') {
                include project.name + '-' + 'debug' + '.apk'
            }
            def intoPath = "./../ymdev/src/main/assets/apk"
            println(intoPath);
            into(intoPath)
        }
    }
}
```
