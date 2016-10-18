---
title: Glide入门教程五-加载gif-视频快照-缩略图
date: 2016-10-14 17:11:40
categories: android
tags: 图片加载
---

## Glide入门教程五-加载gif-视频快照-缩略图

### 播放 网络Gif动画

```
 //加载网络gif
        String gifUrl = "http://b.hiphotos.baidu.com/zhidao/pic/item/faedab64034f78f066abccc57b310a55b3191c67.jpg";
        Glide.with(this).load(gifUrl).placeholder(R.mipmap.pictures_no).into(mIv4);
```

### 本地资源gif

```
 //加载资源gif
        Glide.with(this).load(R.mipmap.loading).asGif().placeholder(R.mipmap.pictures_no).into(mIv5);

```

### 加载本地gif文件


```

        //加载本地gif文件
        String gifPath = Environment.getExternalStorageDirectory().getAbsolutePath()+"/Movies/test.gif";
        //test.gif  animate.gif Starry_Night.mp4 test.mp4

        File gifFile = new File(gifPath);

        Glide.with(this).load(gifFile).placeholder(R.mipmap.ic_launcher).into(mIv6);

```

### 加载本地视频快照

```
 //加载本地小视频 快照，只是快照 并不能播放
        String videoPath =Environment.getExternalStorageDirectory().getAbsolutePath()+"/Movies/test.mp4";;

        File videoFile = new File(videoPath);

        Glide.with(this).load(Uri.fromFile(videoFile)).placeholder(R.mipmap.ic_launcher).into(mIv7);
```

### 加载缩略图一


如果你传递一个0.1f作为参数，Glide会加载原始图片大小的10%的图片。如果原始图片有1000x1000像素，缩略图的分辨率为100x100像素

```
String urlPath="/storage/emulated/0/MagazineUnlock/magazine-unlock-03-2.3.311-bigpicture_03_20.jpg";
        //第一种 设置缩略图比例
        Glide.with(this).load(new File(urlPath)).thumbnail(0.1f).centerCrop().placeholder(R.mipmap.pictures_no).into(mIv8);
```

### 加载缩略图二 高级缩略图请求：原图与缩略图完全不同

```
 //先建立一个缩略图对象 先加载缩略图，再加载原图
        DrawableRequestBuilder thumbnailRequest=  Glide.with(this).load("http://img1.imgtn.bdimg.com/it/u=2615772929,948758168&fm=21&gp=0.jpg");

        Glide.with(this).load(Uri.fromFile(videoFile)).thumbnail(thumbnailRequest).centerCrop().placeholder(R.mipmap.pictures_no).into(mIv9);
```


源码
<https://github.com/ln0491/GlideDemo>


