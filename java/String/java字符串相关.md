# String 
   String不是基本类型，是一个不可继承的final对象。

   字符串池：每当创建一个字符串对象时，首先会检查字符串池中是否存在面值是否存在面值相等的字符串，如果有，则不再创建，直接返回字符串池中对该对象的饮用，若没有则创建然后放入到字符串中并返回新建对象的引用。这个机制可以提高效率，减少内存空间的占用。所有在使用字符串的过程中，推荐使用直接赋值 。

# StringBuffer
  StringBuffer和String几乎是一样的，但是对StringBuffer进行修改不会产生一个新的对象，所以在频繁修改字符串数值的时候，优先采用StringBuffer类。

# StringBuilder
   StringBuilder也是一个可变的字符串对象，但是它是不安全的，因此速度也比StringBuffer快。

