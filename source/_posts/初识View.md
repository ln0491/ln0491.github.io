---
title: 初识View
date: 2016-11-10 13:39:05
categories: android
tags: View
---

### 初识View及一些工具类


#### 什么是View?
View是Android中所有控件的基类，如Button，TextView 或者Relativelayout，ListView的共同的基类也就是父类
除了View还是ViewGroup，ViewGroup也继承了View。
```
public abstract class ViewGroup extends View
```
View可以是一个单独的控件，也可能是一个控件组包含多个子View


#### View的参数坐标

View的位置主要是由View的4个坐标来顶点来决定的
* letf: 	左上角x轴坐标
* top:		左上角y轴坐标
* right:	右下角x轴坐标
* bottom:	右下角y轴坐标


![view坐标.png](https://ln0491.github.io/img/view坐标.png)

宽度: width=right-left
高度：height=bottom-top

#### 获取参数坐标

* left = getLeft()
* top = getTop()
* right = getRight()
* bottom = getBottom()

设置也量样有setXxxx方法

#### 新的参数
3.0后
x,y,translationX,translationY

* x,y是View左上角的坐标
* translationX,translationY 是View左上角相对于父容器的偏移量，默认为0

一样有set/get方法

换算关系

x = left +translationX
y= top+translationY

View在平移过程中,top与left 表示的是原始左上角的位置信息，不会发生改变
此时发生改变的是x,y,translationX,translationY这4个参数


#### MotionEvent和TouchSlop



##### MotionEvent
在手指接触屏幕后的一系列事件如：

ACTION_DOWN:手指刚接触屏幕 按下

ACTION_MOVE:手指在屏幕上移动

ACTION_UP:手机离开屏幕的一瞬间


> 得到点击事件的x,y

* getX/getY

返回相对于当前View左上角的X和Y

* getRawX/getRawY

返回相对于手机屏幕左上角的X，y


##### TouchSlop

是系统所能识别出的被认为是滑动的最小距离，当手指在屏幕上滑动时，如果2次滑动之间的距离小于这个常量，系统就不认为是滑动操作

获取方式 ViewConfiguration-工具类
```
 int scaledTouchSlop = ViewConfiguration.get(getContext()).getScaledTouchSlop();
```
系统给的默认值为8dp
```
 /**
     * Distance a touch can wander before we think the user is scrolling in dips.
     * Note that this value defined here is only used as a fallback by legacy/misbehaving
     * applications that do not provide a Context for determining density/configuration-dependent
     * values.
     *
     * To alter this value, see the configuration resource config_viewConfigurationTouchSlop
     * in frameworks/base/core/res/res/values/config.xml or the appropriate device resource overlay.
     * It may be appropriate to tweak this on a device-specific basis in an overlay based on
     * the characteristics of the touch panel and firmware.
     */
    private static final int TOUCH_SLOP = 8;
```
config.xml
```

<dimen name="config_viewConfigurationTouchSlop">8dp</dimen>

```


#### VelocityTracker GestureDetector 和scroller


##### VelocityTracker
速度追踪：包括水平和竖直方法的速度
在onTouchEvent中添加
```
 @Override
    public boolean onTouchEvent(MotionEvent event) {
        VelocityTracker velocityTracker = VelocityTracker.obtain();
        velocityTracker.addMovement(event);
        return super.onTouchEvent(event);
    }
```
在其它需要的地方
```
		 velocityTracker.computeCurrentVelocity(1000);
        float xVelocity = velocityTracker.getXVelocity();
        float yVelocity = velocityTracker.getYVelocity();
```
需要注意的地方要先计算，才能获取到
不需要时

```
 		velocityTracker.clear();
        velocityTracker.recycle();
```

##### GestureDetector
手势检测，用于辅助检测用户的单击，滑动，长按，双击等行力要使用GestureDetector
```
   GestureDetector mGestureDetector = new GestureDetector(this);
//长按不能拖动现象
        mGestureDetector.setIsLongpressEnabled(false);
```

* onDown 1个ACTION_DONW
* onShowPress 由一个ACTON_DOWN触发，按下没有松开
* onSingleTapUP:一1上ACTION_DOWN和一个ACTION_UP
* onScroll :一个ACTION_DWON多个ACTION_MOVE组成
* onLongPress：用户长按不放开
* onFling: 快速滑动
* 


##### scroller
弹性滑动，View的scrollTo与scrollBy方法来滑动，没有过渡效果，使用Scroller来实现有过渡现象的滑动
Scroll要与View的computeScroll结合来完成

```
 @Override
    public void computeScroll() {
        super.computeScroll();
        if(mScroller.computeScrollOffset()){
            scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
            postInvalidate();

        }
    }

    private void smoothScrollTo(int destX,int destY){
        int scrollX = destX;
        int delta = destX-scrollX;
        mScroller.startScroll(scrollX,0,delta,0,1000);
    }
```