---
title: Android onTouch 事件
date: 2016-09-28 17:18:06
categories: android
tags:	android
---


# onTouch事件
##ViegGroup中有3个方法
```android
 /**
     * 分发事件
     * @param ev
     * @return
     */
    @Override
    public boolean dispatchTouchEvent(MotionEvent ev) {
        Log.d("vivi", "  MyRelativeLayout dispatchTouchEvent: "+ TouchEventUtil.getTouchAction(ev.getAction()));
        return super.dispatchTouchEvent(ev);
    }

    /**
     * 拦截事件
     * @param ev
     * @return
     */
    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        Log.d("vivi", "  MyRelativeLayout onInterceptTouchEvent: "+TouchEventUtil.getTouchAction(ev.getAction()));
        return super.onInterceptTouchEvent(ev);
    }

    /**
     * 事件处理
     * @param event
     * @return
     */
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        Log.d("vivi", "  MyRelativeLayout onTouchEvent: "+TouchEventUtil.getTouchAction(event.getAction()));
        return super.onTouchEvent(event);
    }
```

## View中有2个方法

```android
 @Override
    public boolean dispatchTouchEvent(MotionEvent event) {
        Log.d("vivi", "    MyView      dispatchTouchEvent: "+TouchEventUtil.getTouchAction(event.getAction()));
        return super.dispatchTouchEvent(event);
    }


    @Override
    public boolean onTouchEvent(MotionEvent event) {
        Log.d("vivi", "    MyView      onTouchEvent: "+ TouchEventUtil.getTouchAction(event.getAction()));
        return super.onTouchEvent(event);
    }
```


布局很简单就放了一个自定义的RelativeLayout，重写了，3个方法什么也没有
```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.liu.ontouchdemo.MainActivity">



    <com.liu.ontouchdemo.view.MyRelativeLayout
        android:layout_centerInParent="true"
        android:background="@android:color/holo_blue_dark"
        android:layout_width="400dp"
        android:layout_height="400dp"/>

</RelativeLayout>

```
![mr.png](https://ln0491.github.io/img/mr.png)
## dispatchTouchEvent
在蓝色部分点一下放开，默认情况
```
MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
MyRelativeLayout onInterceptTouchEvent: ACTION_DOWN
MyRelativeLayout onTouchEvent: ACTION_DOWN
 MainActivity  onTouchEvent: ACTION_DOWN
 MainActivity  onTouchEvent: ACTION_UP
```

> * 先走了ViewGroup中的dispatchTouchEvent
> * 再走ViewGroup中 onInterceptTouchEvent
> * 再走ViewGroup中 onTouchEvent
> * 回到Activity中 onTouchEvent


### dispatchTouchEvent返回false
```android
 MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
  MainActivity  onTouchEvent: ACTION_DOWN
  MainActivity  onTouchEvent: ACTION_UP
```

> * ViewGroup中只走了 dispatchTouchEvent方法其它2个方法并没有走说明dispatchTouchEvent返回false，不分发把事件传递上层(这里Activity)去处理
> * Activty中把事件消费处理掉了

--------------
*  dispatchTouchEvent返回false,onInterceptTouchEvent/onTouchEvent方法不会执行也就不会向下传递了，直接返回上层的容器处理

### dispatchTouchEvent返回true
```android
 MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
 MyRelativeLayout dispatchTouchEvent: ACTION_UP
```
---

> * 只走了dispatchTouchEvent方法,说明dispatchTouchEvent返回true时，自己把事件消费了，也没有走onInterceptTouchEvent/onTouchEvent,同时也没有返回上层



###dispatchTouchEvent小结

> * true时,dispatchTouchEvent自己消费事件，不走onInterceptTouchEvent/onTouchEvent,同时也没有返回上层
> * false时,dispatchTouchEvent不分发事件，直接返回上层容器，让上层容器自己处理，同时也不走onInterceptTouchEvent/onTouchEvent
> * super.dispatchTouchEvent(ev) 才会分发事件,去走onInterceptTouchEvent方法

将dispatchTouchEvent设置为super
------------
## onInterceptTouchEvent 

更改下布局，添加一个自定义View重写2个方法都返回默认
![my.png](https://ln0491.github.io/img/my.png)
### super时

```android
   MyView      dispatchTouchEvent: ACTION_DOWN
   MyView      onTouchEvent: ACTION_DOWN
 MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
 MyRelativeLayout onInterceptTouchEvent: ACTION_DOWN
 MyRelativeLayout onTouchEvent: ACTION_DOWN
  MainActivity  onTouchEvent: ACTION_DOWN
  MainActivity  onTouchEvent: ACTION_UP
```
> *都是默认没什么说的，者会走一下，返回到顶级容器去处理

### false
```android
   MyView      dispatchTouchEvent: ACTION_DOWN
   MyView      onTouchEvent: ACTION_DOWN
 MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
 MyRelativeLayout onInterceptTouchEvent: ACTION_DOWN
 MyRelativeLayout onTouchEvent: ACTION_DOWN
  MainActivity  onTouchEvent: ACTION_DOWN
  MainActivity  onTouchEvent: ACTION_UP
```
>* 可以看到和默认的super一样
>*   onInterceptTouchEvent，super和false都回调有onTouchEvent
### true
```android

  MyView      dispatchTouchEvent: ACTION_DOWN
  MyView      onTouchEvent: ACTION_DOWN
MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
MyRelativeLayout onInterceptTouchEvent: ACTION_DOWN
MyRelativeLayout onTouchEvent: ACTION_DOWN
 MainActivity  onTouchEvent: ACTION_DOWN
 MainActivity  onTouchEvent: ACTION_UP
```

> * 这个和上像一样
> * 这么看好像没什么区别，主要看true
-----
前提onInterceptTouchEvent返回true
## 结合onTouchEvent来看

### super 和上面一样了

### false也和上面一样了，

```
 MainActivity  onTouchEvent: ACTION_DOWN
MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
MyRelativeLayout onInterceptTouchEvent: ACTION_DOWN
MyRelativeLayout onTouchEvent: ACTION_DOWN
 MainActivity  onTouchEvent: ACTION_DOWN
 MainActivity  onTouchEvent: ACTION_UP
 MainActivity  onTouchEvent: ACTION_UP
```


> * onTouchEvent的 super和fasle效果是一样的
> * onInterceptTouchEvent返回true事件就会被截不再像子View去传递，而是走ViewGroup自己的onTouchEvent

主要看true

### true


```android
    MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
 MyRelativeLayout onInterceptTouchEvent: ACTION_DOWN
   MyView      dispatchTouchEvent: ACTION_DOWN
   MyView      onTouchEvent: ACTION_DOWN
 MyRelativeLayout onTouchEvent: ACTION_DOWN
  MainActivity  onTouchEvent: ACTION_UP
 MyRelativeLayout dispatchTouchEvent: ACTION_UP
 MyRelativeLayout onTouchEvent: ACTION_UP
```
> * 看到事件没有再向Activity去传递而是在ViewGroup的onTouchEvent中消费完了


---------
### onTouchEvent 小结

> * super和false结果一样都回再向上级去传递
> * true就在ViewGroup中消费完毕不再传递了


### ViweGroup小结

dispatchTouchEvent

> * false 事件不分发直接返回上级onTouchEvent去处理，同时也就不会调有onInterceptTouchEvent/onTouchEvent方法
> * true 直接消费这个事件，不会返回上级，同时也不会 调有onInterceptTouchEvent/onTouchEvent方法

> * super时，再会调用onInterceptTouchEvent方法

> * 这里onInterceptTouchEvent返回 false 和super是不拦截事件，
 如果这个ViewGroup没有View就调onTouchEvent方法

> * true:就在自己就消费事件不再向上传递
> * false和 super：就向上级容器返回事件，调用上级容器的onTouchEvent



 如果这个ViewGroup有子View就向子View(dispatchTouchEvent)传递事件,
子ViewdispatchTouchEvent:

> * true就自己消费了
> * false 就返回ViewGroup中调用onTouchEvent（false/super/true）方法
> * super 就调用子View的onTouchEvent方法

子View的onTouchEvent

> * super/false 都向上调ViewGroup中调用onTouchEvent（false/super/true）方法

> * 就在子View中消费这个事件不再传递




源码:
<https://github.com/ln0491/AndroidTouchDemo>
