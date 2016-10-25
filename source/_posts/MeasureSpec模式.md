---
title: MeasureSpec模式
date: 2016-10-25 17:06:48
categories: android
tags:  自定义View
---


## MeasureSpec模式
### 子View的SpecMode为MeasureSpec.EXACTLY-(完全)，父元素决定自元素的确切大小，子元素将被限定在给定的边界里而忽略它本身大小

* 1.当子view为的LayoutParams的宽(高)采用具体的值(如100dp)宽高为固定大小,且父容器的MeasureSpec为MeasureSpec.EXACTLY或者MeasureSpec.AT_MOST或者MeasureSpec.UNSPECIFIED时：系统返回给该子View的specMode就为MeasureSpec.EXACTLY，系统返回给该子View的specSize就为子View自己指定的大小(childSize)。

> 这种情况，子view指定了宽高的具体值，不管父容器是什么返回的都是MeasureSpec.EXACTLY

* 2.当子View的LayoutParams的宽(高)采用match_parent时并且父容器的MeasureSpec为MeasureSpec.EXACTLY时：系统返回给该子View的specMode就为 MeasureSpec.EXACTLY，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)。


> 这种情况View设置宽高为match_parent,父容器为宽高为固定指定了大小的100dp，之类的，就是告诉子view要和父容器一样大



### 子View的SpecMode为MeasureSpec.AT_MOST-(至多)，子元素至多达到指定大小的值


* 1.当子View的LayoutParams的宽(高)采用match_parent并且父容器的MeasureSpec为MeasureSpec.AT_MOST时：系统返回给该子View的specMode就为MeasureSpec.AT_MOST，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)


> 当子viwe的宽高为match_parent,父容器的宽高为MeasureSpec为MeasureSpec.AT_MOST也就是wrap_content时，子view就是要和父容器一样大

* 2.当子View的LayoutParams的宽(高)采用wrap_content时并且父容器的MeasureSpec为MeasureSpec.EXACTLY时：系统返回给该子View的specMode就为 MeasureSpec.AT_MOST，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)

> 子view的宽高是wrap_content，而父容器的宽高为指定的固定大小，子view要看内容来确定大小，这里系统返回的就是MeasureSpec.AT_MOST



* 3.当子View的LayoutParams的宽(高)采用wrap_content时并且父容器的MeasureSpec为MeasureSpec.AT_MOST时:系统返回给该子View的specMode就为MeasureSpec.AT_MOST，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)

> 子View的LayoutParams的宽(高)采用wrap_content，即说明这个子View的宽高不明确,要视内容而定,这个时候父容器的MeasureSpec为MeasureSpec.AT_MOST。这种情况概况起来简单地说就是：子View的宽高是不确定的，父容器的宽高也是不确定的，那么系统返回给该子View的specMode也就是不确定的即为MeasureSpec.AT_MOST，系统返回给该子View的specSize就为该父容器剩余空间的大小(parentLeftSize)


### 的SpecMode为MeasureSpec.UNSPECIFIED-未指定

一般都是父控件是AdapterView，通过measure方法传入的模式


