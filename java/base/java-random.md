<h1 align="center" >java 随机数－常用操作集合</h1>

   随机数用的场合非常多，但是使用起来并不复杂，非常的简单。下面就是他的一些列方法的使用
      
        Random random = new Random(100);
		
		//随机生成10个int数字  next()
		System.out.println("随机生成5个int数字  next()");
		for(int n =0; n < 5; n++)
		{
			System.out.println(random.nextInt());
		}
		
		//生成5个 0到100之间的数字。
		System.out.println("生成5个 0到100之间的数字。 nextInt(100)");
		for(int n =0; n < 5; n++)
		{
			System.out.println(random.nextInt(100));
		}
		
		//随机生成5个boolean值
		System.out.println("随机生成5个boolean值 netBoolean");
		for(int n =0; n < 5; n++)
		{
			System.out.println(random.nextBoolean());
		}
		
		//随机生成5个long值
		System.out.println("随机生成5个long值 random nextLong()");
		for(int n =0; n < 10; n++)
		{
			System.out.println(random.nextLong());
		}
		
		//随机生成5个fload值
		System.out.println("随机生成5个fload值 netFloat");
		for(int n =0; n <5; n++)
		{
			System.out.println(random.nextFloat()*100);
		}
		
		//随机生成5个double值
		System.out.println("随机生成5个double值 netDouble");
		for(int n =0; n < 5; n++)
		{
			System.out.println(random.nextDouble()*100);
		}
		
		//随机生成10个Gaussian值
		System.out.println("随机生成5个太随机数  nextGaussian()");
		for(int n =0; n < 5; n++)
		{
			System.out.println(random.nextGaussian()*100);
		}
			
		
		//
		System.out.println("建立一个字节数组，来接收随机生成的字节。 nextBytes(byte[])");
		
		byte[] bytes= new byte[10];
		random.nextBytes(bytes);
		for(byte bt : bytes)
		{
			System.out.println(bt);
		}
	
		
执行上面的代码。我们可以等到如下的结果。
		
		生成5个 0到100之间的数字。 nextInt(100)
		66
		36
		88
		23
		13
		随机生成5个boolean值 netBoolean
		true
		false
		false
		false
		false
		随机生成5个long值 random nextLong()
		1994076515850003689
		-3505880182545671094
		5294845296780165755
		7666949800837858506
		-9143039415690246354
		1622479998420556049
		5700377594808985065
		234374027031053629
		-7316603517567067640
		3586133391558490009
		随机生成5个fload值 netFloat
		86.28953
		15.13797
		91.9473
		12.135201
		23.321033
		随机生成5个double值 netDouble
		41.3242845037923
		28.672630466619474
		62.46190087763299
		73.14622835577995
		48.603697468545924
		随机生成5个张太随机数  nextGaussian()
		-218.73465232186376
		-139.0458132709607
		-170.60989533724836
		-31.26162845667001
		80.45245617401423
		建立一个字节数组，来接收随机生成的字节。 nextBytes(byte[])
		-48
		-50
		-114
		12
		-58
		123
		-118
		-39
		104
		
	

		

