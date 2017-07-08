1、Lock和Synchronized的优缺点
   1、Lock实现了所有的Synchronized的功能，并且比其更加的强大和灵活。
   2、通过Lock()获取锁，必须通过unLock()方法来释放锁，否则就会造成死锁结构。故而Lock()方法一般定义在try-finnally 或者try－catch－finnally里面。在finnally()里面释放锁。
   3、Lock比Synchronized提供了更多的功能，比如tryLock方法，可以实现尝试获取锁的控制权
   4、Lock可以实现读写操作分离。
2、Lock的公平性。
   ReetrantLock(Boolean fair) 通过fair来设置是否为公平锁。
   公平锁：JVM从众多等待线程中，选择等待时间最长的一个。
   非公平锁：JVM从众多等待线程中，随机选择一个线程来执行。
