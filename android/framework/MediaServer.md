# MediaServer

## MediaServer 初始化函数

Main_MediaServer

```c++

using namespace android;

int main(int argc, char** argv)
{
    sp<ProcessState> proc(ProcessState::self());
    sp<IServiceManager> sm = defaultServiceManager();
    LOGI("ServiceManager: %p", sm.get());
    AudioFlinger::instantiate();
    MediaPlayerService::instantiate();
    CameraService::instantiate();
    AudioPolicyService::instantiate();
    ProcessState::self()->startThreadPool();
    IPCThreadState::self()->joinThreadPool();
}
```

## ProcessState

[ProcessState.cpp](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/libs/binder/ProcessState.cpp)

ProcessState是单例模式，每个进程只能有一个。

```c++
sp<ProcessState> ProcessState::self()
{
    if (gProcess != NULL) return gProcess;
    
    AutoMutex _l(gProcessMutex);
    if (gProcess == NULL) gProcess = new ProcessState;
    return gProcess;
}
```

构造方法


```c++
ProcessState::ProcessState()
    : mDriverFD(open_driver())
    , mVMStart(MAP_FAILED)
    , mManagesContexts(false)
    , mBinderContextCheckFunc(NULL)
    , mBinderContextUserData(NULL)
    , mThreadPoolStarted(false)
    , mThreadPoolSeq(1)
{
    if (mDriverFD >= 0) {
        // XXX Ideally, there should be a specific define for whether we
        // have mmap (or whether we could possibly have the kernel module
        // availabla).
#if !defined(HAVE_WIN32_IPC)
        // mmap the binder, providing a chunk of virtual address space to receive transactions.
        mVMStart = mmap(0, BINDER_VM_SIZE, PROT_READ, MAP_PRIVATE | MAP_NORESERVE, mDriverFD, 0);
        if (mVMStart == MAP_FAILED) {
            // *sigh*
            LOGE("Using /dev/binder failed: unable to mmap transaction memory.\n");
            close(mDriverFD);
            mDriverFD = -1;
        }
#else
        mDriverFD = -1;
#endif
    }
    if (mDriverFD < 0) {
        // Need to run without the driver, starting our own thread pool.
    }
}
```

* open_driver 
打开/dev/binder这个设备，它是Android在内核中为完成进程间通信而专门设置的一个虚拟设备

```c++
static int open_driver()
{
    if (gSingleProcess) {
        return -1;
    }

    int fd = open("/dev/binder", O_RDWR);
    if (fd >= 0) {
        fcntl(fd, F_SETFD, FD_CLOEXEC);
        int vers;
#if defined(HAVE_ANDROID_OS)
        status_t result = ioctl(fd, BINDER_VERSION, &vers);
#else
        status_t result = -1;
        errno = EPERM;
#endif
        if (result == -1) {
            LOGE("Binder ioctl to obtain version failed: %s", strerror(errno));
            close(fd);
            fd = -1;
        }
        if (result != 0 || vers != BINDER_CURRENT_PROTOCOL_VERSION) {
            LOGE("Binder driver protocol does not match user space protocol!");
            close(fd);
            fd = -1;
        }
#if defined(HAVE_ANDROID_OS)
        //支持的最大线程数是15个
        size_t maxThreads = 15;
        result = ioctl(fd, BINDER_SET_MAX_THREADS, &maxThreads);
        if (result == -1) {
            LOGE("Binder ioctl to set max threads failed: %s", strerror(errno));
        }
#endif
        
    } else {
        LOGW("Opening '/dev/binder' failed: %s\n", strerror(errno));
    }
    return fd;
}

```

## defaultServiceManager

[IServiceManager.cpp](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/libs/binder/IServiceManager.cpp)

defaultServiceManager()返回BpServiceManager。

BpBinder 是客户端用来与Server交互的代理类，p是Proxy。
BBinder： 是Proxy交换的目的端。

## 注册MediaPlayerService

[MediaPlayerService](https://www.androidos.net.cn/android/2.3.7_r1/xref/frameworks/base/media/libmediaplayerservice/MediaPlayerService.cpp)

```c++
void MediaPlayerService::instantiate() {
    defaultServiceManager()->addService(
            String16("media.player"), new MediaPlayerService());
}


//IServiceManager.cpp
virtual status_t addService(const String16& name, const sp<IBinder>& service)
{
    Parcel data, reply;
    data.writeInterfaceToken(IServiceManager::getInterfaceDescriptor());
    data.writeString16(name);
    data.writeStrongBinder(service);
    status_t err = remote()->transact(ADD_SERVICE_TRANSACTION, data, &reply);
    return err == NO_ERROR ? reply.readExceptionCode() : err;
}
```


## StartThread 和 JoinThreadPool


## ServiceManger

ServiceManager能集中管理系统内的所有服务，它能施加权限控制，并不是任何进程都能注册服务的。
ServiceManager支持通过字符串名称来查找对应的Service，像DNS。




