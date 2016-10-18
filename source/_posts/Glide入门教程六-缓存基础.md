---
title: Glide入门教程六-缓存基础
date: 2016-10-17 16:15:44
categories: android
tags: 图片加载
---
## Glide入门教程六-缓存基础


Glide默认使用内存和磁盘缓存来避免不必要的网络请求,所有的图片请求都会被缓存在内存和磁盘上.

* 内存缓存
```
 Glide.with(this)
                .load(url10)
                .placeholder(R.mipmap.pictures_no)
                .skipMemoryCache(true)   //跳过内存缓存，不在内存中保存图片
                .into(mIv10);
```

调用了.skipMemoryCache( true )去特意告诉Glide跳过内存缓存。这意味着Glide不会把这个图片缓存到内存里。重要是，这个只影响内存缓存！Glide为了避免以后的网络请求，仍然会缓存到磁盘。
由于Glide默认会将所有的图片资源缓存到内存中，因此，没有必要手动调用.skipMemoryCache( false )了


* 跳过磁盘缓存

即使你关闭了内存缓存，所请求的图片仍然会被保存在设备的磁盘存储上。如果你有一张不段变化的图片，但是都是用的同一个URL，你可能需要禁止磁盘缓存了。
可以用.diskCacheStrategy()方法改变Glide的行为。不同于.skipMemoryCache()方法，它将需要从枚举型变量中选择一个，而不是一个简单的boolean。如果你想要禁止请求的磁盘缓存，使用枚举型变量DiskCacheStrategy.NONE作为参数

```
 Glide.with(this)
                .load(url10)
                .placeholder(R.mipmap.pictures_no)

                .skipMemoryCache(true)   //跳过内存缓存，不在内存中保存图片
                .diskCacheStrategy(DiskCacheStrategy.NONE)  //跳这磁盘缓存
                .into(mIv10);
```
* DiskCacheStrategy

    * DiskCacheStrategy.NONE 不在磁盘下保存图片没有磁盘缓存
    * DiskCacheStrategy.SOURCE 只缓存全尺寸图，原图是多大就保存多大
    * DiskCacheStrategy.RESULT 只缓存最终降低分辨后用到的图片   例:  .override(300,300),300*300像素
    * DiskCacheStrategy.ALL 缓存所有类型的图片 (默认行为)

官方wiki<https://github.com/bumptech/glide/wiki/Caching-and-Cache-Invalidation>

源码
<https://github.com/ln0491/GlideDemo>