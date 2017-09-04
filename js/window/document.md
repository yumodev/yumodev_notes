### document对象
使用Window.document属性返回一个document对象。代表当前Window内加装的文档。window可以忽略。


### 方法和属性

* 集合

	集合|描述
	------|------
	all[]|提供对文档中所有HTML元素的访问
	anchors[]|返回对文档所有的Anchor对象的引用
	applets|返回对文档中所有Applet对象的引用
	embeds|返回文档中所有嵌入内容的集合
	forms[]|返回对文档中所有Form对象的引用
	images[]|返回文档中所有Image对象的引用
	links[]|返回对文档中所有Area和Link对象的引用。
* 属性

	名称|描述
	------|------
	activeElement|返回文档中当前获得焦点的元素
	body|返回body
	baseURI|返回文档的绝对基础URI
	cookie|设置或返回当前文档相关的cookie，字符串形式。
	doctype|返回与文档相关的类型声明DTD
	documentElement|返回文档根节点
	documentMode|返回用于通过乱来请你渲染文档的模式
	documentUR|设置或返回文档的位置
	domain|返回当前文档的域名
	domconfig|返回normalizeDocument()被调用时所有的配置
	designModel|设置文档是否可以编辑。
	implementation|返回处理该文档的DOMImplementation帝乡
	inputEncoding|返回用于文档的编码方式
	lastModified|放回当前文档的最后修改日期和时间
	referrer|返回载入当前文档的文档的URL。
	readyState|返回文档的状态
	scripts|返回页面中所有脚本的集合
	strictErrorCheckling|设置或返回是否强制进行错误检查
	title|返回当前文档的title
	URL|返回当前文档的URL

* 方法

	名称|描述
	------|------
	addEventListener()|向文档添加句柄。
	close()|关闭用document.open()方法打开的输出流，并显示选定的数据
	createAttribute()|创建一个属性节点
	createComment()|创建注释节点
	createDocumentFragment()|创建空的DocumentFragment对象，并返回此对象
	createElement()|创建元素节点
	createTextNode()|创建文本节点
	hasFocus()| 查看元素是否获取焦点
	getElementsByClassName()|返回文档中所有制定类名的元素集合。
	getElementById()|返回对拥有制定的id的第一个对象的引用
	getElementsByName()|返回带有制定名称的对象的集合
	getElementsByTagName()|返回带有制定标签名的对象集合
	importNode()|把一个节点从另一个文档复制到该文档以便应用
	normalize()|删除空文本节点，并连接相邻节点。
	normalizeDocument()|删除空文本节点，并连接相邻节点。
	open()|打开一个流
	queryCommandEnabled(commandId)|获取命令是否已经激活
	queryCommmandIndeterm(commandId)|确定命令是否是不确定的
     queryCommandState(commandId)|获取命令的状态
     queryCommandSupported(commandId)|获取命令是否被支持。
     queryCommondValue(commandId)|返回命令的值
     querySelector()|返回文档中匹配制定的CSS选择器的第一元素
     querySelectorAll()|返回文档中匹配的CSS选择器的所有元素节点列表
     removeEventListener()|移除文档中的事件句柄
     renameName()|重命名元素或者属性节点
     write()|向文档写HTML表达式或者JavaScript代码
	writeln()|等同于Write()方法，不同的是在么个表达式之后写一个换行符。

###属性

* cookie

	返回字符串的形式

* readyState

   返回文档的状态，三种状态

	 值|说明
	  ------|------
	  loading|文档正在加载
	  interactive|文档已加载，链接资源未完全加载
	  complete|文档和链接资源完全加载

* designModel

  设置文档是否可以编辑；
  值on：html文档可以被编辑，等价于body的contenteditable属性设置为true，但是此时会忽略元素的contenteditable属性。
  值off:html文档不可以别编辑
