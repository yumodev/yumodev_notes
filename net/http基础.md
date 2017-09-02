[TOC]
### http 

#### http

默认的端口是80

主要特点：

* 支持客户/服务器模式。
* 简单快速：客户向服务器端请求服务时，只需传送请求方式和路径。
* 灵活：允许传输任意类型的数据对象。由Content-Type标记。
* 无连接：每次响应一个请求，响应完成后就断开连接
* 无状态：服务器不保存浏览器的任何信息，每次提交的请求之间没有关联。

#### 非持续性和持续性 Connection:

http1.0 默认是非持续性。HTTP1.1 默认持续性。

* 持续性：

浏览器和服务器建立TCP连接后，可以请求多个对象。
客户端在最后一个连接时，发送Connection:close ,明确要求服务器关闭TCP连接。
对于同一个域名，大多数浏览器允许同时建立6个持久连接。

* 非持续性

浏览器和服务器建立TCP连接后，只能请求一个对象。

Connecton:keep-alive

#### 管道机制

1.1引入了管道机制，在同一个TCP连接里面，客户端可以同时发送多个请求。
比如客户端需要请求两个资源，以前的是在同一个TCP连接里面，先发送A请求，然后等待服务器做出回应，收到后在发出B请求。管道机制允许浏览器同时发出A请求和B请求，但是服务器还是按照顺序，先回应A请求，完成后在回应B请求。
#### 非流水线和流水线。

* 流水线

不必等到收到服务器的回应就发送下一个报文。

* 非流水线

发出一个报文，等到下响应，再发下一个报文。类型TCP。





#### http和 https的区别

#### get 和 post的区别

* get

	1.Get会把请求的信息放到URL里面。
	2.Get不能大于2KB
	3.效率高
	4.请求会被浏览器主动Cache
	5.产生一个TCP数据包。
* post

	1.更加安全
	2.传输量不限制
	3.效率高
     4. 浏览器不会cache，除非主动设置。
     5. 产生两个数据包
	
### http 优化

#### 减少TCP连接

* Keep-Alive

  Connection:Keep-Alive
  Keep-Alive:timeout=120， max=5；
  
* Last-Modified
  
  Last-Modified;
  If-Modified-Since: 如果存在则返回，304： Not Modified， 获取本地数据。

* ETag
  Etag:标记
  IF-Note-Match：304
  
#### 对数据进行缓存

* 缓存 Expires和Cache-Control

####减少数据传输量

### HTTP消息头

HTTP的消息头分为：通用报头、请求报头、响应报头、实体报头。消息头由键值对组成。
	
#### Content-Type
	
HTTP1.0版规定，头信息必须是ASCII码，后面的数据可以是任何格式。因为，服务器回应的时候，必须告诉客户端，数据是是什么格式。常见的额Content-Type字段是
text/plan
text/html
text/css
image/jpeg
image/png
image/svg+xml
aduio/mp4
video/mp4
application/javascript
application/pdf
application/zip
application/atom+xml

可以自定义类型。
用Accept字段声明自己可以接受那些格式的数据。
Accept: \*/*

#### Content-Encoding 
说明字段的压缩格式。 
Content-Encoding: gizp
Accept-Encoding:gzip

#### Content-Length字段

生命本次回应的数据长度。

Content-Length:3495

#### Transfer-Encoding

只要请求或回应的头信息有Transfer-Encoding 字段，就表名回应将由数量未定的数据块组成。
Transfer-Encoding:chunked


### Http返回码

* 100-100： 指示信息，表示请求已接受，继续处理
* 200-299：请求成功，表示请求已被成功接收。
* 300-399：重定向，要完成请求必须进行更进一步操作。
* 400-499：客户端错误，请求有语法错误，或者请求无法实现。
* 500-599：服务器端错误。
* 200：成功
* 400 Bad Request: 语法错误。
* 401：请求未经授权
* 403：服务器收到请求，拒绝提供服务。
* 500：服务器发生不可预期的错误。
* 503：服务器当前不能处理客户端的请求。


### HTTPS

端口是443
由SSL+HTTP协议够爱金的可进行加密传输、身份认证的网络协议

### SYDY协议

	





