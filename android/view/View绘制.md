# Android中的绘制

## MeasureSpec 绘制规格

MeasureSpec是一个32位的数值，包含测量模式和尺寸
高2位是测量的模式，低30位为测量的大小。下面是三种模式

* EXACTLY

  该模式标识视图有具体的尺寸，如果对应的宽高属性为match_parent,那么其尺寸就是父视图的尺寸。父控件可以通过MeasureSpec.getSize(measureSpec)获取子视图的大小。
  
* AT_MOST

 该模式对应的视图属性为wrap_content。父控件无法确定子视图的大小，由子视图计算自己的尺寸。
  
* UNSPECIFIED

   视图自定义，想要多大就要多大。
   
   
 View类的onMeasure只支持EXACTLY模式。
 如果需要View支持wrap_content就必须重写onMeasure()方法

## Measure 

### 核心方法

* measure(int widthMeasureSpec, int heightMeasureSpec)

	该方法定义在View中，final类型，不能被子类重写。在measure中会调用View和ViewGroup的onMeasure()方法。

* onMeasure(int widthMeasureSpec, int heightMeasureSpec)

其方法参数widthMeasureSpec和heightMeasureSpec是父视图对其的测量要求，在子视图中根据这两个参数，计算子视图的尺寸。

* setMeasureDimension

  测量阶段的终极方法，在onMeasure()方法中调用，将计算得到的尺寸，传递给该方法，测量结束。

### ViewGroup

#### measureChildren

在ViewGroup中依次测量子视图的尺寸。

```java

protected void measureChildren(int widthMeasureSpec, int heightMeasureSpec) {
        final int size = mChildrenCount;
        final View[] children = mChildren;
        for (int i = 0; i < size; ++i) {
            final View child = children[i];
            if ((child.mViewFlags & VISIBILITY_MASK) != GONE) {
                measureChild(child, widthMeasureSpec, heightMeasureSpec);
            }
        }
    }
```

#### measureChild

该方法主要是通过getChildMeasureSpec()获取每个子视图的测量数据，最终调用子视图的measure进行绘制。

```java
  /**
     * Ask one of the children of this view to measure itself, taking into
     * account both the MeasureSpec requirements for this view and its padding.
     * The heavy lifting is done in getChildMeasureSpec.
     *
     * @param child The child to measure
     * @param parentWidthMeasureSpec The width requirements for this view
     * @param parentHeightMeasureSpec The height requirements for this view
     */
    protected void measureChild(View child, int parentWidthMeasureSpec,
            int parentHeightMeasureSpec) {
        final LayoutParams lp = child.getLayoutParams();

        final int childWidthMeasureSpec = getChildMeasureSpec(parentWidthMeasureSpec,
                mPaddingLeft + mPaddingRight, lp.width);
        final int childHeightMeasureSpec = getChildMeasureSpec(parentHeightMeasureSpec,
                mPaddingTop + mPaddingBottom, lp.height);

        child.measure(childWidthMeasureSpec, childHeightMeasureSpec);
    }
```

#### getChildMeasureSpec

该方法主要是子视图计算其MeasureSpec。

```java
  /**
 * Does the hard part of measureChildren: figuring out the MeasureSpec to
 * pass to a particular child. This method figures out the right MeasureSpec
 * for one dimension (height or width) of one child view.
 *
 * The goal is to combine information from our MeasureSpec with the
 * LayoutParams of the child to get the best possible results. For example,
 * if the this view knows its size (because its MeasureSpec has a mode of
 * EXACTLY), and the child has indicated in its LayoutParams that it wants
 * to be the same size as the parent, the parent should ask the child to
 * layout given an exact size.
 *
 * @param spec The requirements for this view
 * @param padding The padding of this view for the current dimension and
 *        margins, if applicable
 * @param childDimension How big the child wants to be in the current
 *        dimension
 * @return a MeasureSpec integer for the child
 */
public static int getChildMeasureSpec(int spec, int padding, int childDimension) {
    int specMode = MeasureSpec.getMode(spec);
    int specSize = MeasureSpec.getSize(spec);

    int size = Math.max(0, specSize - padding);

    int resultSize = 0;
    int resultMode = 0;

    switch (specMode) {
    // Parent has imposed an exact size on us
    case MeasureSpec.EXACTLY:
        if (childDimension >= 0) {
            resultSize = childDimension;
            resultMode = MeasureSpec.EXACTLY;
        } else if (childDimension == LayoutParams.MATCH_PARENT) {
            // Child wants to be our size. So be it.
            resultSize = size;
            resultMode = MeasureSpec.EXACTLY;
        } else if (childDimension == LayoutParams.WRAP_CONTENT) {
            // Child wants to determine its own size. It can't be
            // bigger than us.
            resultSize = size;
            resultMode = MeasureSpec.AT_MOST;
        }
        break;

    // Parent has imposed a maximum size on us
    case MeasureSpec.AT_MOST:
        if (childDimension >= 0) {
            // Child wants a specific size... so be it
            resultSize = childDimension;
            resultMode = MeasureSpec.EXACTLY;
        } else if (childDimension == LayoutParams.MATCH_PARENT) {
            // Child wants to be our size, but our size is not fixed.
            // Constrain child to not be bigger than us.
            resultSize = size;
            resultMode = MeasureSpec.AT_MOST;
        } else if (childDimension == LayoutParams.WRAP_CONTENT) {
            // Child wants to determine its own size. It can't be
            // bigger than us.
            resultSize = size;
            resultMode = MeasureSpec.AT_MOST;
        }
        break;

    // Parent asked to see how big we want to be
    case MeasureSpec.UNSPECIFIED:
        if (childDimension >= 0) {
            // Child wants a specific size... let him have it
            resultSize = childDimension;
            resultMode = MeasureSpec.EXACTLY;
        } else if (childDimension == LayoutParams.MATCH_PARENT) {
            // Child wants to be our size... find out how big it should
            // be
            resultSize = View.sUseZeroUnspecifiedMeasureSpec ? 0 : size;
            resultMode = MeasureSpec.UNSPECIFIED;
        } else if (childDimension == LayoutParams.WRAP_CONTENT) {
            // Child wants to determine its own size.... find out how
            // big it should be
            resultSize = View.sUseZeroUnspecifiedMeasureSpec ? 0 : size;
            resultMode = MeasureSpec.UNSPECIFIED;
        }
        break;
    }
    //noinspection ResourceType
    return MeasureSpec.makeMeasureSpec(resultSize, resultMode);
}
```
### View

#### measure


```java
 /**
 * <p>
 * This is called to find out how big a view should be. The parent
 * supplies constraint information in the width and height parameters.
 * </p>
 *
 * <p>
 * The actual measurement work of a view is performed in
 * {@link #onMeasure(int, int)}, called by this method. Therefore, only
 * {@link #onMeasure(int, int)} can and must be overridden by subclasses.
 * </p>
 *
 *
 * @param widthMeasureSpec Horizontal space requirements as imposed by the
 *        parent
 * @param heightMeasureSpec Vertical space requirements as imposed by the
 *        parent
 *
 * @see #onMeasure(int, int)
 */
public final void measure(int widthMeasureSpec, int heightMeasureSpec) {
    boolean optical = isLayoutModeOptical(this);
    if (optical != isLayoutModeOptical(mParent)) {
        Insets insets = getOpticalInsets();
        int oWidth  = insets.left + insets.right;
        int oHeight = insets.top  + insets.bottom;
        widthMeasureSpec  = MeasureSpec.adjust(widthMeasureSpec,  optical ? -oWidth  : oWidth);
        heightMeasureSpec = MeasureSpec.adjust(heightMeasureSpec, optical ? -oHeight : oHeight);
    }

    // Suppress sign extension for the low bytes
    long key = (long) widthMeasureSpec << 32 | (long) heightMeasureSpec & 0xffffffffL;
    if (mMeasureCache == null) mMeasureCache = new LongSparseLongArray(2);

    final boolean forceLayout = (mPrivateFlags & PFLAG_FORCE_LAYOUT) == PFLAG_FORCE_LAYOUT;

    // Optimize layout by avoiding an extra EXACTLY pass when the view is
    // already measured as the correct size. In API 23 and below, this
    // extra pass is required to make LinearLayout re-distribute weight.
    final boolean specChanged = widthMeasureSpec != mOldWidthMeasureSpec
            || heightMeasureSpec != mOldHeightMeasureSpec;
    final boolean isSpecExactly = MeasureSpec.getMode(widthMeasureSpec) == MeasureSpec.EXACTLY
            && MeasureSpec.getMode(heightMeasureSpec) == MeasureSpec.EXACTLY;
    final boolean matchesSpecSize = getMeasuredWidth() == MeasureSpec.getSize(widthMeasureSpec)
            && getMeasuredHeight() == MeasureSpec.getSize(heightMeasureSpec);
    final boolean needsLayout = specChanged
            && (sAlwaysRemeasureExactly || !isSpecExactly || !matchesSpecSize);

    if (forceLayout || needsLayout) {
        // first clears the measured dimension flag
        mPrivateFlags &= ~PFLAG_MEASURED_DIMENSION_SET;

        resolveRtlPropertiesIfNeeded();

        int cacheIndex = forceLayout ? -1 : mMeasureCache.indexOfKey(key);
        if (cacheIndex < 0 || sIgnoreMeasureCache) {
            // measure ourselves, this should set the measured dimension flag back
            onMeasure(widthMeasureSpec, heightMeasureSpec);
            mPrivateFlags3 &= ~PFLAG3_MEASURE_NEEDED_BEFORE_LAYOUT;
        } else {
            long value = mMeasureCache.valueAt(cacheIndex);
            // Casting a long to int drops the high 32 bits, no mask needed
            setMeasuredDimensionRaw((int) (value >> 32), (int) value);
            mPrivateFlags3 |= PFLAG3_MEASURE_NEEDED_BEFORE_LAYOUT;
        }

        // flag not set, setMeasuredDimension() was not invoked, we raise
        // an exception to warn the developer
        if ((mPrivateFlags & PFLAG_MEASURED_DIMENSION_SET) != PFLAG_MEASURED_DIMENSION_SET) {
            throw new IllegalStateException("View with id " + getId() + ": "
                    + getClass().getName() + "#onMeasure() did not set the"
                    + " measured dimension by calling"
                    + " setMeasuredDimension()");
        }

        mPrivateFlags |= PFLAG_LAYOUT_REQUIRED;
    }

    mOldWidthMeasureSpec = widthMeasureSpec;
    mOldHeightMeasureSpec = heightMeasureSpec;

    mMeasureCache.put(key, ((long) mMeasuredWidth) << 32 |
            (long) mMeasuredHeight & 0xffffffffL); // suppress sign extension
}
```
#### onMeasure


```java
/**
 * <p>
 * Measure the view and its content to determine the measured width and the
 * measured height. This method is invoked by {@link #measure(int, int)} and
 * should be overridden by subclasses to provide accurate and efficient
 * measurement of their contents.
 * </p>
 *
 * <p>
 * <strong>CONTRACT:</strong> When overriding this method, you
 * <em>must</em> call {@link #setMeasuredDimension(int, int)} to store the
 * measured width and height of this view. Failure to do so will trigger an
 * <code>IllegalStateException</code>, thrown by
 * {@link #measure(int, int)}. Calling the superclass'
 * {@link #onMeasure(int, int)} is a valid use.
 * </p>
 *
 * <p>
 * The base class implementation of measure defaults to the background size,
 * unless a larger size is allowed by the MeasureSpec. Subclasses should
 * override {@link #onMeasure(int, int)} to provide better measurements of
 * their content.
 * </p>
 *
 * <p>
 * If this method is overridden, it is the subclass's responsibility to make
 * sure the measured height and width are at least the view's minimum height
 * and width ({@link #getSuggestedMinimumHeight()} and
 * {@link #getSuggestedMinimumWidth()}).
 * </p>
 *
 * @param widthMeasureSpec horizontal space requirements as imposed by the parent.
 *                         The requirements are encoded with
 *                         {@link android.view.View.MeasureSpec}.
 * @param heightMeasureSpec vertical space requirements as imposed by the parent.
 *                         The requirements are encoded with
 *                         {@link android.view.View.MeasureSpec}.
 *
 * @see #getMeasuredWidth()
 * @see #getMeasuredHeight()
 * @see #setMeasuredDimension(int, int)
 * @see #getSuggestedMinimumHeight()
 * @see #getSuggestedMinimumWidth()
 * @see android.view.View.MeasureSpec#getMode(int)
 * @see android.view.View.MeasureSpec#getSize(int)
 */
 protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        setMeasuredDimension(getDefaultSize(getSuggestedMinimumWidth(), widthMeasureSpec),
                getDefaultSize(getSuggestedMinimumHeight(), heightMeasureSpec));
    }
```

#### setMeasureDimension



```java
  /**
 * <p>This method must be called by {@link #onMeasure(int, int)} to store the
 * measured width and measured height. Failing to do so will trigger an
 * exception at measurement time.</p>
 *
 * @param measuredWidth The measured width of this view.  May be a complex
 * bit mask as defined by {@link #MEASURED_SIZE_MASK} and
 * {@link #MEASURED_STATE_TOO_SMALL}.
 * @param measuredHeight The measured height of this view.  May be a complex
 * bit mask as defined by {@link #MEASURED_SIZE_MASK} and
 * {@link #MEASURED_STATE_TOO_SMALL}.
 */
protected final void setMeasuredDimension(int measuredWidth, int measuredHeight) {
    boolean optical = isLayoutModeOptical(this);
    if (optical != isLayoutModeOptical(mParent)) {
        Insets insets = getOpticalInsets();
        int opticalWidth  = insets.left + insets.right;
        int opticalHeight = insets.top  + insets.bottom;

        measuredWidth  += optical ? opticalWidth  : -opticalWidth;
        measuredHeight += optical ? opticalHeight : -opticalHeight;
    }
    setMeasuredDimensionRaw(measuredWidth, measuredHeight);
}
```

