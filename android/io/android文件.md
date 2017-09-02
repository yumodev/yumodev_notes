### android文件目录为

   /data/data/<package name>/files。
   可以在Activity中通过getFilesDir()方法获取该目录
### android的缓存目录为：

   /data/data//<package name>/cache
   可以在Activity中通过getCacheDir()方法获取该目录
  
### 有几种存储方式，分别介绍一下
   SharePreference; 文件存储数据；Sqlite数据库存储数据；ContentProvider存储数据；网络存储数据。
   Preference 对应的目录是：／data/data/Package Name/Shared_Pref, 
   文件存储对应的目录为：/data/data/Package Name/files,
   sqlite数据库对应的目录为： /data/data/package Name/database


### android有几种解析xml的方式，分别是什么，原理是什么，有什么区别
   XML解析主要有三种方式：SAX，DOM，PULL。
   SAX：在手机上优先采用，它读取时单向的，不占用内存空寂哪，解析属性方便，但对于套黔的多个分支解析起来比较麻烦。
   DOM：把整个XML文件加入到内存当中，如果文件大的话，会造成内存溢出。
   pull：类似SAX方式


