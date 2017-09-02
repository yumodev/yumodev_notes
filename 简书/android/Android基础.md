###一、请简单的描述一下android的系统架构

一个完整的android系统架构有底层向上依次分为：操作系统层（LinuxKernel），各种底层库和android运行环境， 应用程序的设计框架，应用层。

* 操作系统层 LinuxKernel

	    该层是Linux系统的内核。android是在linux2.6内核的基础上创建的。

* 中间层 各种底层库，android运行时。

		SufaceManager:提供2D和3D图像合成，提供管理窗口的子系统的功能。
		
		Media Framework:多媒体底层库，提供音频和视频的支持。
		
		SQLite:一种小型的开源关系型数据库，为android系统提供数据库的支持。
		
		OpenGL|ES：提供3D绘图的支持
		
		FreeType: 提供位图和矢量字体的功能。
		
		WebKit:为系统提供浏览器的功能。
		
		SGL:2D图像引擎。
		
		SSL:为通信的安全性提供支持
		
		Libc: c语言的核心库
		
		DalvikVM ：android的java虚拟机，是基于寄存器的虚拟机，执行以.dex格式的文件，在执行的过程中，每一个应用程序就是一个进程。

* 应用程序框架 为开发android应用App提供基础，提高代码组件的复用。

		ActivityManager: 活动管理框架。
		
		WindowManager:窗口管理框架
		
		ContentProvider:
		
		ViewSystem:视图系统
		
		NotificationManager:系统通知管理框架
		
		PackageManager:应用程序管理框架
		
		TelephonyManager：电话管理框架
		
		ResourceManager：资源管理器
		
		LocationManager:提供位置的服务
		
		XMPPService:XMPP通信

* 应用程序层：主要是应用程序，直接提供给普通用户使用的APP

###二、请描述一下JVM虚拟机和Dalvik虚拟机之间的区别。

* JVM虚拟机是基于栈的，而Dalvik虚拟机是基于寄存器的，所以它需要更少的指令，又更快的运行速度。
* JVM执行的是.class文件，而Dalvik虚拟机执行的是.dex格式的文件，.dex格式的文件更小，访问速度更快。
* Dalvik的常量池已被修改为使用32位的索引，简化了解释器。
* Dalvik虚拟机很小，使用的空间也小
* Dalvik经过优化允许在有限的内存中，运行多个虚拟机实例，并且每一个Dalvik应用当作一个独立的linux进程。

###三、android系统的四大组件

    Activity Service，Broadcast receiver ， Content，Provider



