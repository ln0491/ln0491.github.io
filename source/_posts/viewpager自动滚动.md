---
title: viewpager自动滚动
date: 2016-10-08 13:15:53
categories: android
tags: viewpager
---

# viewpager自动滚动

### 先写一个普通的Viewpager
参考
基本使用<https://ln0491.github.io/2016/09/29/ViewPager%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/>

添加指示器
<https://ln0491.github.io/2016/10/08/ViewPager%E6%B7%BB%E5%8A%A0%E6%8C%87%E7%A4%BA%E5%99%A8/>

布局

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/holo_blue_dark"
    android:paddingBottom="@dimen/activity_vertical_margin"
    android:paddingLeft="@dimen/activity_horizontal_margin"
    android:paddingRight="@dimen/activity_horizontal_margin"
    android:paddingTop="@dimen/activity_vertical_margin"
    tools:context="com.liu.viewpagerdemo.ui.MainActivity">


    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="200dp"
        android:layout_marginTop="20dp">

        <android.support.v4.view.ViewPager
            android:id="@+id/viewPagerAuto"
            android:layout_width="match_parent"
            android:layout_height="200dp">

        </android.support.v4.view.ViewPager>
        <!-- 指示点 -->
        <LinearLayout
            android:id="@+id/dotsLayout"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_alignParentBottom="true"
            android:layout_centerHorizontal="true"
            android:layout_marginBottom="10dp"
            android:orientation="horizontal">
        </LinearLayout>
       
    </RelativeLayout>

</RelativeLayout>
```
### 适合器与Activity代码
```

/**
 * @Description: 广告viewpager适配器
 * @AUTHOR 刘楠  Create By 2016/10/8 0008 11:18
 */
public class MainAutoViewpagerAdapter extends PagerAdapter {

    private Context mContext;
    private List<Integer> mDatas;

    public MainAutoViewpagerAdapter(Context context, List<Integer> datas) {
        mContext = context;
        mDatas = datas;
    }

    @Override
    public int getCount() {
        return mDatas.size();
    }

    @Override
    public boolean isViewFromObject(View view, Object object) {
        return view==object;
    }


    @Override
    public Object instantiateItem(ViewGroup container, int position) {

        ImageView imageView = new ImageView(mContext);

        imageView.setImageResource(mDatas.get(position));

        imageView.setScaleType(ImageView.ScaleType.FIT_XY);


        container.addView(imageView);


        return imageView;
    }


    @Override
    public void destroyItem(ViewGroup container, int position, Object object) {
        container.removeView((View) object);
    }
}
```

Activity

```
 private void initView() {

        mAutoViewPager = (ViewPager) findViewById(R.id.viewPagerAuto);
        mDotsLayout = (LinearLayout) findViewById(R.id.dotsLayout);

    }

    private void initData() {
        //添加图片资源
        mDatas.add(R.mipmap.img_home_banner1);
        mDatas.add(R.mipmap.img_home_banner2);
        mDatas.add(R.mipmap.img_home_banner3);
        mDatas.add(R.mipmap.img_home_banner4);
        //初始化ViewPager
        initViewPager();
        initDots();

    }



    private void initViewPager() {
        mMainAutoViewpagerAdapter = new MainAutoViewpagerAdapter(this,mDatas);

        mAutoViewPager.setAdapter(mMainAutoViewpagerAdapter);
        mAutoViewPager.setPageTransformer(true,new DepthPageTransformer());

    }

    private void initDots() {
        mDotParams = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);

        mDotParams.rightMargin = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 5, getResources().getDisplayMetrics());


        for(int i = 0; i < mDatas.size(); i++) {

            ImageView imageView = new ImageView(this);
            imageView.setImageResource(R.drawable.dot_selector);

            imageView.setSelected(false);





            //添加到容器
            mDotsLayout.addView(imageView,mDotParams);

            dots.add(imageView);

        }
        dots.get(0).setSelected(true);



    }

    private void initListener() {

        initViewPagerListener();
    }

    private void initViewPagerListener() {

        mAutoViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

            }

            @Override
            public void onPageSelected(int position) {

                dots.get(mCurrPageIndex %mDatas.size()).setSelected(false);
                mCurrPageIndex =position;
                dots.get(mCurrPageIndex %mDatas.size()).setSelected(true);

            }

            @Override
            public void onPageScrollStateChanged(int state) {

            }
        });

    }
```
这样可以基本的Viewpager+指示器的使用

### 左右无限滑动

目前还不能无限滑动，适配器中返回的大不是数据的大小
```

    @Override
    public int getCount() {
        return mDatas.size();
    }
```
更改为无限大
```

    @Override
    public int getCount() {
       // return mDatas.size();
        return Integer.MAX_VALUE;
    }
```
改变初始化设置

//最余不然会下越界
**imageView.setImageResource(mDatas.get(position%mDatas.size()));**


这样貌似可以无限向左滑动，但向右滑动还不行,设置返因的数据量为一半
改变Activiy中初始化的值

        //当前的位置放大1000倍，
        mCurrPageIndex = mDatas.size()*1000;
        
        
这样就可以左右无限滑动

![vpmax.gif](https://ln0491.github.io/img/vpmax.gif)

### 无限滚动

```
 /**
     * 自动滚动任务
     */
    private class AutoScrollTask implements Runnable{



        //开始任务
        public void start(){

            //先移除
         stop();
            //再开始
            mHandler.postDelayed(this,5000);
        }

        @Override
        public void run() {

            // 完成viewpager的切换
            int currentItem = mAutoViewPager.getCurrentItem();
            //判断是不是最后一个
            if(currentItem==mAutoViewPager.getAdapter().getCount()-1){
                currentItem=0;
            }else {
                currentItem++;
            }
            //设置当前要显示的
            //mAutoViewPager.setCurrentItem(currentItem,true);
            mAutoViewPager.setCurrentItem(currentItem);

            start();
        }

        /**
         * 停止任务
         */
        public void stop(){
           
            //停止任务 不滚动
            mHandler.removeCallbacks(this);
        }
    }
```
开启任务
```
 private void initTask() {

        mAutoScrollTask = new AutoScrollTask();
        mAutoScrollTask.start();


    }
```


### 按下时，停止，抬起时继续

```
 private void initViewPagerTouchEvent() {

        mAutoViewPager.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {


                switch(event.getAction()){
                    case MotionEvent.ACTION_DOWN:
                        //按下时--停止
                        mAutoScrollTask.stop();

                        break;

                    case MotionEvent.ACTION_MOVE:
                        //移动时
                        break;
                    case MotionEvent.ACTION_UP:
                        //抬起时-开始
                        mAutoScrollTask.start();
                        break;
                    case MotionEvent.ACTION_CANCEL:
                        mAutoScrollTask.stop();
                        break;
                    default:
                        break;
                }


                return false;
            }
        });

    }
```
![vpauto.gif](https://ln0491.github.io/img/vpauto.gif)

### 第二种实现自动滚动方式 使用Handler发送消息

```java

    /**
     * 静态内部类
     * 防止内在泄漏
     */
    private static class MyHandler extends Handler {


        WeakReference<MainActivity2> mWeakReference;

        public MyHandler(MainActivity2 activity2) {
            mWeakReference = new WeakReference<MainActivity2>(activity2);
        }

        @Override
        public void handleMessage(Message msg) {
            //super.handleMessage(msg);
            if(msg.what != 1) {
                return;
            }

            MainActivity2 mainActivity2 = mWeakReference.get();


            int currentItem = mainActivity2.mAutoViewPager.getCurrentItem();

            if(currentItem == mainActivity2.mAutoViewPager.getAdapter().getCount() - 1) {
                currentItem = 0;
            } else {
                currentItem++;
            }

            mainActivity2.mAutoViewPager.setCurrentItem(currentItem);

            //循环发送
            Message msg2 = Message.obtain();
            msg2.what = 1;
            sendMessageDelayed(msg2, 5000);

        }
    }

    /**
     * 开始播放
     */
    public void startAutoScroll() {
        Message msg = Message.obtain();
        msg.what = 1;
        mHandler.sendMessageDelayed(msg, 5000);
    }

    /**
     * 停止播放
     */
    public void stopScroll() {
        // 清除所有消息 handleMessage就不能执行
        mHandler.removeCallbacksAndMessages(null);//

    }

    @Override
    protected void onStart() {
        super.onStart();
        //可见时就循环播放
        startAutoScroll();
    }

    @Override
    protected void onStop() {
        super.onStop();
        //不可见时就停止播放
        stopScroll();
    }
```




源码
<https://github.com/ln0491/ViewPagerDemo>

