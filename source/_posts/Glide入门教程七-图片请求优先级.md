---
title: Glide入门教程七-图片请求优先级
date: 2016-10-17 16:16:33
categories: android
tags: 图片加载
---

## Glide入门教程七-图片请求优先级
.priority()方法参数的Priority的枚举变量
一共有四个不同的枚举变量。下面以递增方式列出：

	* Priority.LOW
	* Priority.NORMAL
	* Priority.HIGH
	* Priority.IMMEDIATE

```
 Glide.with(this)
                .load(url10)
                .placeholder(R.mipmap.pictures_no)
                .override(300,300)
                .skipMemoryCache(true)   //跳过内存缓存，不在内存中保存图片
                .diskCacheStrategy(DiskCacheStrategy.ALL)
                .priority(Priority.HIGH)   //添加高优先级
                .into(mIv10);
```


源码
<https://github.com/ln0491/GlideDemo>