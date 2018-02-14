## 修改NPM的源


```
npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
```

## 提升`/usr/local`权限


```
sudo chown -R `whoami` /usr/local
```
## 安装ReactNative


```
npm install -g react-native-cli 
npm install -g yarn react-native-cli

```
## 创建demo工程并运行


```
react-native init demo1
cd demo1
react-native run-android
```
## 查看版本和帮助


```
react-native -h
react-native -v
```

## 安装Nuclide

在Atom中安装插件：Nuclide

## 安装React Developer Tools

在Chrome浏览器中安装调试工具。

## 安装Watchman


```
brew update
brew install watchman
```

## 查看运行日志


```
adb logcat *:S ReactNative:V ReactNativeJS:V
```

## 升级ReactNative

`react-native upgrade`

## 调试

当代码修改完成保存以后，ReactNative会检测到代码发生变化以后，会立即针对改变 进行编译。通过手机的menu键或者摇晃手机弹出调试菜单，选择'Relaod JavaScript'
## tips

### init失败

`npm WARN deprecated connect@2.30.2: connect 2.x series is deprecated
`

npm update connect@2.30.2
npm update -d

### init失败

删除yarn  `rm /usr/local/bin/yarn`

```
This will walk you through creating a new React Native project in /Users/trunx/git/rn/demo
Using yarn v1.0.0
Installing react-native...
yarn add v1.0.0
info No lockfile found.
[1/4] 🔍  Resolving packages...
error Received malformed response from registry for undefined. The registry may be down.
info Visit https://yarnpkg.com/en/docs/cli/add for documentation about this command.
{ Error: Command failed: yarn add react-native --exact
    at checkExecSyncError (child_process.js:591:13)
    at execSync (child_process.js:631:13)
    at run (/usr/local/lib/node_modules/react-native-cli/index.js:294:5)
    at createProject (/usr/local/lib/node_modules/react-native-cli/index.js:249:3)
    at init (/usr/local/lib/node_modules/react-native-cli/index.js:200:5)
    at Object.<anonymous> (/usr/local/lib/node_modules/react-native-cli/index.js:153:7)
    at Module._compile (module.js:573:30)
    at Object.Module._extensions..js (module.js:584:10)
    at Module.load (module.js:507:32)
    at tryModuleLoad (module.js:470:12)
  error: null,
  cmd: 'yarn add react-native --exact',
  file: '/bin/sh',
  args: [ '/bin/sh', '-c', 'yarn add react-native --exact' ],
  ....
  Command `yarn add react-native --exact` failed.
```

###


```
Installing react-native...
Consider installing yarn to make this faster: https://yarnpkg.com
npm WARN deprecated connect@2.30.2: connect 2.x series is deprecated
npm ERR! Unexpected end of input at 1:5370
npm ERR! ,"babel-helper-hoist-variables":"7.0.0-alpha.9"},"directories":{},"di
npm ERR!                                                                      ^

npm ERR! A complete log of this run can be found in:
npm ERR!     /Users/trunx/.npm/_logs/2017-09-10T05_20_15_338Z-debug.log
{ Error: Command failed: npm install --save --save-exact react-native
    at checkExecSyncError (child_process.js:591:13)
    at execSync (child_process.js:631:13)
    at run (/usr/local/lib/node_modules/react-native-cli/index.js:294:5)
    at createProject (/usr/local/lib/node_modules/react-native-cli/index.js:249:3)
    at init (/usr/local/lib/node_modules/react-native-cli/index.js:200:5)
    at Object.<anonymous> (/usr/local/lib/node_modules/react-native-cli/index.js:153:7)
    at Module._compile (module.js:573:30)
    at Object.Module._extensions..js (module.js:584:10)
    at Module.load (module.js:507:32)
    at tryModuleLoad (module.js:470:12)
  error: null,
  cmd: 'npm install --save --save-exact react-native',
  file: '/bin/sh',
  args:
   [ '/bin/sh',
     '-c',
     'npm install --save --save-exact react-native' ],
  options:
   { stdio: [ [Object], [Object], [Object] ],
     shell: true,
     file: '/bin/sh',
     args:
      [ '/bin/sh',
        '-c',
        'npm install --save --save-exact react-native' ],
     envPairs:
      [ 'TERM_PROGRAM=iTerm.app',
        'ANDROID_HOME=/Users/trunx/android/android-sdk',
        'TERM=xterm-256color',
        'SHELL=/bin/bash',
        'TMPDIR=/var/folders/b6/hp76wj2d7_l_wqc950blpnwh0000gq/T/',
        'GRADLE_HOME=/Users/trunx/.gradle/wrapper/dists/gradle-3.3-all/55gk2rcmfc6p2dg9u9ohc3hw9/gradle-3.3',
        'GOBIN=/Users/trunx/go/package/bin',
        'Apple_PubSub_Socket_Render=/private/tmp/com.apple.launchd.Hb729Z0B88/Render',
        'OLDPWD=/Users/trunx/git',
        'MAIL_USERNAME=disheng54@163.com',
        'http_proxy=http://127.0.0.1:8118',
        'USER=trunx',
        'CLASS_PATH=/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home/lib/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home/bin:/Users/trunx/depot_tools:/Users/trunx/android/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/go/bin:/Users/trunx/android/android-sdk/platform-tools:/Users/trunx/android/android-sdk/tools:/Users/trunx/go/go1.8/bin:/Users/trunx/mongodb/bin:/Users/trunx/android/android-sdk/platform-tools:/Users/trunx/android/android-sdk/tools:/Users/trunx/android/android-ndk-r10e:/Library/tomcat9/bin:/Users/trunx/git/startools',
        'COMMAND_MODE=unix2003',
        'ANDROID_NDK_ROOT=/Users/trunx/android/android-ndk-r10e',
        'SSH_AUTH_SOCK=/private/tmp/com.apple.launchd.s1fYpNNUR2/Listeners',
        '__CF_USER_TEXT_ENCODING=0x1F7:0x19:0x34',
        'XWALK_OS_ANDROID=1',
        'INTEL_MULTI_OS_ENGINE_HOME=/Applications/Intel/multi_os_engine',
        'MAIL_PASSWORD=duzitingyu',
        'PATH=/Users/trunx/.gradle/wrapper/dists/gradle-3.3-all/55gk2rcmfc6p2dg9u9ohc3hw9/gradle-3.3/bin:/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home/bin:/Users/trunx/depot_tools:/Users/trunx/android/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/go/bin:/Users/trunx/android/android-sdk/platform-tools:/Users/trunx/android/android-sdk/tools:/Users/trunx/go/go1.8/bin:/Users/trunx/mongodb/bin:/Users/trunx/android/android-sdk/platform-tools:/Users/trunx/android/android-sdk/tools:/Users/trunx/android/android-ndk-r10e:/Library/tomcat9/bin:/Users/trunx/git/startools',
        'PWD=/Users/trunx/git/rn',
        'JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home',
        'LANG=zh_CN.UTF-8',
        'ITERM_PROFILE=Default',
        'XPC_FLAGS=0x0',
        'ANDROID_HVPROTO=ddm',
        'https_proxy=http://127.0.0.1:8118',
        'MONGODBROOT=/Users/trunx/mongodb',
        'XPC_SERVICE_NAME=0',
        'SHLVL=1',
        'HOME=/Users/trunx',
        'COLORFGBG=7;0',
        'GOROOT=/Users/trunx/go/go1.8',
        'ITERM_SESSION_ID=w0t0p0',
        'LOGNAME=trunx',
        'GOPATH=/Users/trunx/go/package',
        'FLASKY_ADMIN=MAIL_USERNAME',
        'DISPLAY=/private/tmp/com.apple.launchd.Td9Mh1vkcE/org.macosforge.xquartz:0',
        'NDK_ROOT=/Users/trunx/android/android-ndk-r10e',
        'GYP_GENERATORS=ninja',
        'SECURITYSESSIONID=186a6',
        '_=/usr/local/bin/react-native' ],
     killSignal: undefined },
  envPairs:
   [ 'TERM_PROGRAM=iTerm.app',
     'ANDROID_HOME=/Users/trunx/android/android-sdk',
     'TERM=xterm-256color',
     'SHELL=/bin/bash',
     'TMPDIR=/var/folders/b6/hp76wj2d7_l_wqc950blpnwh0000gq/T/',
     'GRADLE_HOME=/Users/trunx/.gradle/wrapper/dists/gradle-3.3-all/55gk2rcmfc6p2dg9u9ohc3hw9/gradle-3.3',
     'GOBIN=/Users/trunx/go/package/bin',
     'Apple_PubSub_Socket_Render=/private/tmp/com.apple.launchd.Hb729Z0B88/Render',
     'OLDPWD=/Users/trunx/git',
     'MAIL_USERNAME=disheng54@163.com',
     'http_proxy=http://127.0.0.1:8118',
     'USER=trunx',
     'CLASS_PATH=/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home/lib/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home/bin:/Users/trunx/depot_tools:/Users/trunx/android/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/go/bin:/Users/trunx/android/android-sdk/platform-tools:/Users/trunx/android/android-sdk/tools:/Users/trunx/go/go1.8/bin:/Users/trunx/mongodb/bin:/Users/trunx/android/android-sdk/platform-tools:/Users/trunx/android/android-sdk/tools:/Users/trunx/android/android-ndk-r10e:/Library/tomcat9/bin:/Users/trunx/git/startools',
     'COMMAND_MODE=unix2003',
     'ANDROID_NDK_ROOT=/Users/trunx/android/android-ndk-r10e',
     'SSH_AUTH_SOCK=/private/tmp/com.apple.launchd.s1fYpNNUR2/Listeners',
     '__CF_USER_TEXT_ENCODING=0x1F7:0x19:0x34',
     'XWALK_OS_ANDROID=1',
     'INTEL_MULTI_OS_ENGINE_HOME=/Applications/Intel/multi_os_engine',
     'MAIL_PASSWORD=duzitingyu',
     'PATH=/Users/trunx/.gradle/wrapper/dists/gradle-3.3-all/55gk2rcmfc6p2dg9u9ohc3hw9/gradle-3.3/bin:/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home/bin:/Users/trunx/depot_tools:/Users/trunx/android/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/go/bin:/Users/trunx/android/android-sdk/platform-tools:/Users/trunx/android/android-sdk/tools:/Users/trunx/go/go1.8/bin:/Users/trunx/mongodb/bin:/Users/trunx/android/android-sdk/platform-tools:/Users/trunx/android/android-sdk/tools:/Users/trunx/android/android-ndk-r10e:/Library/tomcat9/bin:/Users/trunx/git/startools',
     'PWD=/Users/trunx/git/rn',
     'JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_11.jdk/Contents/Home',
     'LANG=zh_CN.UTF-8',
     'ITERM_PROFILE=Default',
     'XPC_FLAGS=0x0',
     'ANDROID_HVPROTO=ddm',
     'https_proxy=http://127.0.0.1:8118',
     'MONGODBROOT=/Users/trunx/mongodb',
     'XPC_SERVICE_NAME=0',
     'SHLVL=1',
     'HOME=/Users/trunx',
     'COLORFGBG=7;0',
     'GOROOT=/Users/trunx/go/go1.8',
     'ITERM_SESSION_ID=w0t0p0',
     'LOGNAME=trunx',
     'GOPATH=/Users/trunx/go/package',
     'FLASKY_ADMIN=MAIL_USERNAME',
     'DISPLAY=/private/tmp/com.apple.launchd.Td9Mh1vkcE/org.macosforge.xquartz:0',
     'NDK_ROOT=/Users/trunx/android/android-ndk-r10e',
     'GYP_GENERATORS=ninja',
     'SECURITYSESSIONID=186a6',
     '_=/usr/local/bin/react-native' ],
  stderr: null,
  stdout: null,
  pid: 93659,
  output: [ null, null, null ],
  signal: null,
  status: 1 }
Command `npm install --save --save-exact react-native` failed.
Test-Mac:rn trunx$
```

### 连接不上Genymotion


```
在电脑中查看该网址是否可以打开：https://localhost:8081/index.android.bundle?platform=android
```





