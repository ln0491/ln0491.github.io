---
title: Android onTouch 事件(一)
date: 2016-09-28 17:18:06
categories: android
tags:	android
---


# onTouch事件
##ViegGroup中有3个方法
	
```java

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
```java
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
###工具类
```java
public class TouchEventUtil {


    /**
     * 判断事件类型
     *
     * @param actionId
     * @return
     */
    public static String getTouchAction(int actionId) {

        String actionName = "actionId";
        switch(actionId) {
            case MotionEvent.ACTION_DOWN:
                actionName = "ACTION_DOWN";
                break;
            case MotionEvent.ACTION_MOVE:
                actionName = "ACTION_MOVE";
                break;
            case MotionEvent.ACTION_UP:
                actionName = "ACTION_UP";
                break;
            case MotionEvent.ACTION_CANCEL:
                actionName = "ACTION_CANCEL";
                break;
            case MotionEvent.ACTION_OUTSIDE:
                actionName = "ACTION_OUTSIDE";
                break;
        }
        return actionName;
    }
}
```

###布局

```xml
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
就只一个布局，就重写了，ViewGroup中的3个方法

###点击抬起的默认显示什么
在MyRelativeLayout中点击一下放开
```
MyRelativeLayout dispatchTouchEvent: ACTION_DOWN
MyRelativeLayout onInterceptTouchEvent: ACTION_DOWN
MyRelativeLayout onTouchEvent: ACTION_DOWN
 MainActivity  onTouchEvent: ACTION_DOWN
 MainActivity  onTouchEvent: ACTION_UP
```

