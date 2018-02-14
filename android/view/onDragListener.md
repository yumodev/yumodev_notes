# View.onDragListener-拖放

## OnDragListener的定义


```java
 /**
     * Interface definition for a callback to be invoked when a drag is being dispatched
     * to this view.  The callback will be invoked before the hosting view's own
     * onDrag(event) method.  If the listener wants to fall back to the hosting view's
     * onDrag(event) behavior, it should return 'false' from this callback.
     *
     * <div class="special reference">
     * <h3>Developer Guides</h3>
     * <p>For a guide to implementing drag and drop features, read the
     * <a href="{@docRoot}guide/topics/ui/drag-drop.html">Drag and Drop</a> developer guide.</p>
     * </div>
     */
    public interface OnDragListener {
        /**
         * Called when a drag event is dispatched to a view. This allows listeners
         * to get a chance to override base View behavior.
         *
         * @param v The View that received the drag event.
         * @param event The {@link android.view.DragEvent} object for the drag event.
         * @return {@code true} if the drag event was handled successfully, or {@code false}
         * if the drag event was not handled. Note that {@code false} will trigger the View
         * to call its {@link #onDragEvent(DragEvent) onDragEvent()} handler.
         */
        boolean onDrag(View v, DragEvent event);
    }

```

## 拖放的使用步骤 

1. view注册View.onDragLister()
2. 调用view的  `startDragAndDrop(ClipData data, DragShadowBuilder shadowBuilder,
            Object myLocalState, int flags)`
            
3. 在onDragLister接口的onDrag(View v, DragEvent event)中处理相关逻辑

## DragEvent

* ACTION_DRAG_STARTED
      
      开始拖拽View，返回true表示View的Drag和Drop动作应用。
* ACTION_DRAG_ENTERED
       
       表示drag的View进入到当前的View的区域
* ACTION_DRAG_LOCATION
		 
	表示drag的View进入到当前的View的区域，并改变位置。
* ACTION_DRAG_EXITED
		 
	标识Drag的View离开了当前的View的区域。
* ACTION_DROP
		 
	真正的Drop操作，标识
* ACTION_DRAG_ENDED

## DragShadowBuilder

