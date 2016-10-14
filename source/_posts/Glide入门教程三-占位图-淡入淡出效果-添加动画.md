---
title: Glide入门教程三 占位图-淡入淡出效果-添加动画.
date: 2016-10-14 13:51:42
categories: android
tags: 图片加载
---
* 占位图
网络的环境不好，加载过程可能需要花费大量的时间。这时候就需要一个占位图先显示出来，直到实际的图片加载并处理完毕。

```
Glide.with(mContext) //上下文
                .load(url) //图片地址
                .placeholder(R.mipmap.pictures_no) //占位图
                .into(holder.mIv); //显示在哪个控件中
```

* 出错的占位图 .error()
假设我们的app尝试从网页加载一张图片，但网页不可访问,Glide会给我们选项去进行出错的回调，并采取合适的行动

```
  Glide.with(mContext) //上下文
                .load(url) //图片地址
                .placeholder(R.mipmap.pictures_no) //占位图
                .error(R.mipmap.ic_launcher)  //出错的占位图
                 .into(holder.mIv); //显示在哪个控件中
```

加载前显示placeholder中的图片资源，加载失败就显示error中的图片资源
error()可以接受的只能是已经被初始化的图片资源或者指向图片资源的id

### 渐变动画 .crossFade()

crossFade()
```

        Glide.with(mContext) //上下文
                .load(url) //图片地址
                .placeholder(R.mipmap.pictures_no) //占位图
                .error(R.mipmap.ic_launcher)  //出错的占位图
                .crossFade(5000)                 //淡入淡出 可以不写默认300
                 .into(holder.mIv); //显示在哪个控件中
```

淡入淡出 -不写默认也会有值是300毫秒
crossFade()方法有另外一个特征：.crossFade(int duration),如果你想要减慢（或加快）动画，随便传入一个毫秒级的时间进去感受一下。
![crossfade.gif](https://ln0491.github.io/img/crossfade.gif)

#### dontAnimate()的使用
如果你只是直接显示图片，而不需要crossfade效果，那就在Glide的请求构造里调用.dontAnimate()



### animate 添加自定义的动画

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android">

    <alpha android:fromAlpha="0"
           android:duration="3000"
            android:toAlpha="1"/>

    <scale android:fromXScale="0"
           android:fromYScale="0"
           android:pivotX="50%"
           android:pivotY="50%"
           android:toXScale="100%"
           android:toYScale="100%"
           android:duration="3000"/>

    <rotate android:pivotY="50%"
            android:pivotX="50%"
            android:fromDegrees="30"
            android:toDegrees="360"
            android:duration="3000"
        />

</set>
```

* 添加
```

        Glide.with(mContext) //上下文
                .load(url) //图片地址
                .placeholder(R.mipmap.pictures_no) //占位图
                .error(R.mipmap.ic_launcher)  //出错的占位图

                .animate(R.anim.my_alpha)
                 .into(holder.mIv); //显示在哪个控件中

```

![animate.gif](https://ln0491.github.io/img/animate.gif)


这些参数都是独立的，并且设置不依赖彼此。例如，你可以只设置.error()，而不用调用.placeholder()。你可以设置crossFade()动画，而不用设置占位图。参数的任意结合都是可行的

源码
<https://github.com/ln0491/GlideDemo>