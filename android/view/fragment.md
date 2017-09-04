#什么是Fragment

Fragment是Activity的一个组成部分，它有着自己的生命周期和接收处理用户事件，可以动态的添加、替换和移除某一个Fragment。

#Fragment的加载方式良种

   * 静态加载
      直接在xml布局文件中加载。
      
   * 动态加载：
      1、创建碎片实例：fragment1
      2、获取到Fragmentmanager。调用getFragmentManager方法获取。
      3、开启一个事务，通过调用beginTransaction方法开启
      4、向容器内添加碎片一般涌replace 方法实现。
         transaction.replace(R.id.frangment,fragment1);
      5、提交事务。commit。
      
#将fragment添加到返回栈中

  通过调用FragmentTransaction的addToBackStack(null)加入到返回栈中。
  
# 在Activity与Fragment的通信

* 通过方法 getFragmentManager.findViewById(id) 或者getFragmentManager.findViewByTag(tag);

* setArguments(Bundle) 向Fragment传递参数
# 在Fragment中调用其关联的Activity

* 在Fragment中通过调用getActivity()就可以获取其关联的活动的实例
   
#Fragment 之间的通信

   在一个碎片中获取它关联的活动实例，然后通过该活动实例，获取另外一个随便的实例，然后这两个碎片之间就可以通信了。
   
#Fragment的生命周期

   1. onAttach() 当碎片和活动建立关联的时候调用
   2. onCreate()
   3. onCreateView() 当碎片加载视图的时候调用
   4. onActivityView() 当活动创建完成以后，调用。
   5. onStart（）
   6. onResume()
   7. onPause()
   8. onStop()
   9. onDestroyView()
   10. onDestroy()
   11. onDetach() 当碎片和活动解除关联时调用。

   * 第一次创建时调用顺序为：
     onAttach->onCreate->onCreateView->onActivityView->onStart->onResume
   * 当前的Fragment被替换的时候调用为
     onPause->onStop->onDestoryView  ,如果没有调用addToBackStack，还有可能进入onDestroy->onDetach
   * 当Frament重新激活时调用：
      onActivityView->onStart->onResume
      
    # Replace和Add、Hide的区别
      
      * replace 
         replace() 是remove和add的合体。
         
         如果前一个Fragment不在回收站，那么其将被销毁。
         如果前一个Fragment已经放入回退栈，那么其实例不会销毁，但是视图层次仍旧会销毁。即调用了onDestoryView和onCreateView.
      
      
      

