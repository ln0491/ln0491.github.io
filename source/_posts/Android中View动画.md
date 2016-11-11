---
title: Android中View动画
date: 2016-11-11 14:28:51
categories: android
tags: View动画
---

###  Android中View动画

#### XML结构

```
<?xml version="1.0" encoding="utf-8"?>  
<set xmlns:android="http://schemas.android.com/apk/res/android"  
    android:interpolator="@[package:]anim/interpolator_resource"  
    android:shareInterpolator=["true" | "false"] >  
    <alpha  
        android:fromAlpha="float"  
        android:toAlpha="float" />  
    <scale  
        android:fromXScale="float"  
        android:toXScale="float"  
        android:fromYScale="float"  
        android:toYScale="float"  
        android:pivotX="float"  
        android:pivotY="float" />  
    <translate  
        android:fromX="float"  
        android:toX="float"  
        android:fromY="float"  
        android:toY="float" />  
    <rotate  
        android:fromDegrees="float"  
        android:toDegrees="float"  
        android:pivotX="float"  
        android:pivotY="float" />  
    <set>  
        ...  
    </set>  
</set> 
```
XML文件中必须有一个根元素，可以是<alpha>、<scale>、<translate>、<rotate>中的任意一个，也可以是<set>来管理一个由前面几个元素组成的动画集合。
<set>是一个动画容器，管理多个动画的群组，与之相对应的Java对象是AnimationSet

* fillBefore fillAfter

fillBefore是指动画结束时画面停留在此动画的第一帧;
fillAfter是指动画结束是画面停留在此动画的最后一帧。
```
/*****动画结束时，停留在最后一帧*********
setFillAfter(true);
setFillBefore(false);  
/*****动画结束时，停留在第一帧*********
setFillAfter(false);
setFillBefore(true);
```
XML设置

```
xml设置如下：
/******动画结束时，停留在最后一帧**********
<set Android:fillAfter="true" android:fillBefore="false">


/******动画结束时，停留在第一帧**********
<set android:fillAfter="false" android:fillBefore="true">


```
用这两个属性前要先 把 设 android:fillEnabled="true" 这样才有效果
PS：xml设置在子标签里面设置是无效的，注意是set标签

* shareInterpolator
android:shareInterpolator代表<set>里面的多个动画是否要共享插值器，默认值为true，即共享插值器，如果设置为false，那么<set>的插值器就不再起作用，我们要在每个动画中加入插值器。


* interpolator
Android:interpolator代表一个插值器资源，可以引用系统自带插值器资源，也可以用自定义插值器资源，默认值是匀速插值器
```
AccelerateDecelerateInterpolator	@android:anim/accelerate_decelerate_interpolator	先加速再减速
AccelerateInterpolator				@android:anim/accelerate_interpolator				加速
AnticipateInterpolator				@android:anim/anticipate_interpolator				先回退一小步然后加速前进
AnticipateOvershootInterpolator		@android:anim/anticipate_overshoot_interpolator		在上一个基础上超出终点一小步再回到终点
BounceInterpolator					@android:anim/bounce_interpolator					最后阶段弹球效果
CycleInterpolator					@android:anim/cycle_interpolator					周期运动
DecelerateInterpolator				@android:anim/decelerate_interpolator				减速
LinearInterpolator					@android:anim/linear_interpolator					匀速
OvershootInterpolator				@android:anim/overshoot_interpolator				快速到达终点并超出一小步最后回到终点

```

#### translate

* <translate>是位移动画，代表一个水平、垂直的位移。
* 与之对应的Java对象是TranslateAnimation。
* android:fromXDelta属性代表起始X方向的位置，
* android:toXDelta代表结尾X方向上的位置，
* android:fromYScale属性代表起始Y方向上的位置，
* android:toYDelta属性代表结尾Y方向上的位置
* 以上四个属性都支持三种表示方式：浮点数、num%、num%p；
* 如果以浮点数字表示，代表相对自身原始位置的像素值；
* 如果以num%表示，代表相对于自己的百分比
* 比如toXDelta定义为100%就表示在X方向上移动自己的1倍距离；如果以num%p表示，代表相对于父类组件的百分比。


XML

```
	<?xml version="1.0" encoding="utf-8"?>
	<set xmlns:android="http://schemas.android.com/apk/res/android"
     android:fillEnabled="true"
    android:shareInterpolator="true"
     android:fillBefore="true"
     android:fillAfter="false"
    android:interpolator="@android:anim/bounce_interpolator">


    <translate android:fromXDelta="-20%"
               android:fromYDelta="-30%"
               android:toXDelta="80%p"
               android:toYDelta="80%"
               android:duration="2000"

               android:repeatMode="restart"/>

    <!--
    fromXDelta 从哪里开始x轴
    fromYDelta 从哪里开始y轴
    repeatCount="infinite" 播放次数 infinite 无限
    repeatMode   reverse    播放模式  reverse 反方向进行，播放完了就，从播放完的地方倒着回到开始地址, restart 每次都从开始的地方开始播放
    -->

	</set>
```
java



```
 	private void toTranstion() {
 	      
        /**
         * 加载XML
         */
        Animation animation = AnimationUtils.loadAnimation(this,R.anim.translate);

        //开始播放
        mBtnTr.startAnimation(animation);



    }

```



代码动画


```
	Animation animation =new TranslateAnimation(0,100,0,0);
        animation.setDuration(3000);
        animation.setRepeatMode(Animation.REVERSE);
        animation.setRepeatCount(Animation.INFINITE);
 	mBtnTr.startAnimation(animation);
```


#### rotate

<rotate>是旋转动画，与之对应的Java对象是RotateAnimation。
android:fromDegrees属性代表起始角度，浮点值，单位：度；
android:toDegrees属性代表结尾角度，浮点值，单位：度；
android:pivotX属性代表旋转中心的X坐标值，
android:pivotY属性代表旋转中心的Y坐标值，
这两个属性也有三种表示方式，数字方式代表相对于自身左边缘的像素值，num%方式代表相对于自身左边缘或顶边缘的百分比，num%p方式代表相对于父容器的左边缘或顶边缘的百分比。

 例子
```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="true"
    android:fillAfter="true"
     android:fillBefore="false"
    android:interpolator="@android:anim/anticipate_interpolator">

    <rotate android:fromDegrees="0"
            android:repeatMode="reverse"
            android:repeatCount="2"    
            android:pivotX="50%"
            android:pivotY="50%"
            android:duration="2000"
            android:toDegrees="-360"/>
</set>
```
java
```
  Animation animation = AnimationUtils.loadAnimation(this,R.anim.rotate);
        mBtnRot.startAnimation(animation);

```
代码实现动画
```
Animation roateAnimation = new RotateAnimation(0,360,Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF,0.5f);
        roateAnimation.setDuration(5000);
        mBtnRot.startAnimation(roateAnimation);
```

#### scale

<scale>是缩放动画，可以实现动态调控件尺寸的效果，与之对应的Java对象是ScaleAnimation。
android:fromXScale属性代表起始的X方向上相对自身的缩放比例，浮点值，比如1.0代表自身无变化，0.5代表起始时缩小一倍，2.0代表放大一倍；
android:toXScale属性代表结尾的X方向上相对自身的缩放比例，浮点值；
android:fromYScale属性代表起始的Y方向上相对自身的缩放比例，浮点值；
android:toYScale属性代表结尾的Y方向上相对自身的缩放比例，浮点值；
android:pivotX属性代表缩放的中轴点X坐标，浮点值，
android:pivotY属性代表缩放的中轴点Y坐标，浮点值，对于这两个属性，如果我们想表示中轴点为图像的中心，我们可以把两个属性值定义成0.5或者50%。

例子：

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
   android:shareInterpolator="true"
    android:interpolator="@android:anim/accelerate_interpolator">

    <scale android:fromXScale="0"
           android:fromYScale="-1.0"
           android:pivotX="50%"
           android:pivotY="50%"
           android:duration="2000"
           android:repeatCount="2"
           android:repeatMode="reverse"
           android:toXScale="2.0"
           android:toYScale="3.0"/>
</set>
```
JAVA

```
	Animation scalAnimation = AnimationUtils.loadAnimation(this,R.anim.scale);
        mBtnScal.startAnimation(scalAnimation);

```


代码动画
```
	Animation scalAnimation = new RotateAnimation(0,1,Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF,0.5f);
        scalAnimation.setDuration(3000);
        scalAnimation.setFillEnabled(true);
        scalAnimation.setFillAfter(true);
        scalAnimation.setFillBefore(false);
        scalAnimation.setRepeatCount(2);
        mBtnScal.startAnimation(scalAnimation);
```



#### alpha

<alpha>是渐变动画，可以实现fadeIn和fadeOut的效果，与之对应的Java对象是AlphaAnimation。
android:fromAlpha属性代表起始alpha值，浮点值，范围在0.0和1.0之间，分别代表透明和完全不透明，
android:toAlpha属性代表结尾alpha值，浮点值，范围也在0.0和1.0之间。

例子
```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
     android:shareInterpolator="true"
     android:interpolator="@android:anim/accelerate_interpolator"
    >


    <alpha android:fromAlpha="0"
           android:toAlpha="1"
           android:repeatCount="infinite"
           android:repeatMode="reverse"
           android:duration="3000"/>

</set>
```
java

```
	 Animation alphaAnimation = AnimationUtils.loadAnimation(this,R.anim.alpha);
        mBtnAl.startAnimation(alphaAnimation);
```


代码实现
```
	Animation animation = new AlphaAnimation(0,1);
        animation.setDuration(3000);
        animation.setInterpolator(new AccelerateInterpolator());

        mBtnAl.startAnimation(animation);
```

#### 集合动画

set 对应AnimationSet

XML实现
```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="true"
    android:duration="2000"
     android:repeatMode="reverse"
     android:repeatCount="infinite"
     android:interpolator="@android:anim/bounce_interpolator"
    android:startOffset="100">

    <alpha android:fromAlpha="0"
           android:toAlpha="1"/>
    <rotate android:fromDegrees="0"
            android:pivotX="20%"
            android:pivotY="50%"
            android:toDegrees="260"
            />


    <scale android:pivotY="50%"
           android:pivotX="50%"
           android:fromXScale="0"
           android:fromYScale="0"
           android:toXScale="2.0"
           android:toYScale="3.0"
           />

    <translate android:fromXDelta="0"
               android:fromYDelta="0"
               android:toXDelta="80%p"
               android:toYDelta="60%"/>

</set>
```
java
```

        Animation animation =AnimationUtils.loadAnimation(this,R.anim.animset);

        mBtnSet.startAnimation(animation);
```

代码实现
 
```
 		AnimationSet set = new AnimationSet(true);
        set.setDuration(3000);

        Animation translateAnimation = new TranslateAnimation(Animation.RELATIVE_TO_SELF,0,Animation.RELATIVE_TO_SELF,2.0f,Animation.RELATIVE_TO_SELF,0f,Animation.RELATIVE_TO_SELF,-2.0f);

        Animation roateAnimation = new RotateAnimation(0,360,Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF,0.5f);

       Animation scaleAnimation = new ScaleAnimation(0,1f,0,2.0f,Animation.RELATIVE_TO_SELF,0.5f,Animation.RELATIVE_TO_SELF,0.5f);

        Animation alphaAnimation = new AlphaAnimation(0,1);


        set.addAnimation(translateAnimation);
      set.addAnimation(roateAnimation);
        set.addAnimation(scaleAnimation);
        set.addAnimation(alphaAnimation);

        set.setInterpolator(new BounceInterpolator());
        mBtnSet.startAnimation(set);

```
AnimationSet 我们最常用的是调用其 addAnimation 将一个个不一样的动画组织到一起来，然后调用view 的 startAnimation 方法触发这些动画执行。功能较弱不能做到把集合中的动画按一定顺序进行组织然后在执行的定制。
Animation 是针对视图外观的动画实现，动画被应用时外观改变但视图的触发点不会发生变化，还是在原来定义的位置。
源码
<https://github.com/ln0491/ViewAnimation>