---
title: Retrofit学习(二)-get请求.
date: 2016-10-26 13:29:32
categories: android
tags: Retrofit
---

## Retrofit学习(二)-get请求

### 环境、简单封装
Retrofit学习(一)-集成
<http://www.jianshu.com/p/86e5cddcc753>



### 建立接口 
```

    /**
     * 方法不要加返回值之类的void String。。CAll<String>代表返回值
     * @return
     */
    @GET("rank")
    Call<String> getRank(@Query("key") String key ,@Query("area") String area);
```

#### 建立请求

```
 String baseUrl = "http://v.juhe.cn/boxoffice/";

        String appKey = "a915893513986a67fdac70235db2c0af";


        Retrofit retrofit = new Retrofit.Builder()
                 .baseUrl(baseUrl)
                .addConverterFactory(ScalarsConverterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        DayService dayService = retrofit.create(DayService.class);


        Call<String> call = dayService.getRank(appKey,"CN");



        call.enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {

                L.d("vivi",response.message()+ "    "+ response.body());
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {

                t.printStackTrace();
            }
        });
```

>结果：

```
vivi: OK    {"resultcode":"200","reason":"success","result":[{"rid":"1","name":"湄公河行动","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"53200","tboxoffice":"53200"},{"rid":"2","name":"从你的全世界路过","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"46800","tboxoffice":"46800"},{"rid":"3","name":"爵迹","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"27500","tboxoffice":"27500"},{"rid":"4","name":"王牌逗王牌","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"18900","tboxoffice":"18900"},{"rid":"5","name":"鲁滨逊漂流记","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"3800","tboxoffice":"3800"},{"rid":"6","name":"逗鸟外传：萌宝满天飞","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"3050","tboxoffice":"3050"},{"rid":"7","name":"铠甲勇士捕王","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"1745","tboxoffice":"1745"},{"rid":"8","name":"我是哪吒","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"927","tboxoffice":"927"},{"rid":"9","name":"七月与安生","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"359","tboxoffice":"16689"},{"rid":"10","name":"疯狂丑小鸭","wk":"2016.10.1- 2016.10.7（单位：万元）","wboxoffice":"318","tboxoffice":"318"}],"error_code":0}
```

这里看到
@Query表示一个参数key,String 表示要传进来的值
@Query("key") String key ,@Query("area") String area

String 的名字可以和@Query中的不一样，建立保持一致，
@Query表示是键


#### 将数据转换为BEAN，一样写法只是返回值变为实体类

```
public class RankBean {



    public String resultcode;
    public String reason;
    public int    error_code;
  

    public List<ResultEntity> result;

    public static class ResultEntity {
        public String rid;
        public String name;
        public String wk;
        public String wboxoffice;
        public String tboxoffice;
    }
}


    /**
     * 方法不要加返回值之类的void String。。CAll<String>代表返回值
     * @return
     */
    @GET("rank")
    Call<RankBean> getRank(@Query("key") String key , @Query("area") String area);
```


### 参数过多时使用@QueryMap


```
 /**
     * 使用@QueryMap
     * @param params
     * @return
     */
    @GET("rank")
    Call<RankBean> getRank2(@QueryMap Map<String,String> params);

 /**
     * 使用QueryMap传参数
     */
    private void getQueryMap() {
        String baseUrl = "http://v.juhe.cn/boxoffice/";
        String appKey = "a915893513986a67fdac70235db2c0af";


        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(baseUrl)
                .addConverterFactory(ScalarsConverterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        DayService dayService = retrofit.create(DayService.class);


        /**
         * 传入多个参数，自己写key-value
         */
        Map<String,String> params = new HashMap<>();

        params.put("key",appKey);
        params.put("area","HK");
        Call<RankBean> rank2Call = dayService.getRank2(params);

        rank2Call.enqueue(new Callback<RankBean>() {
            @Override
            public void onResponse(Call<RankBean> call, Response<RankBean> response) {
                Toast.makeText(FirstActivity.this, "response  \n"+response.body().toString(), Toast.LENGTH_SHORT).show();
                L.d("vivi",response.message()+ "    "+ response.body().toString());
                mTvResult.setText(response.body().toString());
            }

            @Override
            public void onFailure(Call<RankBean> call, Throwable t) {
                t.printStackTrace();
            }
        });



    }
```


### URL注意事件
图片来源:
<https://inthecheesefactory.com/blog/retrofit-2.0/en>
![apiservice1.png](https://ln0491.github.io/img/apiservice1.png)
![apiservice2.png](https://ln0491.github.io/img/apiservice2.png)
![apiservice3.png](https://ln0491.github.io/img/apiservice3.png)

Base URL: 在最后加下 /

@Url（@GET,@POST）: 不要在开始位置加 /

### 配制HEADER

用 @Headers 设置Http请求的header
```
@Headers("Cache-Control: max-age=640000")
@GET("/widget/list")
void widgetList(Call<List<Widget>> call);
```

```
@Headers({
    "Accept: application/vnd.github.v3.full+json",
    "User-Agent: Retrofit-Sample-App"
})
@GET("/users/{username}")
void getUser(@Path("username") String username, Call<User>);
```
 Call<User>写前面也行，写这里也行

Http请求头不会互相覆盖，如果存在多个请求头的名字相同，那么它们全部都会被包含在Http请求中。


#### 动态更新HEADER
在请求方法的参数中，用 @Header 动态更新Http请求头

```
@GET("/user")
  void getUser(@Header("Authorization") String authorization)
```
参数不能为null，否则这个header将会被删掉。
@Headers可以用来判断用户是否有权限访问某些资源，如：访问用户的个人信息,只有登录了，才能带着自己的ID，tokey之类的去取个人信息


源码：
<https://github.com/ln0491/RetrofitDemo2>
