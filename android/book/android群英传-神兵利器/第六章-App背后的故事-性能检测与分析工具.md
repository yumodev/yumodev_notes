### 6.3 UI性能分析


* 减少绘图的等待时间
* 使帧率更加平稳连贯

16ms的黄金准则

Android系统对UI的提升

* 软解时代-2.3
* 硬解时代-2.3以后，GPU
* 黄油时代-VSYNC垂直同步机制和多缓冲机制。
* 异步绘制时代-Render Thread

* RelativeLayout和Linearlayout

  RelativeLayout 使用时要进行多次测量，而LinearLayout除非有weight属性需要多次测量外，只需要一次测量。
  LinearLayout减少深度；RelativeLayout,控制复杂度。
  
  * 使用Include标签重用布局

  * HierarchyViewer

  * Merge与ViewStub

  * 过渡绘制
	
	使用Debug GPU Overdraw工具检测。
	
     1、改善布局方式，避免重叠
     2、控件和主题背景颜色相同，在绘制时，可移除背景
     3、使用ClipRect减少重绘区域。
     
### 6.4 TraceView

Android Device Monitor

Call+Recur Calls/Total
CPU time/Call 某方法占用的CPU时间与调用次数的百分比。


### 6.5 应用启动时间计算

adb shell am
使用此命令可以从cmd控制台启动 activity, services；发送 broadcast等等：
adb shell am start -W com.mx.browser.star/.account.view.AccountActivity

### 6.6 内存探究

堆：由GC控制。
栈：由虚拟机控制，生命周期结束后，由虚拟机使用该变量占用的内存，内存回收快。

VSS：Virtual Set Size 虚拟耗用内存 包含共享库占用的内存。
RSS： Resident Set Size 实际使用物理内存。
PSS：Proportional Set Size 实际用用的武力内存，比例分配共享库占用内存。
USS: Unique Set Size 进程肚子占用的物理内存，不包含共享库占用的内存。
VSS 》 RSS 》 PSS 》USS

#### dumpsys meminfo 
  
  adb shell dumpsys meminfo <packagename>
  
  PssTotal:占用的内存。
  
  Views,Activities, AppContexts 数据，看到底有没有释放。
  
  Private内存：每个应用自己的那份内存。
  Share内存：所有进程共享一部分内存。
  Dirty内存：分配在Ram中的内存。
  Clean内存：缓存文件的内存。 so，dex， apkttf，code， image，
  
####  GC

   2.3 以前GC为同步的，暂停应用，进行会后。
   2.3以后，GC为并发线程，同时每次GC不会遍历整个Heap，只会遍历部分。
   5.0 以后，GC会进行碎片整理，减少OOM，
   
   常用的GC对象有：
   
   1. class ,由System class loader
   2. JNI，jni相关的调用。
   3. Thread，活着的
   4. Stack：栈中的对象。
   5. 静态：方法区类的静态属性引用的对象。
   6. 常量：方法区中常量引用的对象，final类型。  

#### ActivityManager.MemoryInfo 
      
      1、TotalMem，可用总内存。
      2、availMem：当前剩余内存
      3、lowMemory：是否处于低内存状态
      4、threshold：内存阀值
      5、getMemoryClass:单个内存的状态。
      
#### Debug.memoryInfo
       
       1、dalvikPrivateDirty
       2、dalvikPss
       3、dalvikSharedDirty
       4、TotalPrivateDirty
       5、TotalPss
       6、TotalSharedDirty
       
#### Runtime

       totalMemory
       freeMemory
       maxMemory
       
#### 获取更多内存
      
 * 子进程
 
    WebView 放到子进程中。

 * 通过使用NativeHeap

 * 使用OpenGL

 * Largeheap

#### 6.7 系统内存警告

* onLowMemory
* onTrimMemory


#### 6.8 onLowMemory

#### 6.9 onTrimMemory

#### 6.11 Dump Heap

HeapSize :  总共大小
Allocated ：已分配
Free  ： 碎片
% Used: 使用率
Objectes: 对象数

详细的统计
free: 判断碎片化的一个指标
1-byte array : 图片的占用。判断是否过大。

#### 6.12 Allocation Tracker


####6.15 MemoryAnalysisTool - MAT 
      
     
    
      







