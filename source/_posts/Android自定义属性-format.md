---
title: Android自定义属性-format
date: 2016-10-12 16:57:39
categories: android
tags: 自定义View
---

## Android自定义属性-format

####  string 字符串

> * 定义

```xml
    <declare-styleable name="MyTextView">
<!--字符串-->
        <attr name="my_textname" format="string"/>


    </declare-styleable>
```

> * 获取

```
TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.MyTextView);


        //获取文字
        String textName = typedArray.getString(R.styleable.MyTextView_my_textname);

        this.setText(textName);

```
> * 使用

```
 <com.liu.myviewformatdemo.view.MyTextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:my_textname="无可奈何花落去枯萎地"
            />
```
#### dimension 尺寸值

> * 定义

```xml
    <declare-styleable name="MyTextView">
<!--字符串-->
        <attr name="my_textname" format="string"/>
        <!--        尺寸值        -->
        <attr name="my_textsize" format="dimension"/>


    </declare-styleable>
```

> * 获取

```
TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.MyTextView);


        //获取文字
        String textName = typedArray.getString(R.styleable.MyTextView_my_textname);

        this.setText(textName);

        //获取文字大小
        float textsize = typedArray.getDimensionPixelSize(R.styleable.MyTextView_my_textsize, 14);

        this.setTextSize(textsize);





```
> * 使用

```
 <com.liu.myviewformatdemo.view.MyTextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:my_textname="无可奈何花落去枯萎地"
            app:my_textsize="10sp"
            />
```



#### color：颜色值

> * 定义

```xml
    <declare-styleable name="MyTextView">
<!--字符串-->
        <attr name="my_textname" format="string"/>
        <!--        尺寸值        -->
        <attr name="my_textsize" format="dimension"/>
        <!--颜色-->
        <attr name="my_textcolor" format="color"/>


    </declare-styleable>
```

> * 获取

```
TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.MyTextView);


        //获取文字
        String textName = typedArray.getString(R.styleable.MyTextView_my_textname);

        this.setText(textName);

        //获取文字大小
        float textsize = typedArray.getDimensionPixelSize(R.styleable.MyTextView_my_textsize, 14);

        this.setTextSize(textsize);


        //获取文字颜色
        int color = typedArray.getColor(R.styleable.MyTextView_my_textcolor, 0xff00ff00);
        this.setTextColor(color);

```
> * 使用

```
 <com.liu.myviewformatdemo.view.MyTextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:my_textname="无可奈何花落去枯萎地"
            app:my_textsize="10sp"
            app:my_textcolor="#f00"
            />
```
####  reference：参考某一资源ID。

> * 定义

```xml
    <declare-styleable name="MyTextView">
    <!--字符串-->
        <attr name="my_textname" format="string"/>
        <!--        尺寸值        -->
        <attr name="my_textsize" format="dimension"/>
        <!--颜色-->
        <attr name="my_textcolor" format="color"/>
        <!--引用某个资源的ID-->
        <attr name="my_background" format="reference"/>

    </declare-styleable>
```

> * 获取

```
TypedArray typedArray = context.obtainStyledAttributes(attrs, R.styleable.MyTextView);


        //获取文字
        String textName = typedArray.getString(R.styleable.MyTextView_my_textname);

        this.setText(textName);

        //获取文字大小
        float textsize = typedArray.getDimensionPixelSize(R.styleable.MyTextView_my_textsize, 14);

        this.setTextSize(textsize);


        //获取文字颜色
        int color = typedArray.getColor(R.styleable.MyTextView_my_textcolor, 0xff00ff00);
        this.setTextColor(color);


        //获取背景
        int resourceId = typedArray.getResourceId(R.styleable.MyTextView_my_background, R.mipmap.ic_launcher);

        this.setBackgroundResource(resourceId);



```
> * 使用

```
 <com.liu.myviewformatdemo.view.MyTextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:my_textname="无可奈何花落去枯萎地"
            app:my_textsize="10sp"
            app:my_textcolor="#f00"
            app:my_background="@drawable/bg_shap"
            />
```
![这里写图片描述](http://img.blog.csdn.net/20161012171531531)

#### boolean：布尔值.

> * 定义

```
    <declare-styleable name="MyImageView">

        <!--Boolean 是否显示-->
        <attr name="my_display" format="boolean"/>

    </declare-styleable>
```
> * 获取
```
//获取是还是显示boolean值boolean isDisplay = typedArray.getBoolean(R.styleable.MyImageView_my_display, true);
```
> * 使用
```
<com.liu.myviewformatdemo.view.MyImageView    android:layout_marginTop="5dp"    android:layout_width="match_parent"    android:layout_height="50dp"    android:scaleType="centerCrop"    android:src="@mipmap/img_home_banner1"    app:my_display="true"    />
```


#### integer：整型值
> * 定义

```
<declare-styleable name = "AnimatedRotateDrawable">

    <attr name = "visible" />
    <attr name = "frameDuration" format="integer" />
    <attr name = "framesCount" format="integer" />
    <attr name = "pivotX" />
    <attr name = "pivotY" />
    <attr name = "drawable" />

</declare-styleable>
```

> * 获取

```
  typedArray.getInteger(R.styleable.名称,默认值)
```

> * 使用
```
<animated-rotate
    xmlns:android ="http://schemas.android.com/apk/res/android"
    android:drawable = "@drawable/图片ID"
    android:pivotX = "50%"
    android:pivotY = "50%"
    android:framesCount = "12"
    android:frameDuration = "100"/>
```

#### float：浮点值

> * 定义

```
<declare-styleable name = "AlphaAnimation">

    <attr name = "fromAlpha" format = "float" />
    <attr name = "toAlpha" format = "float" />

</declare-styleable>
```
> * 获取

 ```
 typedArray.getFloat(R.styleable.名称,默认值)
 ```

> * 使用

```
<alpha
    android:fromAlpha = "1.0"
    android:toAlpha = "0.7"/>
```


#### fraction：百分数

> * 定义

```
<declare-styleable name="RotateDrawable">
  <attr name = "visible" />
  <attr name = "fromDegrees" format = "float" />
  <attr name = "toDegrees" format = "float" />
  <attr name = "pivotX" format = "fraction" />
  <attr name = "pivotY" format = "fraction" />
  <attr name = "drawable" />
</declare-styleable>
```

> * 使用

```
<rotate
    xmlns:android ="http://schemas.android.com/apk/res/android"
    android:interpolator = "@anim/动画ID"
    android:fromDegrees = "0"
    android:toDegrees = "360"
    android:pivotX = "200%"
    android:pivotY = "300%"
    android:duration = "5000"
    android:repeatMode = "restart"
    android:repeatCount = "infinite"
/>
```

#### enum：枚举值

> * 定义

```
<declare-styleable name="名称">
    <attr name="orientation">
    <enum name="horizontal" value="0" />
    <enum name="vertical" value="1" />
    </attr>
</declare-styleable>
```

> * 使用
```
<LinearLayout
    xmlns:android = "http://schemas.android.com/apk/res/android"
    android:orientation = "vertical"
    android:layout_width = "fill_parent"
    android:layout_height = "fill_parent" >
</LinearLayout>
```


#### flag：位或运算

> * 定义

```
<declare-styleable name="名称">
<attr name="windowSoftInputMode">
<flag name = "stateUnspecified" value = "0" />
<flag name = "stateUnchanged" value = "1" />
<flag name = "stateHidden" value = "2" />
<flag name = "stateAlwaysHidden" value = "3" />
<flag name = "stateVisible" value = "4" />
<flag name = "stateAlwaysVisible" value = "5" />
<flag name = "adjustUnspecified" value = "0x00" />
<flag name = "adjustResize" value = "0x10" />
<flag name = "adjustPan" value = "0x20" />
<flag name = "adjustNothing" value = "0x30" />
</attr>

</declare-styleable>
```

> * 使用
```
<activity
    android:name = ".StyleAndThemeActivity"
    android:label = "@string/app_name"
    android:windowSoftInputMode = "stateUnspecified | stateUnchanged　|　stateHidden">
    <intent-filter>
        <action android:name = "android.intent.action.MAIN" />
        <category android:name = "android.intent.category.LAUNCHER" />
    </intent-filter>
</activity>
```


#### 属性定义时可以指定多种类型值

```
<declare-styleable name = "名称">

<attr name = "background" format = "reference|color" />

</declare-styleable>
```

个人主页：

<https://ln0491.github.io/>
<http://ln0491.coding.me/>
博客:
<http://blog.csdn.net/ko0491>
<http://www.cnblogs.com/liunanjava/>
源码： <https://github.com/ln0491/MyViewFormatDemo>