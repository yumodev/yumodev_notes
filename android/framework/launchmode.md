# LaunchMode

## 概念

LaunchMode用于描述Activity的启动模式，目前一共有四种模式：standard,singleTop,singleTask, singleInstance。 启动模式用于控制Activity和Task关系的。

## standard

一个Task钟可以有多个相同类型的Activity。

## SingleTop

如果目标类型的Activity处于栈顶，就复用，然后调用其onNewIntent函数，否则要创建一个新的该类型的Activity的实例。

## singleTask

该Activity只存在一个实例，并且将和一个Task绑定，当需要此Activity时，系统会以onNewIntent方式启动它，而不会新建Task和Activity。

## singleInstanc

一个Task只能有一个设置了singleIntance的Activity。

## Flag

* FLAG_ACTIVITY_NEW_TASK

  将目标Activity放到一个新的Task中

* FLAG_ACTIVITY_CLEAR_TASK

  当启动一个Activity时，先把和目标Activity有关联的Task干掉，然后
  启动一个新的Task，并把目标Activity放到新的Task中。

