---
title: ViewPager填充Fragment和懒加载
date: 2016-10-11 13:27:56
categories: android
tags: viewpager
---
## Tablayout+ViewPager填充Fragment和懒加载

![这里写图片描述](http://img.blog.csdn.net/20161011133031109)

### 使用Fragment来填充ViewPager
TabLayout+ViewPager+Fragmenet是使用非常多的控件组合。

### TabLayout组件的简单介绍

 -  TabLayout是Material Design组件开发中的一种，使用时需要先导入design库的依赖。
 -  TabLayout做出的效果是一排可以滑动的Tab，相当于一排指示器。
 -  在布局中申明控件
```xml
<android.support.design.widget.TabLayout
        android:id="@+id/tablayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:tabBackground="@color/colorPrimaryDark"
        app:tabIndicatorColor="#ff0000"
        app:tabIndicatorHeight="2dp"
        app:tabTextColor="@color/colorAccent"
        app:tabSelectedTextColor="#FFFFFF"
        app:tabMode="scrollable"
        app:tabPadding="5dp">

    </android.support.design.widget.TabLayout>
```

 - 基本属性介绍，布局和代码都可以使用

 - 列表内容

	 - tabBackground 背景色
	 - tabIndicatorColor：指示器颜色，横线
	 - tabIndicatorHeight 指示器高度
	 - tabTextColor 指示器，文字 没有选择时正常的颜色
	 - tabSelectedTextColor  指示器，文字 选择时颜色
	 - tabPadding  指示器 内边距
	 - app:tabMode="scrollable" 模式，有scrollable和fixed两种，分别表示可以滑动和固定的意思
 -----------

 - 代码中绑定ViewPager
	- 在ViewPager设置Adapter之后，调用tabLayout的setupWithViewPager方法绑定ViewPager
	- 设置显示的内容
	- 可以调用tabLayout的addTab方法添加
	-也可以重写ViewPager适配器的getPagerTitle方法返回显示的内容

 
### FragmentPagerAdapter与FragmentStatePagerAdapter的区别

#### FragmentPagerAdapter
* FragmentPagerAdapter类默认会对getItem()方法返回的Fragment做缓存处理，只有当第一次打开ViewPager的页面才会去创建Fragment的对象，后面再打开页面时就会直接从缓存中获取Fragment对象的引用，这样getItem方法就不会调用了。

* 因为FragmentPagerAdapter做了缓存处理，所有当创建很多的Fragment时内存就会吃不消，应用程序有可能会崩掉，所以呢，FragmentPagerAdapter不适合做大量数据的Fragment显示，比较适合数据不变的静态Fragment显示。

#### FragmentStatePagerAdapter
* FragmentStatePagerAdapter正好与之相反，每次进入页面时都会创建Fragment对象，每次滑出时都会销毁对应的Fragment对象，没有做一点的缓存，这样内存就完全的解放出来了。

* FragmentStatePagerAdapter在销毁Fragment时会调用onSaveInstanceState方法保存一些数据信息，然后下一次创建Fragment时会将这些数据读取出来

### ViewPager的预加载

ViewPager天生会加载左右两侧的页面，这是通过一个叫做DEFAULT_OFFSCREEN_PAGES的属性指定的，通过setOffscreenPageLimit(int)可以指定这个属性的值。

```
 private static final int DEFAULT_OFFSCREEN_PAGES = 1;

public void setOffscreenPageLimit(int limit) {
        if (limit < DEFAULT_OFFSCREEN_PAGES) {
            Log.w(TAG, "Requested offscreen page limit " + limit + " too small; defaulting to "
                    + DEFAULT_OFFSCREEN_PAGES);
            limit = DEFAULT_OFFSCREEN_PAGES;
        }
        if (limit != mOffscreenPageLimit) {
            mOffscreenPageLimit = limit;
            populate();
        }
    }
```
发现要是给的值小于1，那还是默认为1，也就是说这个方法只能指定加载更多的页面。并不能解决问题,设置为0，还是不设置都一样默认为1，只有大于1时才有作用

### Fragment实现懒加载


Fragment的生命周期中，我们一般在onCreateView方法初始化视图，onActivityCreated方法初始化数据

* 通过setUserVisibleHint和getUserVisibleHint方法来设置和获取Fragment的显示状态，当显示了才去加载数据。因为每一个Fragment都是这样，所以在BaseFragment中完成操作。

```
 /**
     * setUserVisibleHint是在onCreateView之前调用的
     *
     * @param isVisibleToUser
     */
    @Override
    public void setUserVisibleHint(boolean isVisibleToUser) {

        super.setUserVisibleHint(isVisibleToUser);

        /**
         * 判断是否可见
         */
        if(getUserVisibleHint()) {

            isVisible = true;
            //执行可见方法-初始化数据之类
            onVisible();

        } else {

            isVisible = false;
            //不可见
            onInvisible();
        }

    }
```

* onActivityCreated中也做判断
```
 @Override
    public void onActivityCreated(@Nullable Bundle savedInstanceState) {

        super.onActivityCreated(savedInstanceState);
        //这里 初始化view的各控件 数据
        isPrepared = true;
        lazyLoad();

    }
```
*  lazyLoad

```
/**
     * 可见做懒加载
     */
    private void onVisible() {
        lazyLoad();
    }

    /**
     * 懒加载
     */
    private void lazyLoad() {
        /**
         * 判断是否可见，或者 初始化view的各控件
         */
        if(!isVisible || !isPrepared) {
            return;
        }
        //可见 或者 控件初始化完成 就 加载数据
        initData();

    }
```

#### 第二种 将lazyLoad抽象方法 在子类在实现

子Fragment继承BaseFramentTabLayout
```
// 标志位，标志已经初始化完成。
    private boolean isPrepared;
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        Log.d(LOG_TAG, "onCreateView");
        View view = inflater.inflate(R.layout.fragment_open_result, container, false);
        //XXX初始化view的各控件
    isPrepared = true;
        lazyLoad();
        return view;
    }
    @Override
    protected void lazyLoad() {
        if(!isPrepared || !isVisible) {
            return;
        }
        //填充各控件的数据
    }
```


个人主页
<https://ln0491.github.io/>
<http://ln0491.coding.me/>
源码：
<https://github.com/ln0491/ViewPagerDemo>