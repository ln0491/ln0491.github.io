---
title: Volley基本用法
date: 2016-09-28 14:13:38
categories: android
tags: volley
---

## Volley基本用法

### 什么是volley

  Volley，是谷歌官方于2013年发布的用于Android平台的网络通信库.

### 下载Volley

    git clone https://android.googlesource.com/platform/frameworks/volley

自备梯子
在AndroidStudio中Dependencies中搜索
或者访问

<https://github.com/mcxiaoke/android-volley> 镜像地址

添加依赖
    
    compile 'com.android.volley:volley:1.0.0'

### Volley的基本使用

#### 创建一个RequestQueue对象。

         //获取请求队列  传入上下文
        RequestQueue requestQueue = Volley.newRequestQueue(this/getApplicationContext());

#### 创建一个Request对象

#### 将Request对象添加到RequestQueue里面。

##### StringRequest

     /**
     *
     * @param method 请求方法 get post   Request.Method.GET,Request.Method.POST
     * @param url   请求的URL
     * @param listener    请求成功的回调接口
     * @param errorListener  请求失败的回调接口
     */
    public StringRequest(int method, String url, Response.Listener<String> listener,
                         Response.ErrorListener errorListener) {
        super(method, url, errorListener);
        mListener = listener;
    }

    //get请求可以不写请求方法
    public StringRequest(String url, Listener<String> listener, ErrorListener errorListener) {
        this(Method.GET, url, listener, errorListener);
    }
######GET请求

例子: <https://www.juhe.cn/> 免费的接口可以申请

         String url    = "http://op.juhe.cn/onebox/weather/query";
        String appKey = "9275deacf73ae4316f48caa251ec501e";

        url += "?cityname=" + "深圳&key=" + appKey;

        //获取请求队列
        RequestQueue requestQueue = Volley.newRequestQueue(this);

        /**
         * 第一个参数 Request.Method.GET
         * 第二个url 请求URL
         * 第三个  new Response.Listener<String> 请求成功回调接口
         * 第四个请求失败回调接口
         */
        //构建请求
        StringRequest stringRequest = new StringRequest(Request.Method.GET, url, new Response.Listener<String>() {
            @Override
            public void onResponse(String response) {

                Log.d("vivi", "onResponse: "+response.toString());

                mTvResult.setText(response);

            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                error.printStackTrace();
                Log.d("vivi", "onResponse: "+error.getMessage());

                mTvResult.setText(error.getMessage());
            }
        });

        //加入请求队列
        requestQueue.add(stringRequest);

> 结果：

    onResponse: {"reason":"successed!","result":{"data":{"realtime":{"city_code":"101280601","city_name":"深圳","date":"2016-09-26","time":"12:00:00","week":1,"moon":"八月廿六","dataUptime":1474864566,"weather":{"temperature":"30","humidity":"76","info":"多云","img":"1"},"wind":{"direct":"西北风","power":"1级","offset":null,"windspeed":null}},"life":{"date":"2016-9-26","info":{"chuanyi":["炎热","天气炎热，建议着短衫、短裙、短裤、薄型T恤衫等清凉夏季服装。"],"ganmao":["少发","各项气象条件适宜，发生感冒机率较低。但请避免长期处于空调房间中，以防感冒。"],"kongtiao":["部分时间开启","天气热，到中午的时候您将会感到有点热，因此建议在午后较热时开启制冷空调。"],"wuran":["中","气象条件对空气污染物稀释、扩散和清除无明显影响，易感人群应适当减少室外活动时间。"],"xiche":["较适宜","较适宜洗车，未来一天无雨，风力较小，擦洗一新的汽车至少能保持一天。"],"yundong":["较适宜","天气较好，户外运动请注意防晒。推荐您进行室内运动。"],"ziwaixian":["中等","属中等强度紫外线辐射天气，外出时建议涂擦SPF高于15、PA+的防晒护肤品，戴帽子、太阳镜。"]}},"weather":[{"date":"2016-09-26","info":{"day":["1","多云","33","","微风","06:13"],"night":["1","多云","27","","微风","18:16"]},"week":"一","nongli":"八月廿六"},{"date":"2016-09-27","info":{"dawn":["1","多云","27","无持续风向","微风","18:16"],"day":["3","阵雨","33","","微风","06:13"],"night":["3","阵雨","27","","微风","18:15"]},"week":"二","nongli":"八月廿七"},{"date":"2016-09-28","info":{"dawn":["3","阵雨","27","无持续风向","微风","18:15"],"day":["10","暴雨","29","西南风","5-6 级","06:14"],"night":["10","暴雨","26","西南风","5-6 级","18:14"]},"week":"三","nongli":"八月廿八"},{"date":"2016-09-29","info":{"dawn":["10","暴雨","26","西南风","5-6 级","18:14"],"day":["9","大雨","29","","微风","06:14"],"night":["3","阵雨","24","","微风","18:13"]},"week":"四","nongli":"八月廿九"},{"date":"2016-09-30","info":{"dawn":["3","阵雨","24","无持续风向","微风","18:13"],"day":["3","阵雨","29","","微风","06:14"],"night":["3","阵雨","25","","微风","18:12"]},"week":"五","nongli":"八月三十"}],"pm25":{"key":"Shenzhen","show_desc":0,"pm25":{"curPm":"98","pm25":"72","pm10":"102","level":2,"quality":"良","des":"可以接受的，除极少数对某种污染物特别敏感的人以外，对公众健康没有危害。"},"dateTime":"2016年09月26日12时","cityName":"深圳"},"jingqu":"","date":"","isForeign":"0"}},"error_code":0}

###### POST请求
 

    StringRequest stringRequest = new StringRequest(Method.POST, url,  listener, errorListener); 

StringRequest中并没有提供设置POST参数的方法，但是当发出POST请求的时候，Volley会尝试调用StringRequest的父类——Request中的getParams()方法来获取POST参数,只需要在StringRequest的匿名类中重写getParams()方法

    
        String       url    = "http://op.juhe.cn/onebox/weather/query";
        final String appKey = "9275deacf73ae4316f48caa251ec501e";

        //建立请求队列
        RequestQueue requestQueue = Volley.newRequestQueue(this);

        StringRequest stringRequest = new StringRequest(Request.Method.POST, url, new Response.Listener<String>() {
            @Override
            public void onResponse(String response) {

                Log.d("vivi", "onResponse: " + response.toString());

                mTvResult.setText(response);

            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError volleyError) {
                volleyError.printStackTrace();
                Log.d("vivi", "onResponse: " + volleyError.getMessage());

                mTvResult.setText(volleyError.getMessage());
            }

        }){
            @Override
            protected Map<String, String> getParams() throws AuthFailureError {

                Map<String,String> params = new HashMap<>();
                params.put("cityname","深圳");
                params.put("key",appKey);

                return params;
            }
        };

        //添加到队列
        requestQueue.add(stringRequest);

### JsonRequest的用法
JsonRequest是一个抽象类,JsonRequest有两个直接的子类，JsonObjectRequest和JsonArrayRequest，一个是用于请求一段JSON数据的，一个是用于请求一段JSON数组的。
#### JsonObjectRequest
     /**
     * 
     * @param url 请求URL
     * @param jsonRequest  JSON对象
     * @param listener 成功回调
     * @param errorListener  失败回调
     */
    public JsonObjectRequest(String url, JSONObject jsonRequest, Response.Listener<JSONObject> listener,
                             Response.ErrorListener errorListener) {
        this(jsonRequest == null ? Request.Method.GET : Request.Method.POST, url, jsonRequest,
                listener, errorListener);
    }

    /**
     *  上面的会调下面的方法
     * @param method  
     * @param url
     * @param jsonRequest
     * @param listener
     * @param errorListener
     */
    public JsonObjectRequest(int method, String url, JSONObject jsonRequest,
                             Response.Listener<JSONObject> listener, Response.ErrorListener errorListener) {
        super(method, url, (jsonRequest == null) ? null : jsonRequest.toString(), listener,
                errorListener);
    }

##### GET请求

     String url ="http://v.juhe.cn/weixin/query";
        String appKey ="24834287692b8d54cf634668ce5de0b4";
        url += "?key=" + appKey;

        RequestQueue requestQueue = Volley.newRequestQueue(this);

        JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.GET, url, null, new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                Log.d("vivi", "onResponse: " + response.toString());

                mTvResult.setText(response.toString());
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                error.printStackTrace();
                Log.d("vivi", "onResponse: " + error.getMessage());

                mTvResult.setText(error.getMessage());
            }
        });
        //添加队列
        requestQueue.add(jsonObjectRequest);

> 日志

    onResponse: {"reason":"success","result":{"list":[{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-8255071.jpg\/640","id":"wechat_20160915039850","source":"功夫体育","title":"女排冠军们“高”人一等，谁敢娶？","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160915039850","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-8575838.jpg\/640","id":"wechat_20151201058612","source":"龍神迷失","title":"中国首辆上牌的布加迪威龙，以为车牌很一般，最后发现车牌很霸气","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20151201058612","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-8206632.jpg\/640","id":"wechat_20160102022165","source":"篮球频道","title":"【球迷关注点】书豪:一度想退役不再打球 曾被科比一句蔑视激怒","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160102022165","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-8064767.jpg\/640","id":"wechat_20160908053966","source":"娱乐圈扒姐","title":"某知名女星在节目中威胁选手收贿，私下爱搞潜规则","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160908053966","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986866.jpg\/640","id":"wechat_20160906017693","source":"颜小白的篮球梦","title":"科比跟乔丹比到底差在哪里？仅仅是身体素质吗","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906017693","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986843.jpg\/640","id":"wechat_20160906017663","source":"嘻嘻哈哈","title":"神动图|这手感绝对不差！","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906017663","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986833.jpg\/640","id":"wechat_20160906017658","source":"嘻嘻哈哈","title":"江湖流传的这些奇葩书，简直刷新我的三观~","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906017658","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986819.jpg\/640","id":"wechat_20160906017552","source":"笑你妹","title":"【搞笑段子】有心无力啊，办起事来几分钟完事~","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906017552","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986802.jpg\/640","id":"wechat_20160906017288","source":"TechWeb","title":"在iPhone7发布之前我们先来看看它的跑分吧","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906017288","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986693.jpg\/640","id":"wechat_20160906016354","source":"陇南生活网","title":"甘肃：将发生严重腐败问题的地区部门直接评为“一般”","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906016354","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986675.jpg\/640","id":"wechat_20160906016213","source":"云上的空母","title":"万吨两栖舰上的袖珍火炮，射速为每分钟200发","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906016213","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986645.jpg\/640","id":"wechat_20160906016204","source":"云上的空母","title":"全球最大口径的加特林机关炮，一眨眼打出50发炮弹","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906016204","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986534.jpg\/640","id":"wechat_20160906015882","source":"钛媒体","title":"三星耗10亿美元召回Note7，并称损失令人real心痛钛晨报","url":"http:\/\/v.juhe.cn\/weixin\/redirect?wid=wechat_20160906015882","mark":""},{"firstImg":"http:\/\/zxpic.gtimg.com\/infonew\/0\/wechat_pics_-7986438.jpg\/640","id":"wechat_20160906015702","source":"摄影笔记","title":"一篇文章看懂尼康数码单反","url":"http:\/\/v.juhe

##### POST请求
    
          String       url    ="http://v.juhe.cn/weixin/query";
        final String appKey ="24834287692b8d54cf634668ce5de0b4";

        RequestQueue requestQueue = Volley.newRequestQueue(this);
        JsonObjectRequest jsonObjectRequest = new JsonObjectRequest(Request.Method.POST, url, null, new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                Log.d("vivi", "onResponse: " + response.toString());

                mTvResult.setText(response.toString());
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                error.printStackTrace();
                Log.d("vivi", "onResponse: " + error.getMessage());

                mTvResult.setText(error.getMessage());
            }
        }){

            /**
             * 重写这个方法添加POST请求的参数
             * @return
             * @throws AuthFailureError
             */
            @Override
            protected Map<String, String> getParams() throws AuthFailureError {
                Map<String, String> params = new HashMap<>();
                params.put("key", appKey);

                return params;
            }
        };

        //添加队列
        requestQueue.add(jsonObjectRequest);
可能会提示KEY错误

#### JsonArrayRequest
    public JsonArrayRequest(int method, String url, JSONArray jsonRequest,
                            Listener<JSONArray> listener, ErrorListener errorListener) {
        super(method, url, (jsonRequest == null) ? null : jsonRequest.toString(), listener,
                errorListener);
    }

     public JsonArrayRequest(String url, Listener<JSONArray> listener, ErrorListener errorListener) {
        super(Method.GET, url, null, listener, errorListener);
    }

##### GET

    
        String url ="http://v.juhe.cn/toutiao/index";
        String appKey ="97a25ce56b4d4563c86884738eed8502";

        RequestQueue requestQueue = Volley.newRequestQueue(this);

        url+="?key="+appKey;

        JsonArrayRequest jsonArrayRequest = new JsonArrayRequest(url, new Response.Listener<JSONArray>() {
            @Override
            public void onResponse(JSONArray response) {
                Log.d("vivi", "onResponse: " + response.toString());

                mTvResult.setText("=====\n"+response.toString());
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                error.printStackTrace();
                Log.d("vivi", "onResponse: " + error.getMessage());

                mTvResult.setText(error.getMessage());
            }
        });

        requestQueue.add(jsonArrayRequest);

##### POST
    
     String       url    ="http://v.juhe.cn/toutiao/index";
        final String appKey ="97a25ce56b4d4563c86884738eed8502";

        RequestQueue requestQueue = Volley.newRequestQueue(this);
        JsonArrayRequest jsonArrayRequest = new JsonArrayRequest(Request.Method.POST, url, null, new Response.Listener<JSONArray>() {
            @Override
            public void onResponse(JSONArray response) {

            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {

            }
        }){
            @Override
            protected Map<String, String> getParams() throws AuthFailureError {
                Map<String, String> params = new HashMap<>();
                params.put("key", appKey);

                return params;
            }
        };

        requestQueue.add(jsonArrayRequest);
可能会提示KEY错误

#### 取消Request

>可以针对某些个request做取消操作

    @Override
    protected void onStop() {
        super.onStop();
        for (Request <?> req : mRequestQueue) {          
            req.cancel();         }     
    

    }
>取消这个队列里的所有请求：

     mRequestQueue.cancelAll(this);

>可以根据RequestFilter或者Tag来终止某些请求

    mRequestQueue.cancelAll( new RequestQueue.RequestFilter() {
            @Override
            public boolean apply(Request<?> request) {
                return false;
            }
        });         
        mRequestQueue.cancelAll(new Object());

或者 
通过使用tag来做到，在构造GET请求的时候，添加一个tag给它。
    request.setTag("GET");
    mRequestQueue.add(request);

如果要取消GET请求，只需简单的添加下面的一行代码：    

    mRequestQueue.cancelAll("GET");



感谢郭霖大大
<http://blog.csdn.net/guolin_blog/article/details/17482095>
源码：
<https://github.com/ln0491/VolleyDemo>