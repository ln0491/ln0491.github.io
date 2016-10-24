---
title: GRADLE命令简单学习
date: 2016-10-24 17:43:24
categories: android
tags: 工具
---


##GRADLE命令简单学习


### 1、./gradlew -v 来查看下项目所用的Gradle版本
如果你是第一次执行会去下载Gradle，这个过程如果不翻墙非常慢，建议翻墙

紧接着下载成功会看到如下信息：

	------------------------------------------------------------
	Gradle 2.2.1
	------------------------------------------------------------
	
	Build time:   2014-11-24 09:45:35 UTC
	Build number: none
	Revision:     6fcb59c06f43a4e6b1bcb401f7686a8601a1fb4a
	
	Groovy:       2.3.6
	Ant:          Apache Ant(TM) version 1.9.3 compiled on December 23 2013
	JVM:          1.7.0_60 (Oracle Corporation 24.60-b09)
	OS:           Mac OS X 10.9.5 x86_64
### 2、接着执行 ./gradlew clean


BUILD SUCCESSFUL
### 3、最后执行 ./gradlew build
这个命令会直接编译并生成相应的apk文件，如果看到如下字样就代表build成功了


### 很有用的命令 **assemble**

* ./gradlew assembleDebug 编译并打Debug包
* ./gradlew assembleRelease 编译并打Release的包除此之外，assemble还可以和productFlavors结合使用，具体在下一篇多渠道打包进一步解释。

* ./gradlew installRelease Release模式打包并安装
* ./gradlew uninstallRelease 卸载Release模式包



### 4.友盟多渠道打包
在AndroidManifest.xml里面会有这么一段
```
<meta-data
    android:name="UMENG_CHANNEL"
    android:value="Channel_ID" />
```

里面的Channel_ID就是渠道标示。
* 第一步 在AndroidManifest.xml里配置PlaceHolder
```
<meta-data
    android:name="UMENG_CHANNEL"
    android:value="${UMENG_CHANNEL_VALUE}" />
```

* 第二步 在build.gradle设置productFlavors

```
android {  
    productFlavors {
        xiaomi {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "xiaomi"]
        }
        _360 {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "_360"]
        }
        baidu {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "baidu"]
        }
        wandoujia {
            manifestPlaceholders = [UMENG_CHANNEL_VALUE: "wandoujia"]
        }
    }  
}
```
或者批量修改
```
android {  
    productFlavors {
        xiaomi {}
        _360 {}
        baidu {}
        wandoujia {}
    }  

    productFlavors.all { 
        flavor -> flavor.manifestPlaceholders = [UMENG_CHANNEL_VALUE: name] 
    }
}
```
直接执行 ./gradlew assembleRelease打包

### assemble结合Build Variants来创建task

* ./gradlew assembleDebug
* ./gradlew assembleRelease

assemble 还能和 Product Flavor 结合创建新的任务，其实 assemble 是和 Build Variants 一起结合使用的，而 Build Variants = Build Type + Product Flavor

* 如果我们想打包wandoujia渠道的release版本，执行如下命令就好了：

	./gradlew assembleWandoujiaRelease

如果我们只打wandoujia渠道版本，则：

	./gradlew assembleWandoujia

* 同理我想打全部Release版本：

	./gradlew assembleRelease


这条命令会把Product Flavor下的所有渠道的Release版本都打出来。

总之，assemble 命令创建task有如下用法
* **assemble**： 允许直接构建一个Variant版本，例如assembleFlavor1Debug。
* **assemble**： 允许构建指定Build Type的所有APK，例如assembleDebug将会构建Flavor1Debug和Flavor2Debug两个Variant版本。
* **assemble**： 允许构建指定flavor的所有APK，例如assembleFlavor1将会构建Flavor1Debug和Flavor1Release两个Variant版本。

```
  defaultConfig {
       
        
        // dex突破65535的限制
        multiDexEnabled true
        // 默认是umeng的渠道
        manifestPlaceholders = [UMENG_CHANNEL_VALUE: "umeng"]
    }

    lintOptions {
        abortOnError false
    }

    signingConfigs {
        debug {
            // No debug config
        }

        release {
            storeFile file("../yourapp.keystore")
            storePassword "your password"
            keyAlias "your alias"
            keyPassword "your password"
        }
    }
```