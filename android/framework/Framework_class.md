# Framework

* [Instrumentation.java](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/core/java/android/app/Instrumentation.java)

	是一个工具类，当它被启用时，系统先创建它，在通过它来创建其它组件，另外系统和组件之间的交互
	也将通过Instrumentation来传递，这样Instrumentation就能监测系统和这些组件的交互情况了。

* Application
 
  Application保存了全局的application状态，Application由AndroidManifest.xml中的<application>标签声明，在实际使用时需要定义Application的派生类。
  
* Context 是一个借口，获取和操作Application对应的资源、类，甚至包含于Applicaiton中的四大组件。Context是一个抽象类，AMS创建它的子类：ContextImpl.


* [IApplicationThread](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/core/java/android/app/IApplicationThread.java)

  AMS通过该接口管理进程。
  
* [ApplicationNative](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/core/java/android/app/ApplicationThreadNative.java)
  
  继承Binder，实现了IApplicationThread
  
* [ProcessRecord](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/services/java/com/android/server/am/ProcessRecord.java)

  描述一个进程
  
  
* [ActivityManagerService](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/services/java/com/android/server/am/ActivityManagerService.java)
   进程管理
   
* [PackageManagerService](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/services/java/com/android/server/PackageManagerService.java)

* [ActivityThread](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/core/java/android/app/ActivityThread.java)

* [Am.java](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/cmds/am/src/com/android/commands/am/Am.java)
* [ActivityStack](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/services/java/com/android/server/am/ActivityStack.java)

 

