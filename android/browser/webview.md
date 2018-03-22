# WebView一些总结

## WebView 安全漏洞

### 漏洞的原因

在WebView中有一个著名的远程调用漏洞:通过
`WebView.addJavascriptInterface(object, "javaObject")`,
注册让JavaScript调用Java对象，那么在Js中可以通过Java
的反射机制调用Java的任何对象。

`javaObject.getClass().forName("com.xx.xx").getMethod("xx").invoke("xx")`

### 解决办法

在方法添加添加注解：`@JavascriptInterface`
只允许该注解的方法在Js中调用


## WebView内存泄露

* 主动调用destroy
* 创建WebView的时候传入Application而不是Activity
* 将WebView从父节点移除


