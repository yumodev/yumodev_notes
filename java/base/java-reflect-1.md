<h1 align="center" >反射-一个简单的实例应用 </h1>

    反射就是通过一个类的名字或者实例，才操作它的属性和方法的机制。该机制在android开发中非常有用。因为android开发中很多API里的一些方法没有公开，就需要用反射机制才能使用它。
        
###    什么是Class
    我定义一个类的时候需要使用Class关机字。比如 Class A{};我们定义了一个名字为A的类对象，那么我们就可以理解为Class 就是类对象A的类型。这个想法有点抽象。
    Class类有两个重要的方法。
    getName(); 获取这个对象的名字。
    forName(); 这个是静态方法。通过一个类的名字，获取到这个类的类型说明，我也不知道该怎么描述，只要不要理解成一个类对象的实例就行。
    newInstance(); 通过这个成员方法可以获得一个类的心的实例。
        
    下面我我们看一个列子。给一个类的名字，来打印这个类的所有字段和方法。       
     
		/**
		 * TODO 给定一个类名，打印它的属性，方法等等。
		 * yumo
		 * @param className
		 * void
		 * 2015-1-4
		 */
		public void printClass(String className)
		{
			if(className == null || className.length() <=0) return ;
			
			try {
				//返回与带有给定字符串名的类或接口相关联的 Class 对象。
				Class cls = Class.forName(className);
				//一个字符串的权限。
				String modifiers = Modifier.toString(cls.getModifiers());
				System.out.print(modifiers);
				// 打印类的名字
				System.out.print(cls.getName());
				// 获取它的父类。
				Class superCls = cls.getSuperclass();
				if(superCls != null) System.out.print(" extends " + superCls.getName());
				System.out.println("{");
				
				//打印field 成员变量
				Field[] fields = cls.getFields();
				for(Field field:fields)
				{
					//一个字符串的权限。
					modifiers = Modifier.toString(field.getModifiers());
					System.out.print(modifiers + " ");
					//打印类型
					System.out.print(field.getType() + " ");
					//打印方法的名字
					System.out.print(field.getName());
					
					System.out.println();
				}
				
				//打印methods 
				Method[] methods = cls.getMethods();
				for(Method method : methods)
				{
					//一个字符串的权限。
					modifiers = Modifier.toString(method.getModifiers() );
					
					System.out.print(modifiers + " ");
					//打印返回类型
					System.out.print(method.getReturnType().getName() + " ");
					//打印方法的名字
					System.out.print(method.getName()+"(");
					//打印参数
					Class[] parameters = method.getParameterTypes();
					for(int i = 0; i < parameters.length; i++)
					{
						if(i > 0) System.out.print(",");
						Class para = parameters[i];
						System.out.print(para.getName());
					}
					
					System.out.println(")");
				}
				
				System.out.println("}");
				
				
			} catch (ClassNotFoundException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			}
		
	当我们用这个语句打印Date类的方法，printClass("java.util.Date");
	打印的结果如下，它完整的输出一个类的结构。
	
		publicjava.util.Date extends java.lang.Object{
		public boolean equals(java.lang.Object)
		public java.lang.String toString()
		public int hashCode()
		public java.lang.Object clone()
		public int compareTo(java.util.Date)
		public volatile int compareTo(java.lang.Object)
		public boolean after(java.util.Date)
		public boolean before(java.util.Date)
		public static long parse(java.lang.String)
		public void setTime(long)
		public long getTime()
		public int getYear()
		public int getMonth()
		public int getDate()
		public int getHours()
		public int getMinutes()
		public int getSeconds()
		public static long UTC(int,int,int,int,int,int)
		public void setDate(int)
		public void setMonth(int)
		public void setHours(int)
		public void setMinutes(int)
		public void setSeconds(int)
		public void setYear(int)
		public int getDay()
		public java.lang.String toLocaleString()
		public java.lang.String toGMTString()
		public int getTimezoneOffset()
		public final void wait()
		public final native void wait(long)
		public final void wait(long,int)
		public final native java.lang.Class getClass()
		public final native void notify()
		public final native void notifyAll()
		}
	
### 	通过反射操作一个类的字段。
     我们定义一个Book类。有两个属性，书名和价格。定义如下。
	       /**
		 * yumo
		 * 定义一个用于测试的Book类
		 */
		class Book
		{
			public String name = "";
			
			public double price = 0.0;
			
			public Book(String name, double price)
			{
				this.name = name;
				this.price = price;
			}
			
			public double getPrice(){
				System.out.println("getPrice 进入运行了");
				return price;
			}
			
			public void setPrice(Double price)
			{
				System.out.println("setPrice 进入运行了");
				this.price = price;
			}
		}
		
		这里我们先看一个Class的一个方法。
		Field getDeclaredField（String; //给定一个字段名称，获取到该字段的实例。
		Field类是表示一个Class中的一个字段。它主要有三个方法。
		getName(); 获取字段的名称。
		get(Object): 获取制定对象上该字段的值。
		set(Object obj, Object value); 将指定对象变量上此 Field 对象表示的字段设置为指定的新值。
		
		下面就用一个列子来展现它的用法。
		我们首先定义一个Book类的实例，然后通过实例获取到它的类对象clsBook.
		然后通过clsBook的getField("name")方法获取到name字段的Field的说明。
		然后调用Field对象的get和set方法对该字段惊醒操作。
		
		Book book = new Book("Android高级编程",74);
		//获取book对象
		Class clsBook = book.getClass();
		try {
			//获取book对象的，name字段
			Field name = clsBook.getDeclaredField("name");
			//获取并打印name的值
			System.out.println("通过反射获取的书名为："+name.get(book));
			//修改name的值
			System.out.println("通过反射获取的将书名修改为：android 开发入门详解");
			name.set(book, "android 开发入门详解");
			//输出修改后的name的值
			System.out.println("通过反射修改后书名为："+book.name);
		} catch (SecurityException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (NoSuchFieldException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}catch (IllegalArgumentException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}catch (NoSuchMethodException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}catch (InvocationTargetException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
	打印结果如下：
	
		通过反射获取的书名为：Android高级编程
		通过反射获取的将书名修改为：android 开发入门详解
		通过反射修改后书名为：android 开发入门详解

###      通过反射操作一个类的方法

	我们可以通过反射机制在运行当中动态的获取一个实例方法，来操纵这个方法。需要用到相关类的方法如下
	Class类中的方法
	Method getMethod(String name, Class<?>... parameterTypes)   返回一个 Method 对象，它反映此 Class 对象所表示的类或接口的指定公共成员方法。
	Method 顾名思义表示的一个类型方法类型。它有两个常用的方法。
	getName(); 获取这个方法的名称。
	Object	invoke(Object obj, Object... args)  对带有指定参数的指定对象调用由此 Method 对象表示的底层方法。
	下面我们通过一个列子，领略一下反射魅力。

	       Book book = new Book("Android高级编程",74);
		//获取book对象
		Class clsBook = book.getClass();
		try {			
			//通过反射调用方法
			Method getPrice = clsBook.getMethod("getPrice",null);
			getPrice.invoke(book);
			System.out.println("现在书的价格为:"+book.getPrice());
			
			Double price = 100.0;
			Class[] argsCls = new Class[1];
			argsCls[0] = price.getClass();
			
			Method setPrice = clsBook.getMethod("setPrice", argsCls);
			setPrice.invoke(book, price);
			System.out.println("通过反射通过反射将书的价格修改:"+price);
			
			getPrice = clsBook.getMethod("getPrice",null);
			getPrice.invoke(book);
			System.out.println("通过反射修改后，书的价格为:"+book.getPrice());
		} catch (SecurityException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (NoSuchFieldException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}catch (IllegalArgumentException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}catch (NoSuchMethodException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}catch (InvocationTargetException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
	
	打印结果如下：
	
		现在书的价格为:74.0
		setPrice 进入运行了
		通过反射通过反射将书的价格修改:100.0
		getPrice 进入运行了
		getPrice 进入运行了
		通过反射修改后，书的价格为:100.0


