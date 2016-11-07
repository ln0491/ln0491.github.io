---
title: Android 欢迎页快速启动
date: 2016-11-07 17:38:21
categories: android
tags: android
---


###  Android 欢迎页快速启动

默认情况下，Android App在点击App logo到App完全启动这之间会有一段时间空白期。那么如何做到在用户点击logo图标之后立即打开App的界面而不是一段白屏或黑屏呢？


#### 设置xml

在drawable下建立welcome.xml

```xml
<?xml version="1.0" encoding="utf-8"?>

<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <!--背景色-->
    <item  android:drawable="@color/white"/>
    <item>
        <!--图片-->
        <bitmap
            android:gravity="center"
            android:src="@mipmap/welcome_page"/>
    </item>
</layer-list>
```



#### 设置style

```xml
<!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
    </style>
  <style name="WelcomeThem" parent="AppTheme">
        <item name="android:windowBackground">@drawable/welcome</item>
    </style>
```

#### 清单文件中配置style


```xml
 <!-- 欢迎页 -->

        <activity
            android:name=".ui.WelcomeActivity"
            android:windowSoftInputMode="adjustNothing" android:theme="@style/WelcomeThem">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>

                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>

```

#### Activity中不需要设置setContentView()

```java
public class WelcomeActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Intent intent = new Intent(this, MainActivity.class);
        startActivity(intent);
        finish();
    }
}

```
不需要为你的SplashActivity设置一个视图，这个视图来自于主题，在主题中为你的SplashActivity设置UI就足够了。
