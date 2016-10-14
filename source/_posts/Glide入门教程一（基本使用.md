---
title: Glide入门教程一（基本使用)
date: 2016-10-14 10:43:20
categories: android
tags: 图片加载
---
## Glide入门教程一（基本使用）

### 添加依赖
```
compile 'com.github.bumptech.glide:glide:3.7.0'
```

### 简单介绍

* with(Context context)- Context是许多Android API需要调用的， Glide也不例外。这里Glide非常方便，你可以任意传递一个Activity或者Fragment对象，它都可以自动提取出上下文。
* load(String imageUrl) - 这里传入的是你要加载的图片的URL，大多数情况下这个String类型的变量会链接到一个网络图片。
* into(ImageView targetImageView) - 将你所希望解析的图片传递给所要显示的ImageView


### 加载url网络图片
* 权限
```
<uses-permission android:name="android.permission.INTERNET"/>
```
* 加载图片

```
 Glide.with(this).load("http://img1.imgtn.bdimg.com/it/u=2615772929,948758168&fm=21&gp=0.jpg").into(mIv1);
```
这几行！如果这个URL链接的图片的确存在，并且你的ImageView可见，你将会在1~2秒见到这张图片被加载。假如这张图片不存在，Glide会回调相应的出错接口下面说



### 加载本地图片

* File 文件
```
 //本地图片路径 /storage/emulated/0/1470634823290.jpg
        String path ="/storage/emulated/0/1470634823290.jpg";

        File file = new File(path);

        Glide.with(this).load(file).into(mIv3);
```
* 转换的URI
```
 //本地图片路径 /storage/emulated/0/1470634823290.jpg
        String path ="/storage/emulated/0/1470634823290.jpg";

        File file = new File(path);

       // Glide.with(this).load(file).into(mIv3);
        Uri uri = Uri.fromFile(file);


       Glide.with(this).load(uri).into(mIv3);
```
如果图片不显示建议设置要控件的宽与高，后面再说怎么解决



### 加载资源图片
这里是一个Int型的的资源id。
```
Glide.with(this).load(R.mipmap.test).into(mIv2);
```

源码
<https://github.com/ln0491/GlideDemo>