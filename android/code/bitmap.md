
### Bitmap

#### BitmapFactory

BitmapFactory类可以根据各种不同的数据源来构建Bitmap位图对象。 BitmapFactory有一个内部类：BitmapFactory.options，其参数如下

* inBitmap :如果设置那么，在加载Bitmap的时候会尝试去重用这块内存，不能重用的时候，会返回null，否则返回Bitmap
* inDensity:原始图片的像素密度。
* inDiter:是否采用抖动界面。
* inInputShareable：与inPurgeable一起使用。如果inPurgeable设置为true，那么它可以决定位图是否能够共享一个纸箱数据源的引用，或者进行一份拷贝。
* inJustDecodeBounds: 当为True的时候Bitmap返回null，但是其他的一些option心事还是会返回的，
* inMutable:如果设置为true的话，家吗方法将始终返回一个可变的位图。
* inPreferQuilityWverSpeed：如果设置为true，解码器将尝试重建图片以获得更高质量的解码，甚至牺牲解码速度。
* inSampleSize：如果大于1，将等比缩放图像，以节约内存。
* outMimeTyoe。

### 面试相关
#### 一个Bitmap究竟占用多大的内存。

占用的内存由像素点的个数和单位像素占用的字节数决定的。

getRowBytes()\*getHeight()

4.4后右提供了getAllocationByteCount方法获取实际占用的内存。

一个1024\*768的图片占用的字节数为
1024\*768\*单位像素的字节数

#### Bitmmap内存和优化

在Andorid3.0 以后，所有的内存都分配在VM堆中，不在需要调用Recycle方法使用Bitmap的内存。
优化如下：
* 使用软件用使用缓存。
* 复用图片。
* 降低采样率和缩放图片。

####  Bitmap能否复用。

BitmapOptions.inBitmap 设置为true的时候，就可以复用了。
4.4 一下必须保证图片的大小一致，4.4 以后，二次复用的图片，只要保证比源图片资源大就可以了。

#### 渐进式显示大图，如何做到。

Fresco库可以做到。

#### 图片合适区分。

touchDelegate

#### 图片的压缩原理









