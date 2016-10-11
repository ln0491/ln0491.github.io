---
title: Android-按二次返回键退出.
date: 2016-10-11 14:39:56
categories: android
tags: android
---
## 按二次返回键退出的功能

#### 在onBackPressed 方法中做下时间判断

```
   /**
     * 最后按下的时间
     */
    private  long lastTime ;

    /**
     * 按二次返回键退出应用
     */
    @Override
    public void onBackPressed() {
        long currentTime = System.currentTimeMillis();

        if(currentTime-lastTime<2*1000){
            super.onBackPressed();
        }else {
            Toast.makeText(this, "再按一次退出应用", Toast.LENGTH_SHORT).show();
            lastTime=currentTime;
        }


    }
```

#### 第二种在onKeyDown 方法中做下判断

```
 @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {

        if(keyCode==KeyEvent.KEYCODE_BACK){
            //禁用返回键
            exit();
            return false;
        }

        return super.onKeyDown(keyCode, event);
    }
```
exit方法就是第一种的写法


#### 有些页面不让用返回键可以禁用

```
@Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {

        if(keyCode==KeyEvent.KEYCODE_BACK){
            //禁用返回键
            return false;
        }

        return super.onKeyDown(keyCode, event);
    }

```

