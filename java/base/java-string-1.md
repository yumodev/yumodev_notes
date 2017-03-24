<h1 align="center" >String 字符串的解析 </h1>
    java String类是一个使用频率非常高的类。借助分析String类的源码，有助于更好的了解java中字符串的操作，写出更加有效率的代码。
    
###     String 定义
       public final class String implements Serializable, Comparable<String>, CharSequence｛｝
       String首先是一个final类，它将不能被继承生成一个子类。String为什么要设置为final类型主要有2个方面的考虑。
       1、处于安全性的考虑.String类是java中一个非常底层的类，和操作系统进行的频繁的交互。如果可以String类可以被继承，用户就可能会重写String类的相关方法，这就提供破坏操作系统提供了机会，降低了java的宣称的安全性。
       2、效率的问题：String被定义为final类，那么它的所有方法都是final的，jvm编译器会内联所有的方法，此举可以让String类的效率使用50%，单凭这点就让人心动不已了。
       
       String类继承了Serializable ,所以String类是可序列化的。
       String类继承了Comparable, 所以String可以在集合里面，直接用sort()排序。
       String类继承实现了CharSequence ,所以String类本质上是一个字符序列。
       
###     String 成员变量
      //定义一个字符数组来存放 String的字符序列。
      private final char[] value;
      // 字符偏移的位置。
      private final int offset;
      //String的字符的个数。可以通过length() 方法返回。
      private final int count;
      //该字符串对应生产的一个哈希值。
      private int hashCode;
      
###       String 初始化
        String的类的初始化方法非常多，非常的丰富，所以初始化String对象还是很方便的。
        
        //声明一个空字符串
         public String() {
              value = EmptyArray.CHAR;
              offset = 0;
             count = 0;
        }
        
        利用byte[] 生成为字符串。
        //下面几个是利用字节数组转换一个新的String对象。将字节数组解码为字符串的字符集可以制定，也可使使用系统默认
        public String(byte[] data) ;//将一个字节数组生成一个新的字符串。使用默认字符集
        public String(byte[] data, int offset, int byteCount) ；将数组从offset位置后读取byteCount个字节转换为数组。使用默认字符集。
        
        将字节数据转换为从offset位置读取byteCount个字节转换为数组，使用用户指定的字符集，如果不支持该字符集，就抛出UnsupportedEncodingException.
        public String(byte[] data, int offset, int byteCount, String charsetName) throws UnsupportedEncodingException 
        将字节数据转换为从offset位置读取byteCount个字节转换为数组，使用用户指定的字符集，如果不支持该字符集，就抛出UnsupportedEncodingException.
        public String(byte[] data, int offset, int byteCount, Charset charset) ;
        利用用户制定的字符集.将byte数组生成一个新的字符串。
        public String(byte[] data, Charset charset) ;
        
        利用char[] 声明一个字符串。
         public String(char[] data)//将一个char数组声明一个新的String对象。内部调用public String(char[] data, int offset, int charCount)实现
          
          /**
	    * 用char数组生命一个新的字符串变量。然后然后修改这个char数组将不再影响新生成的字符串。	    *
	    * 当 data为null的时候，报空指针异常。
	    * @throws IndexOutOfBoundsException
	    *             if {@code charCount < 0 || offset < 0 || offset + charCount > data.length}
	    */
	   public String(char[] data, int offset, int charCount) {
	      //检查是否越界
	       if ((offset | charCount) < 0 || charCount > data.length - offset) {
	           throw failedBoundsCheck(data.length, offset, charCount);
	       }
	       this.offset = 0;
	       //新生成一个string对象 ，所有新生的char数组和旧的数组没有关联。
	       this.value = new char[charCount];
	       this.count = charCount;
	       System.arraycopy(data, offset, value, 0, count);
	   }
	   
	   /*
	     * 直接用char数组生产一个字符串对象。其中char[]没有在new 一个新的数组，所以二者是一个char数组。	     * Does not range check, null check, or copy the character array.
	     */
	    String(int offset, int charCount, char[] chars) {
	        this.value = chars;
	        this.offset = offset;
	        this.count = charCount;
	    }
	    
	     /**
	     * 用一个字符串的副本构造一个新的字符串。
	     */
	    public String(String toCopy) {
	        value = (toCopy.value.length == toCopy.count)
	                ? toCopy.value
	                : Arrays.copyOfRange(toCopy.value, toCopy.offset, toCopy.offset + toCopy.length());
	        offset = 0;
	        count = value.length;
	    }

         /**
	     * 用字符缓冲区的字符序列构建一个字符串。是线程安全的。
	     * {@code StringBuffer}.
	     */
	    public String(StringBuffer stringBuffer) {
	        offset = 0;
	        synchronized (stringBuffer) {
	            value = stringBuffer.shareValue();
	            count = stringBuffer.length();
	        }
	    }
	
	    /**
	     * 用int数组构建一个字符串，字符串的序列为unicode字符。	     *
	     * @throws NullPointerException
	     *             if {@code codePoints == null}.
	     * @throws IllegalArgumentException
	     *             if any of the elements of {@code codePoints} are not valid
	     *             Unicode code points.
	     * @throws IndexOutOfBoundsException
	     *             if {@code offset} or {@code count} are not within the bounds
	     *             of {@code codePoints}.
	     * @since 1.5
	     */
	    public String(int[] codePoints, int offset, int count) {
	        if (codePoints == null) {
	            throw new NullPointerException("codePoints == null");
	        }
	        if ((offset | count) < 0 || count > codePoints.length - offset) {
	            throw failedBoundsCheck(codePoints.length, offset, count);
	        }
	        this.offset = 0;
	        //unicode 字符占用两个字节，int型数据为4个字节。所以要乘以二。
	        this.value = new char[count * 2];
	        int end = offset + count;
	        int c = 0;
	        for (int i = offset; i < end; i++) {
	            c += Character.toChars(codePoints[i], this.value, c);
	        }
	        this.count = c;
	    }
	
	    /**
	     * 构建一个新的string对象，包含字符串生成器的字符序列。
	     *
	     * @throws NullPointerException
	     *             if {@code stringBuilder == null}.
	     * @since 1.5
	     */
	    public String(StringBuilder stringBuilder) {
	        if (stringBuilder == null) {
	            throw new NullPointerException("stringBuilder == null");
	        }
	        this.offset = 0;
	        this.count = stringBuilder.length();
	        this.value = new char[this.count];
	        stringBuilder.getChars(0, this.count, this.value, 0);
	    }
        
        
       

