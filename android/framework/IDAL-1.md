
1、什么时AIDL
    Android Interface Ddfinition Language android接口定义语言。可以让某一个Service与多个应用程序组件之内部或者跨进程进程通信，从而实现让多个应用程序共享一个service的功能。比如音乐服务。
2、AIDL的流程
     1、首先在定义个AIDL文件 a.aidl, 和普通的可口文件一样，只是指出的数据类型比较少。ADT会自动生成一个java文锦啊。不论在服务器段还是客户端。AIDL文件必须一致，且放到同一个包下面，同一个名字。
     2、建立一个远程服务类，实现 a.aidl中的接口。并在onBind中返回这个AIDL类。
     3、在清单文件中配置该服务，android:process=":remote" android:exported = "true"
     2、隐式调用该远程service
     2、在serverConnection里面获取这个远程服务的实例：A.Stub.asInterface(Serviece);
      
3、AIDL的数据类型
    1、基本数据类型。2、String  CharSequence, list map Parcelables 3、自定义对象。
    如果要访问自定义对象，还需要建立对象的aidl文件，即使在一个包里面，也要用import倒入进来。
4、注意事项
     不能声明静态属性。
    接口和方法前不能加访问权限控制符。
     即使在一个包中，也要显式import。
    所有非基本类型参数，都必须加行in,out,inout 标记输入还是输出参数。
    java的原始类型默认时in，不能是其它标示