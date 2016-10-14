---
title: Glide入门教程四-调整图片大小
date: 2016-10-14 14:26:07
categories: android
tags: 图片加载
---
## Glide入门教程四-调整图片大小

### 调整图片大小 override()

Picasso也有同样的能力,resize(x, y)但需要调用fit()方法

用Glide时，如果图片不需要自动适配ImageView，调用override(horizontalSize, verticalSize)，它会在将图片显示在ImageView之前调整图片的大小。

```
   int width = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 200, mContext.getResources().getDisplayMetrics());
        int height   = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 200f, mContext.getResources().getDisplayMetrics());
        Glide.with(mContext) //上下文
                .load(url) //图片地址
                .placeholder(R.mipmap.pictures_no) //占位图
                .error(R.mipmap.ic_launcher)  //出错的占位图
                .override(width,height) //图片显示的分辨率 ，像素值 可以转化为DP再设置
                .animate(R.anim.my_alpha)

                 .into(holder.mIv); //显示在哪个控件中

```

### 缩放图片-centerCrop

Glide提供了变换去处理图片显示，通过设置centerCrop 和 fitCenter，可以得到两个不同的效果。


CenterCrop()会缩放图片让图片充满整个ImageView的边框，然后裁掉超出的部分。ImageVIew会被完全填充满，但是图片可能不能完全显示出。
```
  String url = mDatas[position];


        int width = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 200, mContext.getResources().getDisplayMetrics());
        int height   = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 200f, mContext.getResources().getDisplayMetrics());
        Glide.with(mContext) //上下文
                .load(url) //图片地址
                .placeholder(R.mipmap.pictures_no) //占位图
                .error(R.mipmap.ic_launcher)  //出错的占位图
                .override(width,height) //图片显示的分辨率 ，像素值 可以转化为DP再设置
                .animate(R.anim.my_alpha)
                .centerCrop()
                 .into(holder.mIv); //显示在哪个控件中

```

![animate.gif](https://ln0491.github.io/img/animate.gif)
### 缩放图片-fitCenter
为ImageView加个背景色
```xml

    <ImageView
        android:background="#ff0000"
               android:layout_margin="2dp"
               android:id="@+id/iv"
               android:layout_width="200dp"
               android:layout_height="200dp"/>
```

fitCenter()会缩放图片让两边都相等或小于ImageView的所需求的边框。图片会被完整显示，可能不能完全填充整个ImageView。

```
Glide.with(mContext) //上下文
                .load(url) //图片地址
                .placeholder(R.mipmap.pictures_no) //占位图
                .error(R.mipmap.ic_launcher)  //出错的占位图
                .override(width,height) //图片显示的分辨率 ，像素值 可以转化为DP再设置
                .animate(R.anim.my_alpha)
                .centerCrop()
                .fitCenter()
                 .into(holder.mIv); //显示在哪个控件中
```



![fitcenter.gif](https://ln0491.github.io/img/fitcenter.gif)

![fitcenter1.png](https://ln0491.github.io/img/fitcenter1.png)

centerCrop-fitCenter会相互覆盖，后面调用的后覆盖前面调用的效果


源码
<https://github.com/ln0491/GlideDemo>