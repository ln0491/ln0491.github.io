---
title: viewpager在最后一页滑动之后跳转到主页面.
date: 2016-10-08 11:03:59
categories: android
tags:	viewpager
---

## viewpager在最后一页滑动之后跳转到主页面

### 添加切换监听器 

```android
mViewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {
			//判断是不是要跳转下页一个标记位
            private boolean flag;

            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {
             
            }

            @Override
            public void onPageSelected(int position) {
            


            }

            @Override
            public void onPageScrollStateChanged(int state) {


            }
        });
````


### 在onPageScrollStateChanged方法中判断是不是最后一个，同是是不是拖放状态
```android
			@Override
            public void onPageScrollStateChanged(int state) {

                Log.d("vivi", "onPageScrollStateChanged: " + state);
                switch(state) {
                    case ViewPager.SCROLL_STATE_DRAGGING:
                        //拖的时候才进入下一页
                        flag = false;
                        Log.d("vivi", "SCROLL_STATE_DRAGGING: " + ViewPager.SCROLL_STATE_DRAGGING);

                        break;
                    case ViewPager.SCROLL_STATE_SETTLING:
                        flag = true;
                        Log.d("vivi", "SCROLL_STATE_SETTLING: " + ViewPager.SCROLL_STATE_SETTLING);
                        break;


                    case ViewPager.SCROLL_STATE_IDLE:
                        Log.d("vivi", "SCROLL_STATE_IDLE: " + ViewPager.SCROLL_STATE_IDLE+"  mViewPager.getCurrentItem() "+mViewPager.getCurrentItem());
                        /**
                         * 判断是不是最后一页，同是是不是拖的状态
                         */
                        if(mViewPager.getCurrentItem() == mWelcomeViewpagerAdapter.getCount() - 1 && !flag) {
                            Intent localIntent = new Intent();

                            localIntent.setClass(WelcomeActivity.this, MainActivity.class);
                            startActivity(localIntent);
                            // overridePendingTransition(0, 0);
                            finish();
                        }
                        flag = true;

                        break;
                }
```



源码
<https://github.com/ln0491/ViewPagerDemo>
