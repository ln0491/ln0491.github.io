---
title: ViewPager的基本使用
date: 2016-09-29 09:52:30
categories: android
tags: viewpager
---
ViewPagr应该非常常用的控件之一了
> * 引导页
> * 轮播图
最常用的地方，今天学习下最基本的用法
## 效果

![guid.gif](https://ln0491.github.io/img/guid.gif)
## 布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_welcome"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context="com.liu.viewpagerdemo.ui.WelcomeActivity">


    <android.support.v4.view.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

    </android.support.v4.view.ViewPager>
</RelativeLayout>
```

### Adapter

```android
public class WelcomeViewpagerAdapter extends PagerAdapter {
    //数据
    List<Integer> mDatas;
    Context mContext;

    public WelcomeViewpagerAdapter(Context context, List<Integer> datas) {
        mContext= context;
        mDatas = datas;
    }


    /**
     * 返回要滑动的VIew的个数
     * @return
     */
    @Override
    public int getCount() {
        return mDatas.size();
    }

    @Override
    public boolean isViewFromObject(View view, Object object) {
        return view == object;
    }


    /**
     * 做了两件事，第一：将当前视图添加到container中，第二：返回当前View
     * @param container
     * @param position
     * @return
     */
    @Override
    public Object instantiateItem(ViewGroup container, int position) {
      //  View view = View.inflate(mContext,R.layout.item_pager,null);
       // ImageView imageView = (ImageView) view.findViewById(R.id.ivDispaly);

        ImageView imageView = new ImageView(mContext);
        imageView.setImageResource(mDatas.get(position));
        imageView.setScaleType(ImageView.ScaleType.CENTER_CROP);
        //添加View到容器
        container.addView(imageView);

        //返回当前的View
        return imageView;
        //        return super.instantiateItem(container, position);
    }

    /**
     * 从当前container中删除指定位置（position）的View;
     * @param container
     * @param position
     * @param object
     */
    @Override
    public void destroyItem(ViewGroup container, int position, Object object) {
       // super.destroyItem(container, position, object);

        container.removeView((View) object);
    }
}

```

### Activity 

```android
public class WelcomeActivity extends AppCompatActivity {

    //viewpager
    private ViewPager mViewPager;
 
    //viewpager数据集合也可以用数组
    private List<Integer> mDatas = new ArrayList<>();
   // private int[] imageIds = new int[] { R.mipmap.img_recom01,R.mipmap.img_recom02, R.mipmap.img_recom03 };
    //viewpager适配器
    private WelcomeViewpagerAdapter mWelcomeViewpagerAdapter;
  

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        requestWindowFeature(Window.FEATURE_NO_TITLE);
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_welcome);
        initView();
        initData();
        
    }

    private void initView() {

        mViewPager = (ViewPager) findViewById(R.id.viewPager);

    }


    private void initData() {
        //添加图片
        mDatas.add(R.mipmap.img_recom01);
        mDatas.add(R.mipmap.img_recom02);
        mDatas.add(R.mipmap.img_recom03);

        initViewPager();
     
    }

    private void initViewPager() {
        mWelcomeViewpagerAdapter = new WelcomeViewpagerAdapter(this,mDatas);
        mViewPager.setAdapter(mWelcomeViewpagerAdapter);

    }
   
}
```

代码很简单，这是基本的用法
源码：<https://github.com/ln0491/ViewPagerDemo>