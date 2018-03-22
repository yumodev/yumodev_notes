#AIDL 文件


## 定义AIDL


```java

// IMyServiceAidl.aidl
package com.yumo.android.test.aidl;

// Declare any non-default types here with import statements

interface IMyServiceAidl {
     String getAidlInfo();
     void setAidlInfo(String aidl1);
}

```
## Build后生产的AIDL



```java
//包名同AIDL文件定义
package com.yumo.android.test.aidl;
// Declare any non-default types here with import statements

//IMyServiceAidl还是一个接口，扩展android.os.IInterface接口
public interface IMyServiceAidl extends android.os.IInterface {
    /**
     * Local-side IPC implementation stub class.
     */
    public static abstract class Stub extends android.os.Binder implements com.yumo.android.test.aidl.IMyServiceAidl {
        //Binder描述符，唯一的标识符。服务端和客户端都通过该ID定位到Binder实例。
        private static final java.lang.String DESCRIPTOR = "com.yumo.android.test.aidl.IMyServiceAidl";

       public Stub() {
            this.attachInterface(this, DESCRIPTOR);
        }

        /**
         * Cast an IBinder object into an com.yumo.android.test.aidl.IMyServiceAidl interface,
         * generating a proxy if needed.
         */
        public static com.yumo.android.test.aidl.IMyServiceAidl asInterface(android.os.IBinder obj) {
            if ((obj == null)) {
                return null;
            }
            //统一进程中，直接返回，返回调用代理。
            android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
            if (((iin != null) && (iin instanceof com.yumo.android.test.aidl.IMyServiceAidl))) {
                return ((com.yumo.android.test.aidl.IMyServiceAidl) iin);
            }
            return new com.yumo.android.test.aidl.IMyServiceAidl.Stub.Proxy(obj);
        }

        @Override
        public android.os.IBinder asBinder() {
            return this;
        }

        //
        @Override
        public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException {
            switch (code) {
                case INTERFACE_TRANSACTION: {
                    reply.writeString(DESCRIPTOR);
                    return true;
                }
                case TRANSACTION_getAidlInfo: {
                    data.enforceInterface(DESCRIPTOR);
                    java.lang.String _result = this.getAidlInfo();
                    reply.writeNoException();
                    reply.writeString(_result);
                    return true;
                }
                case TRANSACTION_setAidlInfo: {
                    data.enforceInterface(DESCRIPTOR);
                    java.lang.String _arg0;
                    _arg0 = data.readString();
                    this.setAidlInfo(_arg0);
                    reply.writeNoException();
                    return true;
                }
            }
            return super.onTransact(code, data, reply, flags);
        }

		   //私有静态内部类，继承了IMyServiceAidl。 代理远端的Binder。
        private static class Proxy implements com.yumo.android.test.aidl.IMyServiceAidl {
            //远程代理
            private android.os.IBinder mRemote;

            Proxy(android.os.IBinder remote) {
                mRemote = remote;
            }

            @Override
            public android.os.IBinder asBinder() {
                return mRemote;
            }

            public java.lang.String getInterfaceDescriptor() {
                return DESCRIPTOR;
            }

            @Override
            public java.lang.String getAidlInfo() throws android.os.RemoteException {
                android.os.Parcel _data = android.os.Parcel.obtain();
                android.os.Parcel _reply = android.os.Parcel.obtain();
                java.lang.String _result;
                try {
                    _data.writeInterfaceToken(DESCRIPTOR);
                    mRemote.transact(Stub.TRANSACTION_getAidlInfo, _data, _reply, 0);
                    _reply.readException();
                    _result = _reply.readString();
                } finally {
                    _reply.recycle();
                    _data.recycle();
                }
                return _result;
            }

            @Override
            public void setAidlInfo(java.lang.String aidl1) throws android.os.RemoteException {
                android.os.Parcel _data = android.os.Parcel.obtain();
                android.os.Parcel _reply = android.os.Parcel.obtain();
                try {
                    _data.writeInterfaceToken(DESCRIPTOR);
                    _data.writeString(aidl1);
                    mRemote.transact(Stub.TRANSACTION_setAidlInfo, _data, _reply, 0);
                    _reply.readException();
                } finally {
                    _reply.recycle();
                    _data.recycle();
                }
            }
        }

        static final int TRANSACTION_getAidlInfo = (android.os.IBinder.FIRST_CALL_TRANSACTION + 0);
        static final int TRANSACTION_setAidlInfo = (android.os.IBinder.FIRST_CALL_TRANSACTION + 1);
    }

    public java.lang.String getAidlInfo() throws android.os.RemoteException;

    public void setAidlInfo(java.lang.String aidl1) throws android.os.RemoteException;
}

```

## android_os_interface 


```java

/**
 * Base class for Binder interfaces.  When defining a new interface,
 * you must derive it from IInterface.
 */
public interface IInterface
{
    /**
     * Retrieve the Binder object associated with this interface.
     * You must use this instead of a plain cast, so that proxy objects
     * can return the correct result.
     */
    public IBinder asBinder();
}

```


## MyServiceAidl


```java
public class MyServiceAidl extends Service {
	
	private final String TAG = "MyServiceAidl";
	
	//定义Bindler
	public final IBinder mBinder = new IMyServiceAidl.Stub() {
		
		private String aidlInfo = "ImyServiceAidl";
		
		@Override
		public void setAidlInfo(String aidl) throws RemoteException {
			Log.i(TAG,"mBinder, setAidlInfo");
			aidlInfo = aidl;
		}
		
		@Override
		public String getAidlInfo() throws RemoteException {
			Log.i(TAG, "mBinder, getAidlInfo");
			return "getAidlInfo result";
		}
		
		@Override
		public boolean onTransact(int code, Parcel data, Parcel reply, int flags)
				throws RemoteException {
			return super.onTransact(code, data, reply, flags);
		}
	};

	public MyServiceAidl() {
	}


	@Override
	public IBinder onBind(Intent intent) {
		return mBinder;
	}
}
```

## 绑定服务



```java
if (mServiceConnection != null){
    return;
}

mServiceConnection = new ServiceConnection() {
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        myBinder = IMyServiceAidl.Stub.asInterface(service);
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        myBinder = null;
    }
};

Intent intent = new Intent(getActivity(), MyServiceAidl.class);
getActivity().bindService(intent, mServiceConnection, Service.BIND_AUTO_CREATE);
```

