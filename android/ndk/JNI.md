# JNI

##概念

JNI是Java Native Interface的缩写

## 使用

### 加载so库

加载JNI库一般在类的static语句中加载，传入库的名字

```java
static{
	System.loadLibrary(soName).
}
```

### 定义一个Native方法

Native方法使用Native修饰的函数
```
private static native void init();
```

## 注册JNI函数

### 静态注册

### 动态注册

JNINativeMetiod的结构


```c++
typedef struct{
	const char* name;//Java中函数的名字，不包含包的路径。
	const char* signature;//Java的签名信息，用字符串表示。
	void* fnPtr; //JNI层函数的函数指针，注意它是void* 类型。
}
```

AndroidRuntime类提供了一个registerNativeMethods函数来动态注册。

System.loadLibrary加载完JNI动态后紧接着查找库中JNI_OnLoad函数

## JNI类型转换

引用数据类型的转换关系

Java引用类型|Native类型
------|------|------|------
All Objects | jobject 
java.lang.Class实例 | jclass
java.lang.String | jstring 
Object[] | jobjectArray
short[] | jshortArray
char[] | jcharArrsy
int[] | jintArray
long[] | jlongArray
boolean[] | jbooleanArray
float[] | floatArray
byte[] | jbyteArray
double[] | jdoubleArray
java.lang.Throwable | jthrowable

除了class和String，Java中其他的类对对应的是jobject

## JNIEnv

JNIEnv是一个与线程相关的代表JNI环境的结构体。
JNIEnv是一个与线程相关的变量，不能再线程B中使用线程A的JNIEnv结构体。
在JNI_onLoad函数的定义为：jint JNI_OnLoad(JavaVM * vm, void *reserved)
调用JavaVM的AttachCurrentThread函数，就可以得到这个线程的JNIEnv结构体。
在后台线程退出后，需要调用的JavaVM的DetachCurrentThread函数来释放对应的资源。
在JNI中jfiedlID和jmethodID来表示Java类的成员变量和成员函数。

```获取函数定义
jfieldId GetFieldID(jclass clazz, const char* name, const char* sig);
jmethodId GetMethodID(jclass clazz, const char* name, const char* sig);

```

jclass:表示Java类
name：表示成员函数或者变量的名字。
sig：为这个函数和变量的签名信息。

NativeType Call<Type>Method(JNIEnv* env, jobject obj, jmethodID methodID..)

## jstring

JNIEnv->NewString(JNIEnv* env, const jchar* unichodeChars, jsize len)

## JNI 签名

（参数1类型表示参数2类型表示......）返回值类型表示
 L表示包名， V表示返回值类型
 [I : int[];[L/java/long/object; Object[]
 
 (Ljava/lang/String;Ljava/lang/String;) V
 
## 垃圾回收

JNI对象引用
Local Reperence : 本地引用
Global Reference : 全局引用
Weak Global Reference : 弱全局引用


## JNI中异常处理

ExceptionOccured : 用来判断是否发生异常
ExceptionClear : 用来清理当前的JNI层的发生的异常
ThrowNew : 用来向Java层抛出异常。








