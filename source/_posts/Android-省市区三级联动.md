---
title: Android-省市区三级联动
date: 2016-10-09 18:59:41
categories: android
tags: android
---

## 省市区三级联动
下载地址：<https://code.google.com/p/android-wheel/>自备梯子
> * 效果图
![三级联动](http://img.blog.csdn.net/20161009183700341)

如果想更改样式需要更改源码

## 单级滚动 

把下载好的做为module导入项目，并添加依赖

![这里写图片描述](http://img.blog.csdn.net/20161009184021852)
这样就可以像使用普通控件一样使用了

```xml

    <kankan.wheel.widget.WheelView
        android:id="@+id/wheelviewTimer"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        />

```

### Activty



```java
  mWheelViewTimer = (WheelView) findViewById(R.id.wheelviewTimer);
   for(int i = 0; i <24 ; i++) {
            mDatas.add(i+"");
        }

        //设置可以见的条目数量， 上中下
        mWheelViewTimer.setVisibleItems(5);


        //设置适配器
        mTimerAdapter = new TimerAdapter(this,mDatas);

        mWheelViewTimer.setViewAdapter(mTimerAdapter);

        //放在最后面 设置当前显示的
        mWheelViewTimer.setCurrentItem(5);
```


### 适配器
感觉和ListView有没有一点像呢,都是要设置适配器
```
public class TimerAdapter extends AbstractWheelTextAdapter {


    List<String> mDatas;
    Context      mContext;
 

    public TimerAdapter(Context context, List<String> datas) {

        super(context, R.layout.item_time, NO_RESOURCE);

        mContext = context;
        mDatas = datas;
        setItemTextResource(R.id.time);
    }

    @Override
    protected CharSequence getItemText(int index) {
        return mDatas.get(index);
    }

    @Override
    public int getItemsCount() {
        return mDatas.size();
    }

    @Override
    public View getItem(int index, View convertView, ViewGroup parent) {

        View view = super.getItem(index, convertView, parent);

        return view;


    }

```

这样就完成了，一个滚动
![这里写图片描述](http://img.blog.csdn.net/20161009184515111)

## 三级联动

看了一级的，三级的说白了就是3个这个控件

### 布局
```java

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"

        android:orientation="horizontal">

        <kankan.wheel.widget.WheelView
            android:id="@+id/province"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            />

        <kankan.wheel.widget.WheelView
            android:id="@+id/city"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            />

        <kankan.wheel.widget.WheelView
            android:id="@+id/area"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            />

    </LinearLayout>
```

### 适配器和上面一样

```
public class AddressTextAdapter extends AbstractWheelTextAdapter {
    List<String> mDatas;
    Context mContext;

    public AddressTextAdapter(Context context, List<String> datas) {
        super(context, R.layout.item_address,NO_RESOURCE);
        mContext = context;
        mDatas=datas;
        //设置资源要显示在那个控件上
        setItemTextResource(R.id.tempValue);
    }

    @Override
    protected CharSequence getItemText(int index) {
        return mDatas.get(index);
    }

    @Override
    public int getItemsCount() {
        return mDatas.size();
    }

    @Override
    public View getItem(int index, View convertView, ViewGroup parent) {


        View view = super.getItem(index,convertView,parent);

        return view;
    }

    public String getName(int index){
        return mDatas.get(index);
    }
```
### Activity初始化数据

* 找到3个控件
```java
 mProvince = (WheelView) findViewById(R.id.province);
        mCity = (WheelView) findViewById(city);
        mArea = (WheelView) findViewById(R.id.area);
```
* 初始化数据
```
 //读取文件
        String str = FileUtils.readAssetsFile(this, "china-city-area-zip.min.json");

        Log.d("vivi", "initData:  " + str);

        //转化为对象集合
        List<RegionJson> datas = JSON.parseArray(str, RegionJson.class);
```
这里的数据放在Assets目录下的json文件，读取文件，解析就OK，数据在最下面可以找到
* 分类数据
```java
 for(RegionJson data : datas) {


            //省
            mProvinceList.add(data.name);

            List<String> mCitysList    = new ArrayList<>();

            for(RegionJson.ChildEntity city : data.child) {

                //市

                mCitysList.add(city.name);
                List<String> mAreaList     = new ArrayList<>();
                for(RegionJson.ChildEntity.ChildEntity2 area : city.child) {
                    //区

                    mAreaList.add(area.name);
                }

                //市-区对该 
                mAreaDatasMap.put(city.name,mAreaList);


            }
            //省支市对应
            mCitisDatasMap.put(data.name,mCitysList);


        }
```
* 为三个控件设置适配器

```java
 /**
         *  省
         */

        mProvince.setVisibleItems(5);

        mProviceAdapter = new AddressTextAdapter(this, mProvinceList);
        mProvince.setViewAdapter(mProviceAdapter);
        mProvince.setCurrentItem(0);

        /**
         * 市
         */
        mCity.setVisibleItems(5);

        mCityAdapter = new AddressTextAdapter(this, mCitisDatasMap.get(mProvinceList.get(0)));
        mCity.setViewAdapter(mCityAdapter);
        mCity.setCurrentItem(0);

        /**
         * 地区
         */

        mArea.setVisibleItems(5);

        mAreaAdapter = new AddressTextAdapter(this, mAreaDatasMap.get(mCitisDatasMap.get(mProvinceList.get(0)).get(0)));
        mArea.setViewAdapter(mAreaAdapter);
        mArea.setCurrentItem(0);
```

### 设置监听滚动完成里切换
```java

private void initScrllListener() {
        /**
         * 省滚动完成时切换 城市与地区
         */
        mProvince.addScrollingListener(new OnWheelScrollListener() {
            @Override
            public void onScrollingStarted(WheelView wheel) {
                //标记位-开始滚动
                proviceScrolling =true;
            }

            @Override
            public void onScrollingFinished(WheelView wheel) {

                proviceScrolling =false;
                //省滚动完成后切换市
                updateCities(mCity,mCitisDatasMap.get(mProviceAdapter.getName(mProvince.getCurrentItem())));
                //更新地区
                updateArea(mArea,mAreaDatasMap.get(mCityAdapter.getName(mCity.getCurrentItem())));

            }
        });

        /**
         * 市滚动 完成时切换地区
         */
        mCity.addScrollingListener(new OnWheelScrollListener() {
            @Override
            public void onScrollingStarted(WheelView wheel) {
                //标记位-开始滚动
                cityScrolling=true;
            }

            @Override
            public void onScrollingFinished(WheelView wheel) {
                cityScrolling =false;


                //市滚动完成后切换
               updateArea(mArea,mAreaDatasMap.get(mCityAdapter.getName(mCity.getCurrentItem())));

            }
        });
    }
```
这样就基本完成了

### 完善滚动中时也可以切换

```java
		 /**
         * 省滚动中切换市
         */
        mProvince.addChangingListener(new OnWheelChangedListener() {
            @Override
            public void onChanged(WheelView wheel, int oldValue, int newValue) {

                //省在滚动
                if(proviceScrolling){
                    //更新城市
                    updateCities(mCity,mCitisDatasMap.get(mProviceAdapter.getName(newValue)));

                        //更新地区
                        updateArea(mArea,mAreaDatasMap.get(mCityAdapter.getName(mCity.getCurrentItem())));

                }

            }

        });
```

```
  mCity.addChangingListener(new OnWheelChangedListener() {
            @Override
            public void onChanged(WheelView wheel, int oldValue, int newValue) {

                if(cityScrolling){
                    updateArea(mArea,mAreaDatasMap.get(mCityAdapter.getName(newValue)));
                }
            }
        });
```

### 更新数据的2个方法

```

    /**
     * 切换城市
     */
    private void updateCities(WheelView city, List<String> cities) {

        mCityAdapter = new AddressTextAdapter(this,cities);

        city.setViewAdapter(mCityAdapter);
        city.setCurrentItem(0);
    }


----------
 private void updateArea(WheelView area,List<String> areas){
       mAreaAdapter= new AddressTextAdapter(this,areas);

        area.setViewAdapter(mAreaAdapter);
        area.setCurrentItem(0);
    }

```

源码：<https://github.com/ln0491/WheelDemo>

