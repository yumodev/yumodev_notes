## PopupWindow的使用详解

### 主要公开方法

返回值 | 定义 | 含义
------| ------ | ------
 |PopupWindow(View contentView,<br> int width, int height) | 设置自定义视图和其显示的宽高
void | setContentView(View view) | 设置自定义视图
void | setFocusable(boolean focusable) | 设置是否可获取焦点

### 设置焦点

如果focusable为false，在一个Activity弹出一个PopupWindow，按返回键，由于PopupWindow没有焦点，会直接退出Activity。如果focusable为true，PopupWindow弹出后，所有的触屏和物理按键都有PopupWindows处理。

### 设置背景

### 设置显示位置

按照参照系分为两种:相对于某一个控件和相对于父控件

#### 相对于某一个控件

 showAsDropDown(View anchor)
 showAsDropDown(View anchor, int xoff, int yoff)

 默认显示某个控件的正下方，无偏移，但是可以设置偏移。

#### 相对于父控件

 showAtLocation(View parent, int gravity, int x, int y)

 设置相对于父控件的位置，有偏移。
 
### 参考

* [PopupWidnow的使用和分析](http://www.cnblogs.com/mengdd/p/3569127.html)




