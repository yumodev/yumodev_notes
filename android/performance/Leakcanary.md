# Leakcanary

## 介绍

Leakcanary可以手动触发GC然后分析强引用的GC引用链，如果有内存泄露，会在你的手机上弹出个提示框，
记录了每一次内存泄露的GC引用链，通过她可以直接定位到内存泄露的未释放对象。
Watch一个即将要销毁的对象。

## 使用

## 原理

1. Activity Destory之后将它放到WeakReferece中
2. 将这个WeakReference关联到一个ReferenceQueue中
3. 查看ReferenceQueue是否存在Activity的引用
4. 如果该Activity泄露了，Dump出Heap信息，然后在去分析泄露路径。

### ReferenceQueue 

对象被垃圾回收，Java虚拟机就会把这个引用加入到与之关联的引用队列中。








