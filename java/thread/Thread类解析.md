### 类介绍

### 属性和方法

返回值|方法名|含义
------|-------|------
static int | activeCount | 线程组内正在运行的线程
static Thread | currentThread | 获取当前线程的实例
static void | dumpStack() | 打印标志的错误流
static Map<Thread, StackTraceElement[]>| getAllStackTraces() | 返回所有线程的Traces
static int | enumerate(Thread[] threads)|
static Thread.UncaughtExceptionHandler | getDefaultUncaughtExceptionHandler()|
static void | setDefaultUncaughtExceptionHandler(Thread.UncaughtExceptionHandler handler)
static boolean | holdsLock(Object object) |
static boolean | interrupted() |
static void | yield() | 
final void | checkAccess |
ClassLoader | getContextClassLoader()| 返回当前线程的ClassLoader
long | getId() | 获取线程id，唯一标识一个线程
final String | getName() | 获取线程名字
final int | getPriority() | 获取线程优先级
StrackTraceElement[] | getStackTrace() |
Thread.State | getState() | 线程状态
final ThreadGroup | getThreadGroup() | 
void | interrupt() |
final boolean | isAlive() | 是否还活着
boolean | isInterrupted() |
final void | join() |
final void | join(long millis, int nanos)
final void | join(long millis)
void | run() | 
void | setContextClassLoader(ClassLoader cl) |
final void | setDaemon(boolean isDaemon) | 设置是否为守护线程
final void | setName(String threadName) | 设置线程名字
final void | setPriority(int priority) | 设置优先级
void | setUncaughtExceptionHandler(Thread.UncaughtExceptionHandler)
static void | sleep(long millis, int nanos)
static void | sleep(long time) | 当前线程休眠一段时间，单位为毫秒。
synchronized void | start() | 开启线程







