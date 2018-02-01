
## 视图架构

* Activity
  
  Activity并不不负责视图的绘制，只是控制生命周期和处理事件。真正控制视图的是Window。
  
  如果Activity没有Widnow就相当于Service了。
  
  * Window

    1、mDecor:DecorView的实例，表示Window内部的顶级视图。
    2、mContentParent：setContentView添加进来的。
    3、mContentRoot：DecorView的唯一子视图，包含mContentParnt,标题栏，状态栏。

  * DecorView 
  
    DecorView是FrameLayout的子类。可以认为是Android视图树的根节点。DecorView作为顶层View，一般情况下它内部包含一个垂直方向的LinearLayout。上面是标题栏，下面是内容栏。
    
    
    
    # 参考
    
    *《Android开发艺术探索》第四章View的工作原理



