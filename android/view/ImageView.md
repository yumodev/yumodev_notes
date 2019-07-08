# ImageView

## ScaleType

* matrix 默认值

默认值，不改变原图的大小，从ImageView的左上角开始绘制原图， 原图超过ImageView的部分作裁剪处理

* fixXY

对图像的横向与纵向进行独立缩放,使得该图片完全适应ImageView,但是图片的横纵比可能会发生改变

* firStart

保持纵横比缩放图片,知道较长的边与Image的编程相等,缩放完成后将图片放在ImageView的左上角

* fitCenter

保持纵横比缩放图片,知道较长的边与Image的编程相等,缩放完成后将图片放在ImageView的中间

* fitEnd

保持纵横比缩放图片,知道较长的边与Image的编程相等,缩放完成后将图片放在ImageView的右下角

* center

保持原图的大小，显示在ImageView的中心。当原图的size大于ImageView的size，超过部分裁剪处理。

* centerCrop

保持横纵比缩放图片,知道完全覆盖ImageView,可能会出现图片的显示不完全

* centerInside

保持横纵比缩放图片,直到ImageView能够完全地显示图片


