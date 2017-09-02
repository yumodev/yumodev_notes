### BIndle

### 背景知识

#### 进程隔离
#### 内核空间和用户空间
#### 内核模块驱动

### 为什么使用BInder。

Andorid使用的Linux内核拥有非常多的跨进程通信机制，比如管道、SystemV，SOCKET等。
为了性能和安全，Android系统单独开发了Binder机制。

### binder模型

对于跨进程的双方 ：Server进程和Client进程。
包含：Binder驱动和ServiceManager。

#### binder是什么

Binder是一种通信机制，就像AIDL使用Binder进行通信，指的就是Binder这种IPC机制。
对于Server进程来说Binder是本地对象。
Client来说Binder是Binder代理对象，它只是Binder本地对象的一个远程代理。
对于传输过程而言，binder是可以进行跨进程传递的对象；binder驱动会对具有跨进程传递能力的对象做特殊处理；自动完成代理对象和本地对象的转换。

面向对象思想的引入将进程间通信转化为通过对某个binder对象的引用调用该对象的方法，而其独特之处在于Binder对象是一个可以跨进程引用的对象，它的实体位于一个进程中，而它的引用却遍布于系统的各个进程之中。这个引用和Java里引用一样几课时强类型，也可以是弱类型，而且可以从一个进程传给其它进程，让大家访问同一个Server，就像一个对象或引用复制给另一个引用一样。binder模糊了进程便捷，淡化了进程间通信过程，整个系统仿佛运行于同一个面向对象的程序之中。形形色色的Binder对象以及星罗棋布的引用仿佛粘接各个应用程序的胶水，这也是Binder在英文里面的愿意。
#### 通信步骤

1、SM建立：首先一个进程向驱动提出申请SM，驱动同意以后，SM进程负责管理Service。
2、各个Server想SM注册：每个Server端进程启动后，向SM报告。
3、Clent通过SM获取一个server，然后开始通信。

#### 驱动

在驱动中，Binder本地对象的代表是一个叫做BInder_node的数据结构；Binder代理对象是用Binder_ref代表的；
有的地方把Binder本地对象直接称作Binder实体；把Binder代理对象，成为Binder引用。



