---
title: 'Glide入门教程十-异常: 调试和报错处理'
date: 2016-10-19 14:08:12
categories: android
tags: 图片加载
---
## Glide入门教程十-异常: 调试和报错处理

* 本地调试

Glide的常规请求里提供了一个方法设置Log的层级。不幸地是，你没法轻易在产品使用中轻易获取。但，也有一个很简单的方法获得Glide的调试log。你只要通过adb shell，打开terminal，然后使用下面的命令行：

```
adb shell setprop log.tag.GenericRequest DEBUG
```
最后一部分DEBUG来自标准Android的log常量。因此，作为参数的递增优先级的选项如下：

    * VERBOSE
    * DEBUG
    * INFO
    * WARN
    * ERROR
当图片不存在时，会输出下面的日志：
```
io.futurestud.tutorials.glide D/GenericRequest: load failed
io.futurestud.tutorials.glide D/GenericRequest: java.io.IOException: Request failed 404: Not Found
...
```
* 基本的异常日志

Glide不提供直接获取常规请求的日志，但是你可以在请求出错时抓取异常的日志。例如，如果图片不存在，Glide会（静静地）抛出一个异常，并显示出你.erroer()里指定的图片。如果你明确想要知道异常，创建一个listener,然后传递给Glide的.listener()方法。


* 监听器
```
 String testUrl ="http://photo.enterdesk.com/2009-4-21/2009012416095313783333.png";

        RequestListener<String,GlideDrawable> listener = new RequestListener<String, GlideDrawable>() {
            @Override
            public boolean onException(Exception e, String model, Target<GlideDrawable> target, boolean isFirstResource) {


                e.printStackTrace();
                Log.d("vivi", "onException: "+e.getMessage());


                /**
                 * false 时error中的才会显示
                 * true 时error中的就不显示
                 */
                return false;
            }

            @Override
            public boolean onResourceReady(GlideDrawable resource, String model, Target<GlideDrawable> target, boolean isFromMemoryCache, boolean isFirstResource) {
                return false;
            }
        };

```
* 加载
你可以在Glide中的构造方法里设置listener:
```
  Glide.with(this)
                .load(testUrl)
                .listener(listener)
                .error(R.mipmap.pictures_no)
                .into(mIvEc1);

```

在onException方法中，你可以抓取问题，并决定你需要做什么，比如记录日志。如果Glide应当处理这个后果，比如显示一个出错占位图，在onException方法中返回false是很重要的。


.error()是否设置不影响日志正常工作。但只有在listener的onException方法里返回false，R.mipmap.pictures_no才会显示出来。
