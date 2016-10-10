---
title: Android-三级联动更改样式
date: 2016-10-10 14:19:53
categories: android
tags: android
---

### 三级联动
<http://blog.csdn.net/ko0491/article/details/52769418>

原来的样式好难看，发现没有设置样式的地方，只有更改源码一途了

### Drawable资源

* wheel_bg.xml 将面的内容注释
这个用来设置背景色的
```xml

<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
<!--	<item>
		<shape android:shape="rectangle">
			<gradient
				android:startColor="#333"
				android:centerColor="#DDD"
				android:endColor="#333"
				android:angle="90" />
				
				<stroke android:width="1dp" android:color="#FF333333" />
		</shape>
	</item>
	<item android:left="4dp" android:right="4dp" android:top="1dp" android:bottom="1dp">
		<shape android:shape="rectangle">
			<gradient
				android:startColor="#AAA"
				android:centerColor="#FFF"
				android:endColor="#AAA"
				android:angle="90" />
		</shape>
	</item>-->
</layer-list>
```
*  wheel_val.xml
 控件中间选择中部分
 ```xml
 <shape xmlns:android="http://schemas.android.com/apk/res/android">
	<!--<gradient
		android:startColor="#70222222"
		android:centerColor="#70222222"
		android:endColor="#70EEEEEE"
		android:angle="90" />

	<stroke android:width="1dp" android:color="#70333333" /> -->

	<!--
	渐变
	-->
	<gradient
		android:startColor="#22ffffff"
		android:centerColor="#00ffffff"
		android:endColor="#22ffffff"
		android:angle="90" />
	
</shape>
 ```
### WheelView类更改阴影色
```java
   /**
     * Top and bottom shadows colors
     */
//    private static final int[] SHADOWS_COLORS = new int[]{0xeeFF0000, 0xee00FF00, 0xee0000FF};

    /**
     * 更改阴影色
     */
    private static final int[] SHADOWS_COLORS = new int[]{ 0xeeffffff, 0xeaffffff, 0x33ffffff };

```
这样就有渐变的效果了
![这里写图片描述](http://img.blog.csdn.net/20161010105800649)

### 中间选择部分加2条线

* 在drawCenterRect方法 中
```
private void drawCenterRect(Canvas canvas) {
        int center = getHeight() / 2;
        //条止的偏移量
        int offset = (int) (getItemHeight() / 2 * 1.2);
        centerDrawable.setBounds(0, center - offset, getWidth(), center + offset);
        centerDrawable.draw(canvas);
        /**
         * 中间选中的画2条线
         *
         */
        //上方的线
        canvas.drawLine(0,center-offset,getWidth(),center-offset,mPaint);
        //下方的线
        canvas.drawLine(0,center+offset,getWidth(),center+offset,mPaint);
    }
```
前面要初始化画笔
```

    /**
     * 初始化画笔
     */
    private void initPaint() {

        mPaint = new Paint();
        //锯齿
        mPaint.setAntiAlias(true);
        //画笔颜色
//        mPaint.setColor(0xffebebeb);
        //为了看出效果
        mPaint.setColor(0xffEA2000);
        //宽度
        mPaint.setStrokeWidth(1f);

    }
```
![这里写图片描述](http://img.blog.csdn.net/20161010110452470)

### 选择中字体变大，不选择时变小

字体更改要更改Adapter中的设置--AbstractWheelTextAdapter

* 设置属性
```
 /**
     *  当前的索引
     */
    private int currentIndex = 0;
    /**
     * 最大字体
     */
    private static int maxsize = 24;
    /**
     *   最小字体
     */
    private static int minsize = 14;
```

* 更改构造方法

```
 protected AbstractWheelTextAdapter(Context context) {
        this(context, TEXT_VIEW_ITEM_RESOURCE);
    }

    /**
     * Constructor
     * @param context the current context
     * @param itemResource the resource ID for a layout file containing a TextView to use when instantiating items views
     */
    protected AbstractWheelTextAdapter(Context context, int itemResource) {
        this(context, itemResource, NO_RESOURCE,0,maxsize,minsize);
    }
    
    /**
     * Constructor
     * @param context the current context
     * @param itemResource the resource ID for a layout file containing a TextView to use when instantiating items views
     * @param itemTextResource the resource ID for a text view in the item layout
     */
    protected AbstractWheelTextAdapter(Context context, int itemResource, int itemTextResource,int currentIndex,int maxsize,int minsize) {
        this.context = context;
        itemResourceId = itemResource;
        itemTextResourceId = itemTextResource;
        /**
         *  为属性赋值
         */
        this.currentIndex=currentIndex;
        this.maxsize = maxsize;
        this.minsize = minsize;
        
        inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    }
```

这样还是没有效果的
更改AbstractWheelTextAdapter 中
```
 public View getItem(int index, View convertView, ViewGroup parent) {
```
方法:

```
 @Override
    public View getItem(int index, View convertView, ViewGroup parent) {
        if (index >= 0 && index < getItemsCount()) {
            if (convertView == null) {
                convertView = getView(itemResourceId, parent);
            }
            TextView textView = getTextView(convertView, itemTextResourceId);
            if (textView != null) {
                CharSequence text = getItemText(index);
                if (text == null) {
                    text = "";
                }
                textView.setText(text);
                /**
                 * 设置字体设置
                 *
                 */

            if(index==currentIndex){
                textView.setTextSize(maxsize);
            }else {
                textView.setTextSize(minsize);
            }

    
                if (itemResourceId == TEXT_VIEW_ITEM_RESOURCE) {
                    configureTextView(textView);
                }
            }
            return convertView;
        }
    	return null;
    }
  ```
  貌似只有第一个会变
  ![这里写图片描述](http://img.blog.csdn.net/20161010111411657)

*  Adapter中把所有Item都存放起来

```java
/**
     * 存放Item容器
     */
    private ArrayList<View> arrayList = new ArrayList<View>();

    /**
     * getter 方法对外使用
     * @return
     */
    public ArrayList<View> getTextViews() {
        return arrayList;
    }
 ```  
* 在getItem方法中添加

```
 @Override
    public View getItem(int index, View convertView, ViewGroup parent) {
        if (index >= 0 && index < getItemsCount()) {
            if (convertView == null) {
                convertView = getView(itemResourceId, parent);
            }
            TextView textView = getTextView(convertView, itemTextResourceId);

            /**
             * 添加到容器中
             */
            if (!arrayList.contains(textView)) {
                arrayList.add(textView);
            }
            
            if (textView != null) {
                CharSequence text = getItemText(index);
                if (text == null) {
                    text = "";
                }
                textView.setText(text);
                /**
                 * 设置字体设置
                 *
                 */

            if(index==currentIndex){
                textView.setTextSize(maxsize);
            }else {
                textView.setTextSize(minsize);
            }

    
                if (itemResourceId == TEXT_VIEW_ITEM_RESOURCE) {
                    configureTextView(textView);
                }
            }
            return convertView;
        }
    	return null;
    }

```

这样貌似还是不行

*  对控件添加监听并设置字体
```java
 private void initListener() {

        /**
         * 滚动中改变
         */
        mWheelView.addChangingListener(new OnWheelChangedListener() {
            @Override
            public void onChanged(WheelView wheel, int oldValue, int newValue) {
                //获取当前的索引
                int currentItem = wheel.getCurrentItem();
                /**
                 * 获取内容
                 */
                String text = (String) mBirthYearAdapter.getItemText(currentItem);
                /**
                 * 设置字体
                 *
                 */

                setItemTextSize(text,mBirthYearAdapter);
               // Toast.makeText(MainActivity.this, text, Toast.LENGTH_SHORT).show();
            }
        });


        /**
         * 滚动完成改变
         */
        mWheelView.addScrollingListener(new OnWheelScrollListener() {
            @Override
            public void onScrollingStarted(WheelView wheel) {

            }

            @Override
            public void onScrollingFinished(WheelView wheel) {
                //获取当前的索引
                int currentItem = wheel.getCurrentItem();
                /**
                 * 获取内容
                 */
                String text = (String) mBirthYearAdapter.getItemText(currentItem);
                /**
                 * 设置字体
                 *
                 */


                setItemTextSize(text,mBirthYearAdapter);
                Toast.makeText(MainActivity.this, text, Toast.LENGTH_SHORT).show();
            }
        });
```

* 设置字体大小方法

```
 /**
     * 设置字体大小
     * @param currentItemText
     * @param birthYearAdapter
     */
    private  void setItemTextSize(String currentItemText,BirthYearAdapter birthYearAdapter){
            //获取所有的View
        ArrayList<View>  arrayLists = birthYearAdapter.getTextViews();

        int size = arrayLists.size();
        //当前条目的内容
        String currentText;
        for(int i = 0; i < size; i++) {
            TextView textview = (TextView) arrayLists.get(i);
            currentText = textview.getText().toString().trim();

            if (currentItemText.equals(currentText)) {
                textview.setTextSize(maxsize);
            } else {
                textview.setTextSize(minsize);
            }
        }

    }
 ```
 效果
![这里写图片描述](http://img.blog.csdn.net/20161010141532894)

### 三级联动一样一做法
![这里写图片描述](http://img.blog.csdn.net/20161010141556852)



源码:
<https://github.com/ln0491/WheelDemo2>




