###java数据类型的知识点

* 自动拆箱和自动装箱
   自动装箱：把基本类型转换为包装类型。
   自动拆箱：就是把包装类型转为基本类型
* Integer的自动拆箱和自动装箱
   Integer的自动装箱是通过valueOf方法实现。对于范围在－128-127之间的int类型数，不是新创建一个对象，而是返回内存中已有的对象，对于不在此范围里面的数则新创建一个对象。
   Integer a=100, b = 100;
   integer c=128, d = 128
   a == b 为true， c ＝＝ d为false。
   因为在Integer类里面有个IntegerCache，存放－128-127范围内对象的Integer类对象。该类的静态方法valueOf的实现在当数据在－128-127范围内时，直接返回内隐类IntegerCache中存放的对应的Integer类对象。否则就new新生成一个对象。

