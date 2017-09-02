### 面试相关

#### SurfaceView和View的区别

SurfaceView是单独起一个单独线程进行重新绘制，而View是在UI线程中进行替换。

#### 如何实现双缓冲。

SurfaceView在更新视图时用到了两张Canvas,即FrontCanvas和BackCanvas，frontCanvas显示当前的视图，backcanvas保存上一次视图。当使用lockcanvas获取画布时，得到的实际上是backCanvas而不是正在显示的FrontCanvas，之后你在获取到的backCanvas上绘制新视图。在unlockCanvasAndPost此视图，那么上次的这个Canvas将替换frontCanvas。原来的frontCanvas就切换到后台作为backCanvas。







