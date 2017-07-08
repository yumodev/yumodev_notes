1、为什么使用线程池。
   如果开发一个运行的的多并发任务程序，可以采用线程池，也可以采用普通的Runnable和Thread实现。
   如果采用普通的Runnable和Thread实现将会有很多的缺点主要如下
      1、你必须要实现很多相关的代码来管理Thread对象的创建，结束，获得结果等等。
      2、你必须为每一个任务创建一个Thread对象，如果执行一个大数量的任务，可能会影响应用程序的吞吐量。
      3、你必须有效的控制和管理计算机资源。如果创建太多线程，将会占用太多资源，影响程序性能，甚至造成死机。
    java提供的Executor机制可以解决上述问题。这个机制将任务的创建和执行分离。使用执行者，你只要实现Runnable对象并把它交给执行者。执行者负责执行，实例话和运行这些线程。除了这些，它还可以使用线程池提高性能。

    Callable借口是ExecutorFramework的另一个重要优点，它跟Runnable借口非常的相信，它做了两点改进1、提供call方法，可以反悔结果2、将callable接口提交到执行者，将会获取一个实现Future接口的对象，就可以使用这个对象来控制callable对象的状态和结果。
2、ThreadPoolExecutor
   ThreadPoolExecutor类和一般的执行者的不同的是你必须明确的结束它，如果没有主动结束，这个执行者会继续它的执行，并且这个程序不会结束。如果执行者没有任务可执行，它会等待新任务并且不会结束它的荤腥。一个java应用程序将不会结束，除非所有的非守护线程完成它们的执行，如果你结束这个执行者，你的应用程序将不会结束。
   
3、四种不同类型的线程池创建方法。
   1、创建缓存线程池。
      Executors.newCachedThreadPool()
      缓存线程池，不限线程执行个数，线程执行完后，线程池也不会退出，会等待新的线程。如果执行大量的线程，同时是长时间运行的线程的时候，会严重影响性能。
      缓存线程池，当需要执行新的任务会创建新的线程，如果它们已经完成运行任务，变成可以用状态，会重新使用这些线程。线程复用的好吃是，它减少线程的创建时间，缓存线程池的缺点就是，为新任务不断的创建线程。如果你提交过多的任务给执行者，会使系统超载。
   2、创建固定大小的线程池
      Executors.newFixedThreadPool(5);
      线程池游最大的线程数目，如果提交的任务超过这个最大的线程数，这个执行者将不会创建额外的线程，并且将剩下的任务组塞，直到执行者有空闲的线程。保证执行者不会引发应用程序性能不佳的问题。
   3、创建单个线程的线程池
      Executors.newSingleThreadExecutor()
   4、创建延时运行的线程池。
      Executors.newScheduledThreadPool(1)
4、ThreadPoolExecutor常用方法
   shutDwon():结束执行者。
   shutdownNow(): 此方法立即关闭执行者，它不会执行待处理的任务，但它会返回待处理任务的列表。调用这个方法后，正在运行的任务继续他们的执行。但是这个方法并不等待他们结束。
   getPoolSize(); 此方法返回线程池的实际的线程数目。
   getActiveCount():  返回正在执行的任务的线程数目。
   getCompletedTaskCount:此方法返回执行者完成的任务数目。

5、有返回结果的任务
   Executor Framework的一个优点就是可以并发执行返回结果的任务。有下面两个接口实现
   1、callable ：此接口只有个call方法，在这个方法中 ，必须实现业务逻辑。Callable接口是一个参数化的接口，意味着使用时必须表明call()方法返回的数据类型。
   Future: 此接口有一些方法保证Callable对象结果的获取和管理它的状态。
   isDone：检测任务是否已经完成
   get()：获取任务的call方法返回的结果。如果任务已经完成，获取get方法将会获取结构，如果任务尚未完成，那么调用get方法将会组塞知道任务完成。在get方法在等待结果时被中断，它将抛出InterruptedException异常。如果在等待结果的过程中，call方法抛出异常，那么get方法会抛出ExecutionException异常。
6、运行多个任务返回第一个任务完成的结果
   主要是通过ThreadPoolExecutor类中的invokeAny() 方法借口任务数列，并启动它们，返回完成时没有抛出异常的第一个任务的结果。
   如果所有的任务都抛出异常，那么invokeAny()方法也将抛出一个ExecutionException

7、运行多个任务并处理所有结果
   通过ThreadPoolExecutor类的invokeAll实现。
   List<Future<result >> list = executor.invokeAll(new List<Callable<Result>()>)

8、执行者延迟运行一个任务。
   通过ScheduledThreadPoolExecutor类来实现延迟运行一个任务。
   首先使用Executores类的newScheduledThreadPool()方法创建ScheduledThreadPoolExecutor类的一个执行者，executor.
   其次调用ScheduleThreadPoolExecutor类的schedule方法，初始化和开始一些任务
      该方法有三个参数如下：
      想要执行的任务
      想要任务执行前等待的时间。
      时间单位，指定为TimeUnit类的常数。
   最后shutdown执行者。
   使用执行者的awaitTermination()方法等待所有的任务完成。

   当调用ScheduledThreadPoolExecutor类的shutdown方法时，如果有待处理的任务正在等待他们的延迟结束。默认行为是不管延迟是否结束这些任务都将被执行。可以通过ScheduledThreadPoolExecutor类的setExecuteExistingDelayedTasksAfterShutDownPolicy()方法来改变这种行为。设置为false时，调用shutdown时，待处理的任务不会被执行。






















