# 介绍

基于Android9.0
## AndroidClassLoader

Android中的加载器主要是加载dex文件，主要分为系统类加载器和自定义加载器，其中系统类加载器分别为BootClassLoader、PathClassLoader和DexClassLoader。

## BootClassLoader

Android系统在启动时会使用BootClassLoader来预加载常用类，由Java语言实现。

BootClassLoader是一个单例类，同时也是ClassLoader的内部类，继承在ClassLoader。
```java
class BootClassLoader extends ClassLoader {

    private static BootClassLoader instance;

    @FindBugsSuppressWarnings("DP_CREATE_CLASSLOADER_INSIDE_DO_PRIVILEGED")
    public static synchronized BootClassLoader getInstance() {
        if (instance == null) {
            instance = new BootClassLoader();
        }

        return instance;
    }

    public BootClassLoader() {
        super(null);
    }

    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        return Class.classForName(name, false, null);
    }

    @Override
    protected URL findResource(String name) {
        return VMClassLoader.getResource(name);
    }

    @SuppressWarnings("unused")
    @Override
    protected Enumeration<URL> findResources(String resName) throws IOException {
        return Collections.enumeration(VMClassLoader.getResources(resName));
    }

    /**
     * Returns package information for the given package. Unfortunately, the
     * Android BootClassLoader doesn't really have this information, and as a
     * non-secure ClassLoader, it isn't even required to, according to the spec.
     * Yet, we want to provide it, in order to make all those hopeful callers of
     * {@code myClass.getPackage().getName()} happy. Thus we construct a Package
     * object the first time it is being requested and fill most of the fields
     * with dummy values. The Package object is then put into the ClassLoader's
     * Package cache, so we see the same one next time. We don't create Package
     * objects for null arguments or for the default package.
     * <p>
     * There a limited chance that we end up with multiple Package objects
     * representing the same package: It can happen when when a package is
     * scattered across different JAR files being loaded by different
     * ClassLoaders. Rather unlikely, and given that this whole thing is more or
     * less a workaround, probably not worth the effort.
     */
    @Override
    protected Package getPackage(String name) {
        if (name != null && !name.isEmpty()) {
            synchronized (this) {
                Package pack = super.getPackage(name);

                if (pack == null) {
                    pack = definePackage(name, "Unknown", "0.0", "Unknown", "Unknown", "0.0",
                            "Unknown", null);
                }

                return pack;
            }
        }

        return null;
    }

    @Override
    public URL getResource(String resName) {
        return findResource(resName);
    }

    @Override
    protected Class<?> loadClass(String className, boolean resolve)
           throws ClassNotFoundException {
        Class<?> clazz = findLoadedClass(className);

        if (clazz == null) {
            clazz = findClass(className);
        }

        return clazz;
    }

    @Override
    public Enumeration<URL> getResources(String resName) throws IOException {
        return findResources(resName);
    }
}
```

## PathClassLoader

加载系统类和应用程序的类。PathClassLoader中没有optimizedDirectory参数，因为PathClassLoader中已经默认其值为：`/data/dalvik-cache`

```java
public class PathClassLoader extends BaseDexClassLoader {
    public PathClassLoader(String dexPath, ClassLoader parent) {
        super((String)null, (File)null, (String)null, (ClassLoader)null);
        throw new RuntimeException("Stub!");
    }

    public PathClassLoader(String dexPath, String librarySearchPath, ClassLoader parent) {
        super((String)null, (File)null, (String)null, (ClassLoader)null);
        throw new RuntimeException("Stub!");
    }
}

```

## DexClassLoader

DexClassLoader可以加载dex问津以及包含dex的压缩文件Apk和jar文件。


```java
public class DexClassLoader extends BaseDexClassLoader {
    public DexClassLoader(String dexPath, String optimizedDirectory, String librarySearchPath, ClassLoader parent) {
        super((String)null, (File)null, (String)null, (ClassLoader)null);
        throw new RuntimeException("Stub!");
    }
}
```

### 参数

* dexPath：dex相关路径的集合。
* optimizedDirectory：加压的dex文件存储路径，这个路径必须是一个内部存储路径，一般情况下为使用当前应用程序的私有路径：/data/data/<packageName>/
* librarySearchPath:so库的路径，多个路径用文件分隔符分割。
* parent：父加载器。

## BaseDexClassLoader


```java
public class BaseDexClassLoader extends ClassLoader {
    public BaseDexClassLoader(String dexPath, File optimizedDirectory, String librarySearchPath, ClassLoader parent) {
        throw new RuntimeException("Stub!");
    }

    protected Class<?> findClass(String name) throws ClassNotFoundException {
        throw new RuntimeException("Stub!");
    }

    protected URL findResource(String name) {
        throw new RuntimeException("Stub!");
    }

    protected Enumeration<URL> findResources(String name) {
        throw new RuntimeException("Stub!");
    }

    public String findLibrary(String name) {
        throw new RuntimeException("Stub!");
    }

    protected synchronized Package getPackage(String name) {
        throw new RuntimeException("Stub!");
    }

    public String toString() {
        throw new RuntimeException("Stub!");
    }
}

```



