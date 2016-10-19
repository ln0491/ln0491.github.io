---
title: Glide入门教程十一变换
date: 2016-10-19 14:09:18
categories: android
tags: 图片加载
---

## Glide入门教程十一变换
在图片显示出之前可以对图片进行变换处理。例如，如果你的app需要显示一张灰度图，但只能获取到一个原始全色彩的版本，你可以使用一个变换去将图片从有明艳色彩的版本转换成惨淡的黑白版


推荐一个库Glide变换
<https://github.com/wasabeef/glide-transformations>
里面有很实现的效果

* 引入

```
//glide 变换
    compile 'jp.wasabeef:glide-transformations:2.0.1'
    // If you want to use the GPU Filters   -glide 变换使用GPU
    compile 'jp.co.cyberagent.android.gpuimage:gpuimage-library:1.3.0'
```

* 使用

```
  String trUrl = "http://pic.58pic.com/58pic/16/83/91/40558PICYDj_1024.jpg";


        Glide.with(this)
                .load(trUrl)
                .bitmapTransform(new CropCircleTransformation(this))
                .into(mIvEc2);



        Glide.with(this)
                .load(trUrl)
                .bitmapTransform(new BlurTransformation(this))
                .into(mIvEc3);


```


提示：当你使用变换的时候，你不能使用.centerCrop()或者.fitCenter()



源码
<https://github.com/ln0491/GlideDemo>