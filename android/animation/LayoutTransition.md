 LayoutTransition 容器布局动画
 
### LayoutTransition

使用示例:

```
LayoutTransition transition = new LayoutTransition();
viewGroup.setLayoutTransition(transition)

```

###  transitionType

* APPEARING:元素在容器中出现时所定义的动画。
* DISAPPEARING :元素在容器中消失时所定义的动画。
* CHANGE_APPEARING:由于容器中要显现一个新的元素，其它需要变化的元素所应用的动画
* CHANGE_DISAPPEARING:当容器中某个元素消失，其它需要变化的元素所应用的动画

### 参考

[animateLayoutChanges与LayoutTransition](http://wiki.jikexueyuan.com/project/android-animation/12.html)

