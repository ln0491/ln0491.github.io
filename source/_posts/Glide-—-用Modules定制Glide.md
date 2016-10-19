---
title: Glide — 用Modules定制Glide
date: 2016-10-19 14:19:56
categories: android
tags: 图片加载
---
## Glide — 用Modules定制Glide

### Glide Modules

Glide modules是一个全局改变Glide行为的抽象的方式。你需要创建Glide的实例，来访问GlideBuilder。可以通过创建一个公共的类，实现GlideModule的接口来定制Glide
```

public class SimpleGlideModule implements GlideModule {
    @Override public void applyOptions(Context context, GlideBuilder builder) {
        // todo
    }

    @Override public void registerComponents(Context context, Glide glide) {
        // todo
    }
}
```
接口提供了两个方法去调节Glide的不同部分，大部分内容只研究第一个方法：applyOptions(Context context, GlideBuilder builder)

需要创建一个额外的类去自定义Glide。下一步是要在全局中声明这个类，这样Glide知道它应该加载并使用它。Glide会扫描AndroidManifest.xml的Glide modules的meta定义。这样，你必须在AndroidManifest.xml里的<application>标签下声明刚创建的Glide module。
```
<manifest

    ...

    <application>

        <meta-data
            android:name="io.futurestud.tutorials.glide.glidemodule.SimpleGlideModule"
            android:value="GlideModule" />

        ...

    </application>
</manifest>
```

确保你设置android:name为你自己的包名+类名，这样才能正确引用。你不需要添加其他的代码到其中。如果你想要禁止Glide Module，只要从AndroidManifest.xml里移除它。Java类里的代码可以留着供以后使用。当在AndroidManifest.xml里没有引用的时候，它永远不会被加载。

Glide推荐实现自定义module的方式有一个好处：你可以一次同时声明多个Glide Module。Glide会（没有特殊的顺序）都遍历所有声明的module。由于你当前未定义顺序，确保你的定制不会造成冲突！

### GlideBuilder

接口的第一个方法：applyOptions(Context context, GlideBuilder builder)。这个方法将GlideBuilder的对象当作参数，并且是void返回类型，所以你在这个方法里能调用GlideBuilder可以用的方法。



* .setMemoryCache(MemoryCache memoryCache)
* .setBitmapPool(BitmapPool bitmapPool)
* .setDiskCache(DiskCache.Factory diskCacheFactory)
* .setDiskCacheService(ExecutorService service)
* .setResizeService(ExecutorService service)
* .setDecodeFormat(DecodeFormat decodeFormat)



Android里有两个方法去解析图片：ARGB8888和RGB565。第一个为每个像素采用4 byte表示，后面一个则用2 byte表示。ARG8888有更高的图片质量，并且能够存储一个alpha通道。当Picasso使用ARGB888时，Glide默认使用低质量的RGB565。Glide用户的好消息：你可以通过使用Glide module方法改变解析格式。

你只要简单地实现一个GlideModule，像之前提到的一样，然后调用builder.setDecodeFormat(DecodeFormat.PREFER_ARGB_8888)，并传入正确的枚举型参数

```
public class SimpleGlideModule implements GlideModule {
    @Override public void applyOptions(Context context, GlideBuilder builder) {
        builder.setDecodeFormat(DecodeFormat.PREFER_ARGB_8888);
    }

    @Override public void registerComponents(Context context, Glide glide) {
        // nothing to do here
    }
}
```
