---
title: Glide入门教程九-通知栏图片加载
date: 2016-10-19 11:25:31
categories: android
tags: 图片加载
---
## Glide入门教程九-通知栏图片加载
系统通知的图标为用户传递了重要的内容。用NotificationCompat.Builder为通知图片传递一个图片是最直接方式，但是这个图片必须是<b>Bitmap</b>格式的。如果这个图片已经在手机上，那没问题。但，如果这个图片还不在手机上，需要从网络下载，想要用这个标准的工具是不现实的。


* 自定义的通知布局


```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@android:color/white"
    android:orientation="vertical">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:padding="2dp">

        <ImageView
            android:id="@+id/remoteview_notification_icon"
            android:layout_width="50dp"
            android:layout_height="50dp"
            android:layout_marginRight="2dp"
            android:layout_weight="0"
            android:scaleType="centerCrop"/>

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:orientation="vertical">

            <TextView
                android:id="@+id/remoteview_notification_headline"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ellipsize="end"
                android:singleLine="true"
                android:textSize="12sp"/>

            <TextView
                android:id="@+id/remoteview_notification_short_message"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:ellipsize="end"
                android:paddingBottom="2dp"
                android:singleLine="true"
                android:textSize="14sp"
                android:textStyle="bold"/>

        </LinearLayout>
    </LinearLayout>

</LinearLayout>
```

* 初始化

```
 //创建通知布局
        RemoteViews remoteViews = new RemoteViews(getPackageName(),R.layout.remoteview_notification);


        remoteViews.setImageViewResource(R.id.remoteview_notification_icon,R.mipmap.ic_launcher);

        remoteViews.setTextViewText(R.id.remoteview_notification_headline,"头部分标题");
        remoteViews.setTextViewText(R.id.remoteview_notification_short_message,"二级标题内容");


        NotificationCompat.Builder builder =
                new NotificationCompat.Builder(getApplicationContext())
                                   .setSmallIcon(R.mipmap.ic_launcher)
                                    .setTicker("ticker")
                                    .setContentText("contenttext")
                                    .setContentTitle("contenttitle")
                                    .setContent(remoteViews)
                                    .setPriority(NotificationCompat.PRIORITY_HIGH);

        Notification notification = builder.build();

        if(Build.VERSION.SDK_INT>16){
            notification.bigContentView = remoteViews;
        }



        NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);

        notificationManager.notify(NOTIFICATION_ID,notification);
        //这个类是Glide自带的
        NotificationTarget notificationTarget =
                new NotificationTarget(getApplicationContext(),remoteViews,R.id.remoteview_notification_icon,notification,NOTIFICATION_ID);


        String notUrl = "http://photo.enterdesk.com/2009-4-21/200901241609531378.png";
        Glide.with(getApplicationContext())
                .load(notUrl)
                .asBitmap()
                .into(notificationTarget);

```
只要图片被加载了，我们定制的通知栏就会显示

