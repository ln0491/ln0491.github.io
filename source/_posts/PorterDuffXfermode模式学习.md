---
title: PorterDuffXfermode模式学习
date: 2016-10-25 17:11:07
categories: android
tags: PorterDuffXfermode
---

## PorterDuffXfermode学习

![http://img.blog.csdn.net/20160517180528876](http://img.blog.csdn.net/20160517180528876)

图片来自网络

### 模式

* PorterDuff.Mode.CLEAR 
	绘制不会提交到画布上
* PorterDuff.Mode.SRC 
	只显示绘制源图像
* PorterDuff.Mode.DST 
	只显示目标图像，即已在画布上的初始图像
* PorterDuff.Mode.SRC_OVER 
	正常绘制显示，即后绘制的叠加在原来绘制的图上
* PorterDuff.Mode.DST_OVER 
	上下两层都显示但是下层(DST)居上显示
* PorterDuff.Mode.SRC_IN 
	取两层绘制的交集且只显示上层(SRC)
* PorterDuff.Mode.DST_IN 
	取两层绘制的交集且只显示下层(DST)
* PorterDuff.Mode.SRC_OUT 
	取两层绘制的不相交的部分且只显示上层(SRC)
* PorterDuff.Mode.DST_OUT 
	取两层绘制的不相交的部分且只显示下层(DST)
* PorterDuff.Mode.SRC_ATOP 
	两层相交，取下层(DST)的非相交部分和上层(SRC)的相交部分
* PorterDuff.Mode.DST_ATOP 
	两层相交，取上层(SRC)的非相交部分和下层(DST)的相交部分
* PorterDuff.Mode.XOR 
	挖去两图层相交的部分
* PorterDuff.Mode.DARKEN 
	显示两图层全部区域且加深交集部分的颜色
* PorterDuff.Mode.LIGHTEN 
	显示两图层全部区域且点亮交集部分的颜色
* PorterDuff.Mode.MULTIPLY 
	显示两图层相交部分且加深该部分的颜色
* PorterDuff.Mode.SCREEN 
	显示两图层全部区域且将该部分颜色变为透明色

### PorterDuff.Mode.CLEAR 

### PorterDuff.Mode.SRC 


### PorterDuff.Mode.DST 


### PorterDuff.Mode.SRC_OVER 


### PorterDuff.Mode.DST_OVER 


### PorterDuff.Mode.SRC_IN 


### PorterDuff.Mode.DST_IN 


### PorterDuff.Mode.SRC_OUT 


### PorterDuff.Mode.DST_OUT 


### PorterDuff.Mode.SRC_ATOP 


### PorterDuff.Mode.DST_ATOP 


### PorterDuff.Mode.XOR 


### PorterDuff.Mode.DARKEN 


### PorterDuff.Mode.LIGHTEN 


### PorterDuff.Mode.MULTIPLY 


### PorterDuff.Mode.SCREEN 
