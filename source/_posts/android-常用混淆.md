---
title: android 常用混淆
date: 2016-11-16 15:40:01
categories: android
tags: android
---


# 通常不需混淆的Android类

	-keep public class * extends android.app.Fragment
	-keep public class * extends android.app.Activity
	-keep public class * extends android.app.Application
	-keep public class * extends android.app.Service
	-keep public class * extends android.content.BroadcastReceiver
	-keep public class * extends android.preference.Preference
	-keep public class * extends android.content.ContentProvider
	-keep public class * extends android.support.v4.**
	-keep public class * extends android.support.annotation.**
	-keep public class * extends android.support.v7.**

# butterknife

	-keep class butterknife.** { *; }
	-dontwarn butterknife.internal.**
	-keep class **$$ViewBinder { *; }
	-keepclasseswithmembernames class * {
	    @butterknife.* <fields>;
	}
	-keepclasseswithmembernames class * {
	    @butterknife.* <methods>;
	}

# RxJava RxAndroid
		
	-dontwarn sun.misc.**
	-keepclassmembers class rx.internal.util.unsafe.*ArrayQueue*Field* {
	   long producerIndex;
	   long consumerIndex;
	}
	-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueProducerNodeRef {
	    rx.internal.util.atomic.LinkedQueueNode producerNode;
	}
	-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueConsumerNodeRef {
	    rx.internal.util.atomic.LinkedQueueNode consumerNode;
	}

# retrofit2

	-dontwarn retrofit2.**
	-keep class retrofit2.** { *; }
	-keepattributes Signature
	-keepattributes Exceptions

# okhttp

	-dontwarn com.squareup.okhttp3.**
	-keep class com.squareup.okhttp3.** { *;}
	-dontwarn okio.**

# 日志

	-dontwarn okhttp3.logging.**



# glide

	-keep public class * implements com.bumptech.glide.module.GlideModule
	-keep public enum com.bumptech.glide.load.resource.bitmap.ImageHeaderParser$** {
	  **[] $VALUES;
	  public *;
	}

# eventbus

	-keepattributes *Annotation*
	-keepclassmembers class ** {
	    @org.greenrobot.eventbus.Subscribe <methods>;
	}
	-keep enum org.greenrobot.eventbus.ThreadMode { *; }

# 友盟统计

	-keepclassmembers class * {
	  public <init> (org.json.JSONObject);
	}
	-keep public class [您的应用包名].R$*{
	  public static final int *;
	}
	-keepclassmembers enum * {
	  public static **[] values(); public static ** valueOf(java.lang.String);
	}

# Gson

	-keepattributes Signature-keepattributes *Annotation*
	-keep class sun.misc.Unsafe { *; }
	-keep class com.google.gson.stream.** { *; }
	# Application classes that will be serialized/deserialized over Gson 下面替换成自己的实体类
	-keep class com.example.bean.** { *; }