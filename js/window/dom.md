# dom
Document Object Model DOM
Document 和 Element 是最重要的两个DOM类。
Node是代表一个文档节点

#获取文档元素。

获取文档元素的方式

	1. 用指定的id属性
	2. 用指定的name属性
	3. 用指定的标签名字。
	4. 用指定的CSS类
	5. 匹配指定的CSS选择器。

* 通过指定的id 获取元素

      var var1 = document.getElementById(id)
* 通过指定的name属性

     var nodeList = document.getElementByName(name)

