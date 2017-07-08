# 
### 属性和方法

* 属性

属性|描述
------|------
closed|返回窗口是否已经关闭
defaultSatus|设置或返回窗口状态栏中的默认文本
document|对Document对象的只读引用
frames|返回窗口中所有命名的框架。
history|对History对象的只读引用
innerHeight|返回窗口的文档显示区的高度
innerWidth|返回窗口的文档显示区的宽带
length|设置或返回窗口中的框架数量
location|用于窗口或框架的Location对象。
name|设置或返回窗口的名称
navigator|对Navigator的对象的只读引用
opener|返回对床架此窗口的窗口的引用。
outerHeight|返回窗口的外部高度，保护的工具条和滚动条
outerWidth|返回窗口的外部宽度，保护的工具条与滚动条。
pageXOffset|设置或返回当前页面相对于窗口显示区左上角的X位置
pageYOffset|设置或返回当前页面相对于窗口显示区左上角的Y位置
parent|返回父窗口
screen|screen对象的只读引用。
screenLeft|返回相对于屏幕的X坐标
screenTop|返回相对于屏幕窗口的Y坐标
screenX|返回相对于屏幕窗口的X坐标
screenY|返回相对于屏幕窗口的Y坐标
self|返回对当前窗口的引用
status|设置窗口状态栏的文本
top|返回最顶层的父窗口




* 方法

方法|描述
------|------
alert()|显示警告框。
blur()|把键盘焦点从顶层窗口移开
clearInterval()|取消由setInterval()设置的timeout
clearTimeout()|取消有setTimeout设置的timeout
close()|关于浏览器窗口
confirm()|确认对话框
createPopup()|弹出Popup窗口
focus()|把键盘焦点给予一个窗口
moveBy()|可相对窗口的当前坐标把它移动到指定的像素
moveTo()|把窗口的左上角移动到一个指定的坐标
open()|打开一个新的浏览器窗口或查找一个已命名的窗口
print()|打印当前窗口的内容
prompt()|弹出可提示用户输入的对话框
resizeBy()|按照指定的像素调整窗口的大小
resizeTo()|把窗口的大小调整到指定的宽度和高度
scroll()|
scrollBy()|按照指定的像素值来滚动内容。
scrollTo()|把内容滚动到指定的坐标。
setInterval()|按照指定的周期来调用函数或计算表达式
setTimeout()|在指定的毫秒数后调用函数或计算表达式。


###window用法

* location
 	通过Window.location或者document.location 获取l
 	location对象.
 	
 	获取一个网址信息。
 	window.location.href
 
   跳转到新的一个网址。
   window.location = "http://www.baidu.com" 跳转到百度
   
* setTimeOut(f,time)
   window.setTimeOut() 注册在制定的时间之后单词调用的函数。
   
* setInterval(f,time), clearInterval(f)
   window.setInterval() 注册在制定时间间隔后重复调用的函数。
   window.clearInterval() 取消已经注册的setInterval()
   
* history
   history.length:历史记录的数量。
   history.back()：后退
   history.forward()：前进
   history.go(1)：向前或者前后调整任意个页面
   
* navigator
   
   获取浏览器厂商和版本信息的Navigator对象。
   appName: 浏览器名称
   appVersion:浏览器版本信息
   userAgent:和appVersion相信，当时更为通用。
   platform：操作系统。
   onLine:是否联网
   geolocation:地理 位置
   cookieEnable:可以永久保存Cookie。
   
* screen
   
   	screen.width,height :获取窗口的宽高
   	screen.availWidth,availHeight:获取实际可用的大小，除去桌面任务栏等特性。
   	
* alert

* prompt

* confirm

* showModelDialog

#### open
     
   语法：`window.open(URL,窗口名称，参数字符串)；`
   
   * 窗口名称：
   
	   1. 由字母和数字和下划线组成。
	   2. _top, _blank,_self具有特殊意义的名称。
	       _top:在框架网页中在上部窗口中显示目标网页
	       _blank:在新窗口中显示目标网页
	       _self:在当前窗口中显示网页
	   3. 相同name的窗口的只能有一个。
	   4. name不能包含空格  

* 参数字符串

   参数|值|说明
   ------|------|-----	  
   top|Number|窗口顶部离开屏幕顶部的像素值
   left|Number|窗口左端离开屏幕左端的像素数
   width|Number|窗口的宽度
   height|Number|窗口的高度
   menubar|yes,no|窗口没有菜单
   toolbar|yes，no|窗口没有工具条
   scrollbar|yes，no|窗口没有滚动条
   status|yes，no|窗口没有状态栏 
     

