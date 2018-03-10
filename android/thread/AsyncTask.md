## 什么是AsyncTask

   AsyncTask适合在UI线程中使用，它允许在UI线程中操作后台操作和在UI线程中输出结果，而不用使用多线程和Handler机制。AsyncTask是封装后的一个后台任务类。

   AysncTask定义三种泛型类型：Params,Progress,Result
     
 * Params:启动任务执行的输入参数
 * Progress:启动任务执行的百分比
 * Result: 后台执行任务最终返回的结果。
     
## AsyncTask的优缺点
  * 优点
    1. AsyncTask 对Thread和Handler进行了封装，减少了复杂度，提高开发效率。
    
  * 缺点：
     1. 轻量级别的异步处理，默认是串行执行的，适合短暂的后台任务；如果高并发或者耗时的后台任务，不适合它来处理。
     2. AsyncTask虽封装好了一个线程池，但是它默认情况下同时执行5个线程，最大的容量是128个

## AsyncTask的使用的注意事项
   1. AsyncTask适合短时间的后台运算，一般只有几秒钟，如果长时间运算，还是使用线程池等等。
   2. 异步线程的实例必须在UI线程中创建
   3. AsyncTask实例只能执行一次。
   4. AsyncTask在1.6版本以前是串行执行，1.6到3.0之间为并行，3.0以后默认为串行执行，但是用户可以自定为并行执行任务。

## AsyacTask中的常用方法

1. execute(Params...params)执行一个异步任务，必须在UI线程中调用
2. onPreExecute() : 在调用execute后，由AsyacTask调用，此时还是在UI线程中。
3. doInBackgroud(); 抽象方法，必须由用户实现不能手工调用。
4. onProgressUpdate(Progress...values) 在publicProgress中调用，更新UI界面的进度。
5. onPostExecute():任务正常执行完毕后调用。
6. onCanceled():AsyacTask实例调用cancel方法后，在任务取消后调用。它和onPostExecute只能执行一个。

## AsyncTask的线程池有多大

private static final int CPU_COUNT = Runtime.getRuntime().availableProcessors();
private static final int CORE_POOL_SIZE = CPU_COUNT + 1;
private static final int MAXIMUM_POOL_SIZE = CPU_COUNT * 2 + 1;
private static final int KEEP_ALIVE = 1;

