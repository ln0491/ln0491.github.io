---
title: ViewPager添加指示器
date: 2016-10-08 10:08:50
categories: android
tags: viewpager
---


## ViewPager添加指示器

接着基本使用的例子
基本使用<https://ln0491.github.io/2016/09/29/ViewPager%E7%9A%84%E5%9F%BA%E6%9C%AC%E4%BD%BF%E7%94%A8/>

### 布局中添加存放揭示器的容器
```xml
 <!--
    存放指示器的容器
    -->
    <LinearLayout
        android:id="@+id/llLayoutDots"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="20dp"
        android:orientation="horizontal">


    </LinearLayout>
```


### Activity中 
```android
	//dots容器
    private LinearLayout mLlLayoutDots;
 //指示器布局参数
    private LinearLayout.LayoutParams mDotParams;

    //指示器容器列表
    private List<ImageView> dots = new ArrayList<ImageView>();
    //当前索引
    private int currPageIndex;

```
> 初始化指示器
```android
 private void initDots() {
        mDotParams = new LinearLayout.LayoutParams(LinearLayout.LayoutParams.WRAP_CONTENT, LinearLayout.LayoutParams.WRAP_CONTENT);

        mDotParams.rightMargin = (int) TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 10, getResources().getDisplayMetrics());

        //根据viewpager 的数据去添加指示器
        for(int i = 0; i < mDatas.size(); i++) {

            ImageView imageView = new ImageView(getApplicationContext());

            //添加背景 选择器
            //imageView.setBackgroundResource(R.drawable.dot_selector);
            //添加图片选择器
            imageView.setImageResource(R.drawable.dot_selector);

            //默认为不选择灰色

            imageView.setSelected(false);

            //添加到dot容器
            mLlLayoutDots.addView(imageView, mDotParams);

            dots.add(imageView);
        }

        //设置第一个为选择状态
        dots.get(0).setSelected(true);

    }
```

> viewpager设置监听
```android
private void initViewPagerListener() {


        mViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
                Log.d("vivi", "onPageScrolled:  position    " + position + "   positionOffset   " + positionOffset + "  positionOffsetPixels " + positionOffsetPixels);
            }

            @Override
            public void onPageSelected(int position) {
               /* int childCount = mLlLayoutDots.getChildCount();

                for(int i = 0; i < childCount; i++) {
                    if(i == position) {
                        mLlLayoutDots.getChildAt(i).setSelected(true);
                    } else {
                        mLlLayoutDots.getChildAt(i).setSelected(false);
                    }

                }*/

                //旧点不亮
                dots.get(currPageIndex%mDatas.size()).setSelected(false);
                //设置新的点
                currPageIndex=position;
                //新点亮起来
                dots.get(currPageIndex%mDatas.size()).setSelected(true);


            }

            @Override
            public void onPageScrollStateChanged(int state) {

                Log.d("vivi", "onPageScrollStateChanged: " + state);
            }
        });
    }
```

> * 效果

![vpdots.gif](https://ln0491.github.io/img/vpdots.gif)