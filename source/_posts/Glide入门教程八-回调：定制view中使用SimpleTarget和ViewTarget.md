---
title: Glide入门教程八-回调：定制view中使用SimpleTarget和ViewTarget
date: 2016-10-18 19:33:22
categories: android
tags: 图片加载
---
## Glide入门教程八-回调：定制view中使用SimpleTarget和ViewTarget


* Glide中的回调：Target

假设我们并没有ImageView作为图片加载的目标。我们只需要Bitmap本身。Glide提供了一个用Target获取Bitmap资源的方法。Target只是用来回调，它会在所有的加载和处理完毕时返回想要的结果。



```
   String url1 = "http://img4.imgtn.bdimg.com/it/u=21385237,1665284751&fm=21&gp=0.jpg";

        /**
         * 代码的第一部分，创建一个target字段对象，里面定义了个方法，
         * 这个方法一旦Glide加载和处理完图片将会被调用。
         * 回调方法传回Bitmap作为参数，你可以在你所需要用的地方随意使用这个Bitmap对象。
         */
        SimpleTarget<Bitmap> target = new SimpleTarget<Bitmap>() {
            @Override
            public void onResourceReady(Bitmap resource, GlideAnimation<? super Bitmap> glideAnimation) {

                //设置图片显示
                mIvt1.setImageBitmap(resource);
            }
        };

        Glide.with(this).load(url1)
                .asBitmap() //作为bitmap显示
                .into(target); //显示在这个对象
```



代码的第二部分，表明了Glide里如何使用Target，明显跟ImageView一样！
你可以传递一个Target或者ImageView作为参数到.into()方法里。
Glide会神奇地将结果返回。这里有个不同点，我们添加了.asBitmap()，
这会强制返回一个Bitmap对象。
记住，Glide也可以加载Gif或视频。为了防止在从网络URL（可能是GIF）获取Bitmap时，
出现未知格式图片冲突（期望是Bitmap），
我们设置.asBitmap()去告诉Glide只有在资源是一个图片是才算成功，其他的都算解析失败。

* 特定大小的Target

```
  /**
         *  通过构造方法设置图片大小
         */
        SimpleTarget<Bitmap> target2 = new SimpleTarget<Bitmap>(300,300) {
            @Override
            public void onResourceReady(Bitmap resource, GlideAnimation<? super Bitmap> glideAnimation) {
                //设置图片显示
                mIvt2.setImageBitmap(resource);
            }
        };

        Glide.with(this).load(url2)
                .asBitmap() //作为bitmap显示
                .into(target2); //显示在这个对象

```
和“普通”target唯一不同的是这个以像素为单位的图片大小声明
```
 new SimpleTarget<Bitmap>(300,300)
```

* ViewTarget

有很多原因导致我们不能直接使用ImageView,由于没有已知的方法在哪里设置图片，Glide并不支持加载图片到定制的View内。然而用ViewTarget会让这个更简单。
假设你有个自定义的View
```
public class FutureStudioView extends FrameLayout {
    ImageView iv;
    TextView tv;

    public void initialize(Context context) {
        inflate( context, R.layout.custom_view_futurestudio, this );

        iv = (ImageView) findViewById( R.id.custom_view_image );
        tv = (TextView) findViewById( R.id.custom_view_text );
    }

    public FutureStudioView(Context context, AttributeSet attrs) {
        super( context, attrs );
        initialize( context );
    }

    public FutureStudioView(Context context, AttributeSet attrs, int defStyleAttr) {
        super( context, attrs, defStyleAttr );
        initialize( context );
    }

    public void setImage(Drawable drawable) {
        iv = (ImageView) findViewById( R.id.custom_view_image );

        iv.setImageDrawable( drawable );
    }
}
```
由于我们定制的view并不是继承自ImageView，这里不能使用常规的.into()方法。因此，我们只能创建一个ViewTarget，用来传递给.into()方法：


```
private void loadImageViewTarget() {
 String targetUrl = "http://photo.enterdesk.com/2009-4-21/200901241609531378.png";
    FutureStudioView customView = (FutureStudioView) findViewById( R.id.custom_view );

    viewTarget = new ViewTarget<FutureStudioView, GlideDrawable>( customView ) {
        @Override
        public void onResourceReady(GlideDrawable resource, GlideAnimation<? super GlideDrawable> glideAnimation) {
            this.view.setImage( resource.getCurrent() );
        }
    };

    Glide.with(this)
                   .load(targetUrl)
                   .centerCrop()
                   .override(300,300)
                   //显示自定义控件中
                   .into(viewTarget);

}
```


在target的回调方法中，我们在定制view上使用我们创建的setImage(Drawable drawable)方法设置图片。同时，确保你注意到我们已经在ViewTarget的构造方法里传递了我们的定制view:new ViewTarget<FutureStudioView, GlideDrawable>( customView )。

源码
<https://github.com/ln0491/GlideDemo>