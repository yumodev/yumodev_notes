## IBinder接口

IBinder是远程对象的基本接口。

```java


package android.os;

import java.io.FileDescriptor;

public interface IBinder {
   
    //第一个transaction
    int FIRST_CALL_TRANSACTION  = 0x00000001;
    //最后一个transaction
    int LAST_CALL_TRANSACTION   = 0x00ffffff;
    
  
    int PING_TRANSACTION        = ('_'<<24)|('P'<<16)|('N'<<8)|'G';
    
   
    int DUMP_TRANSACTION        = ('_'<<24)|('D'<<16)|('M'<<8)|'P';
    
   
    int SHELL_COMMAND_TRANSACTION = ('_'<<24)|('C'<<16)|('M'<<8)|'D';

   
    int INTERFACE_TRANSACTION   = ('_'<<24)|('N'<<16)|('T'<<8)|'F';

    int TWEET_TRANSACTION   = ('_'<<24)|('T'<<16)|('W'<<8)|'T';

    int LIKE_TRANSACTION   = ('_'<<24)|('L'<<16)|('I'<<8)|'K';

    /** @hide */
    int SYSPROPS_TRANSACTION = ('_'<<24)|('S'<<16)|('P'<<8)|'R';

 
    int FLAG_ONEWAY             = 0x00000001;
 
    public static final int MAX_IPC_SIZE = 64 * 1024;

    //获取当前Binder的描述符。
    public String getInterfaceDescriptor() throws RemoteException;

   
    public boolean pingBinder();

    
    public boolean isBinderAlive();
    
    //从本地进程中取出Binder。
    public IInterface queryLocalInterface(String descriptor);

    public void dump(FileDescriptor fd, String[] args) throws RemoteException;

    public void dumpAsync(FileDescriptor fd, String[] args) throws RemoteException;

    public void shellCommand(FileDescriptor in, FileDescriptor out, FileDescriptor err,
            String[] args, ShellCallback shellCallback,
            ResultReceiver resultReceiver) throws RemoteException;

     /**
     * 具体调用远程方法。
     */
     public boolean transact(int code, Parcel data, Parcel reply, int flags)
        throws RemoteException;

     public interface DeathRecipient {
        public void binderDied();
    }

     public void linkToDeath(DeathRecipient recipient, int flags)
            throws RemoteException;

     public boolean unlinkToDeath(DeathRecipient recipient, int flags);
}

```



