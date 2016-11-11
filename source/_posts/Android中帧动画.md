---
title: Android中帧动画
date: 2016-11-11 10:02:53
categories: android
tags: 动画
---

### Android中帧动画


#### Android中的逐帧动画

，逐帧动画是一种常见的动画形式（Frame By Frame），其原理是在“连续的关键帧”中分解动画动作，也就是在时间轴的每帧上逐帧绘制不同的内容，使其连续播放而成动画。 因为逐帧动画的帧序列内容不一样，不但给制作增加了负担而且最终输出的文件量也很大，但它的优势也很明显：逐帧动画具有非常大的灵活性，几乎可以表现任何想表现的内容，而它类似与电影的播放模式，很适合于表演细腻的动画。

在Android中逐帧动画需要得到AnimationDrawable类的支持，它位于"android.graphics.drawable.AnimationDrawable"包下，是Drawable的间接子类。它主要用来创建一个逐帧动画，并且可以对帧进行拉伸，把它设置为View的背景即可使用AnimationDrawable.start()方法播放。既然逐帧动画是需要播放一帧一帧的图像，所以需要为其添加帧。在Android中提供了两种方式为AnimationDrawable添加帧：XML定义的资源文件和Java代码创建，后面再详细讲讲这两种添加帧的方式。

光为AnimationDrawable设置帧还不能完成播放动画的功能，还需要AnimationDrawable定义好的其他的一些方法来操作逐帧动画，下面简单介绍一下AnimationDrawable的常用方法：

* void start()：开始播放逐帧动画。
* void stop()：停止播放逐帧动画。
* void addFrame(Drawable frame,int duration)：为AnimationDrawable添加一帧，并设置持续时间。
* int getDuration(int i)：得到指定index的帧的持续时间。
* Drawable getFrame(int index)：得到指定index的帧Drawable。
* int getNumberOfFrames()：得到当前AnimationDrawable的所有帧数量。
* boolean isOneShot()：当前AnimationDrawable是否执行一次，返回true执行一次，false循环播放。
* boolean isRunning()：当前AnimationDrawable是否正在播放。
* void setOneShot(boolean oneShot)：设置AnimationDrawable是否执行一次，true执行一次，false循环播放


#### 在代码中设置帧动画


```
 // 为AnimationDrawable添加动画帧
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_01),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_02),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_03),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_04),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_05),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_06),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_07),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_08),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_09),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_10),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_11),100);
        mCodeAd.addFrame(getResources().getDrawable(R.drawable.loading_img_12),100);
        //循环播放
        mCodeAd.setOneShot(false);
        //设置背景
        mIvCode.setBackgroundDrawable(mCodeAd);



   
```
播放
```
 private void startCode() {

        if(mCodeAd!=null && !mCodeAd.isRunning()){
            mCodeAd.start();
        }else if(mCodeAd!=null && mCodeAd.isRunning()) {
            mCodeAd.stop();
        }


    }
```

#### 在XML中设置帧动画

```
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">
<!--定义一个帧动画播放时间为100ms-->
    <item android:drawable="@drawable/loading_img_01" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_02" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_03" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_04" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_05" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_06" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_07" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_08" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_09" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_10" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_11" android:duration="100"/>
    <item android:drawable="@drawable/loading_img_12" android:duration="100"/>

</animation-list>
```
代码中使用

```
 private void initXMLFram() {

        mXmlAD = (AnimationDrawable) getResources().getDrawable(R.drawable.loading);
        //为Image设置背景
        mIvXML.setBackgroundDrawable(mXmlAD);

    }
```
播放

```
private void startXML() {
        if(mXmlAD!=null && !mXmlAD.isRunning()){
            mXmlAD.start();
        }else if(mXmlAD!=null && mXmlAD.isRunning()) {
            mXmlAD.stop();
        }
    }

```

源码
<https://github.com/ln0491/DrawableAnimation>
