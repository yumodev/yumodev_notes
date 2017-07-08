#ref

* [性能优化之数据库优化](http://www.trinea.cn/android/database-performance/)
# sqlite 

   一种轻量级的关系型数据库，运算速度非常的快，内存占用只有几百k，同时支持标准的sql语法遵循了ACID事务。
# 使用的时候的优化

   1、使用索引。
   2、使用事务
   3、查询时返回更少的结果集和字段
   
# 如何讲Sqlite数据库与Apk文件一起发布。
   讲数据库文件放到Res/raw目录下面。raw目录下的文件不会被压缩。但是Android不能直接打开在raw文件夹下的数据库文件。所以需要在第一次启动时将文件复制到手机内存或sd卡的某个目录中，然后在打开数据库文件。使用getResources().openRawResource方法获取数据库的inputstraem的对象，然后讲inputstream对象的数据写入到其他目录中的响应的文件。利用SqliteAPI就可以打开数据库文件了。


