---
title: Volley加载网络图片
date: 2016-09-28 14:15:03
categories: android
tags: volley
---

## Volley加载网络图片

#####ImageRequest用法
1. 创建一个RequestQueue对象。
2. 创建一个Request对象。
3. 将Request对象添加到RequestQueue里面。

例子

      String url ="http://android.tgbus.com/news/UploadFiles_8153/201208/2012081013531630.jpg";

        RequestQueue requestQueue = Volley.newRequestQueue(this);

        /**
         * ImageRequest的构造函数接收六个参数，
         * 第一个参数就是图片的URL地址，
         * 第二个参数是图片请求成功的回调，这里我们把返回的Bitmap参数设置到ImageView中。
         * 第三第四个参数分别用于指定允许图片最大的宽度和高度，如果指定的网络图片的宽度或高度大于这里的最大值，则会对图片进行压    缩，指定成0的话就表示不管图片有多大，都不会进行压缩。
         * 第五个参数用于指定图片的颜色属性，Bitmap.Config下的几个常量都可以在这里使用，其中ARGB_8888可以展示最好的颜色属性，每个图片像素占据4个字节的大小，而RGB_565则表示每个图片像素占据2个字节大小。
         * 第六个参数是图片请求失败的回调
         */
        ImageRequest imageRequest = new ImageRequest(url, new Response.Listener<Bitmap>() {
            @Override
            public void onResponse(Bitmap bitmap) {

                mIv1.setImageBitmap(bitmap);

            }
        }, 0, 0, ImageView.ScaleType.CENTER_CROP, Bitmap.Config.RGB_565, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                error.printStackTrace();
                Log.d("vivi", "onErrorResponse: "+error.getMessage());
            }
        });

        requestQueue.add(imageRequest);

##### 使用ImageLoader加载图片
1. 创建一个RequestQueue对象。
2. 创建一个ImageLoader对象。
3. 获取一个ImageListener对象。
4. 调用ImageLoader的get()方法加载网络上的图片。

        //创建请求队列
         RequestQueue requestQueue = Volley.newRequestQueue(this);
         //创建ImageLoader
        ImageLoader imageLoader = new ImageLoader(requestQueue, new ImageLoader.ImageCache() {
            @Override
            public Bitmap getBitmap(String url) {

                return null;
            }

            @Override
            public void putBitmap(String url, Bitmap bitmap) {

            }
        });

第一个参数就是RequestQueue对象，第二个参数是一个ImageCache对象，这里是个空对象，没有实现
ImageCache对象是一个空的实现，完全没能起到图片缓存的作用。其实写一个ImageCache，但是如果想要写一个性能非常好的ImageCache，最好就要借助Android提供的LruCache功能了见最下面
<a href="#overview">Overview</a>
需要获取一个ImageListener对象

          /**
         * 第一个要显示在哪个控件
         * 第二个 默认的资源图片
         * 第三个 错误的资源图片
         */
        ImageLoader.ImageListener imageListener = ImageLoader.getImageListener(mIv2,R.mipmap.ic_launcher,R.mipmap.ic_launcher);

        //调用ImageLoader的get()方法来加载图片
        imageLoader.get("http://www.people.com.cn/mediafile/pic/20151010/79/16111083488036070527.jpg",imageListener);

>设置宽高

       imageLoader.get("http://www.people.com.cn/mediafile/pic/20151010/79/16111083488036070527.jpg",imageListener,300,400);

##### Volley还有一个图片加载的控件

NetworkImageView使用方法
NetworkImageView是一个自定义控制，它是继承自ImageView的，具备ImageView控件的所有功能，并且在原生的基础之上加入了加载网络图片的功能。NetworkImageView控件的用法要比前两种方式更加简单.在布局中使用
    
    
    <com.android.volley.toolbox.NetworkImageView
        android:id="@+id/networkIv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"/>

>步骤

1. 创建一个RequestQueue对象。
2. 创建一个ImageLoader对象。
3. 在布局文件中添加一个NetworkImageView控件。
4. 在代码中获取该控件的实例。
5. 设置要加载的图片地址。

         //创建请求队列
        RequestQueue requestQueue = Volley.newRequestQueue(this);
        //设置默认资源
        mNetworkImageView.setDefaultImageResId(R.mipmap.ic_launcher);
        //设置加载失败的资源
        mNetworkImageView.setErrorImageResId(R.mipmap.ic_launcher);

        //创建ImageLoader
        ImageLoader imageLoader = new ImageLoader(requestQueue, new ImageLoader.ImageCache() {
            @Override
            public Bitmap getBitmap(String url) {

                return null;
            }

            @Override
            public void putBitmap(String url, Bitmap bitmap) {

            }
        });
        //设置资源  setImageUrl()方法接收两个参数，第一个参数用于指定图片的URL地址，第二个参数则是前面创建好的ImageLoader对象。
        mNetworkImageView.setImageUrl("http://img0.imgtn.bdimg.com/it/u=560246235,1189337337&fm=21&gp=0.jpg",imageLoader);

NetworkImageView中则完全没有提供设置最大宽度和高度的方法
由于NetworkImageView是一个控件，在加载图片的时候它会自动获取自身的宽高，然后对比网络图片的宽度，再决定是否需要对图片进行压缩。也就是说，压缩过程是在内部完全自动化的，并不需要我们关心，NetworkImageView会始终给我们一张大小刚刚好的网络图片

<a name="overview">Overview</a>
#### MyVolley 
    
            package com.liu.volleydemo.volley;
        
        import android.app.ActivityManager;
        import android.content.Context;
        import android.util.Log;
        
        import com.android.volley.RequestQueue;
        import com.android.volley.toolbox.ImageLoader;
        import com.android.volley.toolbox.Volley;
        
        /**
         * @Description: 单例
         * @AUTHOR 刘楠  Create By 2016/9/26 0026 15:39
         */
        public class MyVolley {
        
            private static RequestQueue mRequestQueue;
            private static ImageLoader  mImageLoader;
        
            private MyVolley() {
        
            }
            public static RequestQueue getRequestQueue(Context context) {
                if (mRequestQueue == null) {
                    init(context);
                }
                return mRequestQueue;
            }
        
            private static void init(Context context) {
        
                mRequestQueue = Volley.newRequestQueue(context);
        
                ActivityManager activityManager = (ActivityManager) context.getSystemService(Context.ACTIVITY_SERVICE);
                //获取内存大小
                int memoryClass = activityManager.getMemoryClass();
                Log.d("vivi", "init: memoryClass"+memoryClass);
                //使用1/8的内存空间来做为缓存
                int maxCacheSize = 1024*1024*memoryClass/8;
        
                Log.d("vivi", "init: maxCacheSize "+maxCacheSize);
        
                //初始化ImageLoad同时初始化自定义的ImageCache
                mImageLoader = new ImageLoader(mRequestQueue,new BitmapLruCache(maxCacheSize));
        
            }
        
            public static ImageLoader getImageLoader() {
                if (mImageLoader != null) {
                    return mImageLoader;
                } else {
                    throw new IllegalStateException("ImageLoader not initialized");
                }
            }
        }

##### ImageCache 

    package com.liu.volleydemo.volley;

    import android.graphics.Bitmap;
    import android.support.v4.util.LruCache;
    
    import com.android.volley.toolbox.ImageLoader;
    
    /**
     * @Description: 描述
     * @AUTHOR 刘楠  Create By 2016/9/26 0026 15:34
     */
    public class BitmapLruCache extends LruCache<String, Bitmap> implements ImageLoader.ImageCache {
    
    
        public BitmapLruCache(int maxSize) {
    
            super(maxSize);
        }
    
    
    
        @Override
        protected int sizeOf(String key, Bitmap value) {
            //返回宽度与高度字节乖的结果大小
            return value.getRowBytes() * value.getHeight();
        }
    
        @Override
        public Bitmap getBitmap(String url) {
            //根据KEY 获取BITMAP
            return get(url);
        }
    
        @Override
        public void putBitmap(String url, Bitmap bitmap) {
    
            //保存图片key-url,vaule-bitmap
            put(url,bitmap);
        }
    }



感谢郭霖大大
<http://blog.csdn.net/guolin_blog/article/details/17482095>
源码：
<https://github.com/ln0491/VolleyDemo>