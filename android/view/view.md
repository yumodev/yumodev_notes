## 视图架构

* Activity
  
  Activity不负责视图的控制，只是控制生命周期和处理事件。真正控制视图的是Window。
  如果Activity没有Widnow就相当于Service了。
  
  * Window

    1、mDecor:DecorView的实例，表示Window内部的顶级视图。
    2、mContentParent：setContentView添加进来的。
    3、mContentRoot：DecorView的唯一子视图，包含mContentParnt,标题栏，状态栏。

  * DecorView 
  
    DecorView是FrameLayout的子类。可以认为是Android视图树的根节点。DecorView作为顶层View，一般情况下它内部包含一个垂直方向的LinearLayout。上面是标题栏，下面是内容栏。

### View的绘制过程
   测量、布局、绘制
   
   * 测量

     onMeasure
     
   * 布局

     onLayout。

   * 绘制

   onDraw。
   
### 三种测量模式、onMeasure获取长度。
MeasureSpec类。是这个32位的值。高2位是测量的模式。
低30为是测量的大小。

* EXACTLY

  精确值模式。具体数值或者制定为match_parent的时候。
  
* AT_MOST

  wrap_content的时候
  
* UNSPECIFIED

   视图自定义。
   
   
 View类的onMeasure只支持EXACTLY模式。
 如果需要View支持wrap_content就必须重写onMeasure()方法
## 坐标

View.getTop():view自身的顶边到其父布局顶边的距离。
View.getLeft():view自身的左边到父布局左边的距离
View.getBottom():view自身的底边到父布局底边的距离。
View.getRight():view自身的右边到父布局右边的距离。

MotionEvent.getX():获取点击事件相对控件左边的X轴的距离。点击事件距离左边的事件。
MotionEvent.getY():获取点击事件相对控件顶边的y轴的距离。点击事件距离空间顶边的距离。
MotionEvent.getRawX()：获取点击相对整个屏幕左边的x轴坐标。
MotionEvent.getRawY()：获取点击相对整个屏幕顶边的y轴坐标。

## 过度绘制

GPU过度绘制是找一个像素点上绘制多次。
View的绘制过程是：测量、布局、画图。三者的累积时间就是View的最终绘制事件。过多的层级、无用的父子节点 ，过于依赖系统系能计算位置的布局属性(RelativeLayout，weight)，都会影响性能。

### 过度绘制检测工具

GPU绘制：
无白色：绘制1次
蓝色：绘制2次
绿色：绘制3次
浅红：绘制4次
深红：绘制5次。

Hierarchy Viewer:
红色：该View所用时间超过大部分View很多。
红色：该View所用时间超过大部分View。
绿色：该View所用时间。

### 引起过度绘制的因素

* 层级过深
  
  LinearLayout == FrameLayout > RelativieLayout
  Weight
  
* 背景图片

### 优化措施

* theme中给Activity添加背景-WindowBackgroud属性。
* 减少设置背景。
* 避免嵌套过深。
* 减少使用weight。

## View ，suffaceView，GLSurfaceView有什么区别

   view是最基础的，必须在UI主线程内更新画面。速度较慢
   SurfaceView是View的子类，类似使用双缓冲机制，在新的线程中更新画面所以刷新节目速度比View快。
   GLSurfaceView是SurfaceView的子类，openGl专用

## view的刷新机制

   在需要刷新的地方调用Handle.sendmessage发送消息，然后在Handle的getMessage里面执行invaliate和 postinvaliate方法。
   UI线程中通过invalidate 
   非UI线程中通哟postInvalidate
   
## view 自定义和重用的问题。

自定义控件大概分为自绘控件，组合控件，以及继承控件

* 自绘控件：自己在OnDraw里面实现自己画出来。


