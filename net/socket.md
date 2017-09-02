1、socket 实现一个客户端和服务器的简单描述
   客户端实现：
   try{
        Socket socket = new Socket("192.168.0.1",1900);
        InputStream inputStream = new FileInputStream("e://a.txt");
        OutputStream out = socket.getoutputStream();
        byte buffer[] = new byte[4*1024];
        int temp = 0;
        while((temp = inputStream.read(buffer) != -1)
        {
          out.write(buffer,0,temp);
        }
        out.flush();

        InputStream in = socket.getInputStream();
        StringBuffer strBuffer = new StringBuffer();
        while((temp = in.read(buffer) != -1))
        {
            strBuffer.append(buffer);
        }
   }

   服务器端的实现：
     ServerSocket serverSocket = null;
     try{
        serverSocket = new ServerSocket(1900);
        while(true)
        {
            Socket socket = serverSocket.accept();
            //读取
            InputStream inSocket= socket.getinputStream();
            byte buffer[] = new byte[4*1024];
            int temp = 0;
            while((temp = inSocket.read(buffer)) != -1)
            {
                 System.out.println(new String(buffer,0,temp));
            } 
        }
        
     }finally{
        serverSocket.close();
     }
2、如果服务器这边用write写了100字节的数据客户端该如何接受呢

输入ip地址将其转换为int



