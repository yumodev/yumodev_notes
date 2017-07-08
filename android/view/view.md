1、view的原理

2、view的消息分发机制

3、适配的问题

4、View ，suffaceView，GLSurfaceView有什么区别
   view是最基础的，必须在UI主线程内更新画面。速度较慢
   SurfaceView是View的子类，类似使用双缓冲机制，在新的线程中更新画面所以刷新节目速度比View快。
   GLSurfaceView是SurfaceView的子类，openGl专用

5、view的刷新机制
   在需要刷新的地方调用Handle.sendmessage发送消息，然后在Handle的getMessage里面执行invaliate和 postinvaliate方法。
   UI线程中通过invalidate 
   非UI线程中通哟postInvalidate
6、view 自定义和重用的问题。

。自定义控件
  大概分为自绘控件，组合控件，以及继承控件

  自绘控件：自己在OnDraw里面实现自己画出来。