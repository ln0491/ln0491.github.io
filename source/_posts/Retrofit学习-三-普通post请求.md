---
title: Retrofit学习(三)-普通post请求
date: 2016-10-26 18:03:34
categories: android
tags: Retrofit
---


## Retrofit学习(三)-普通post请求

Retrofit学习(一)-集成
<http://www.jianshu.com/p/86e5cddcc753>
Retrofit学习(二)-get请求
<http://www.jianshu.com/p/cdde02a0777c>


###  Post表单提交-单个参数 @Field

* 接口

```
  /** 表单提交要加 @FormUrlEncoded
     * 登录
     * @param username 用户名
     * @param password 密码
     * @param deviceId 设置ID
     * @param platform 平台这里是Android
     * @param verId 版本号
     * @return
     */
    @FormUrlEncoded
    @POST("login")
    Call<String> login(@Field("loginName") String username,
                       @Field("password") String password,
                       @Field("deviceId") String deviceId,
                       @Field("platform") String platform,
                       @Field("verId") String verId);
```

* 调用

```
    private void login(){

        /**
         * 初始化
         */
        Retrofit retrofit = new Retrofit.Builder()
                 .baseUrl(baseUrl)
                .addConverterFactory(ScalarsConverterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        //生成对象的Service
        LoginService loginService = retrofit.create(LoginService.class);
        //调用方法得到Call
        Call<String> loginCall      = loginService.login("home123",
                "123456",
                getDeviceId(),
                PLATFORM,
                VERSION_ID);


        //异步执行
        loginCall.enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {

                L.d("vivi",response.message()+"   "+response.body());
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {

               L.d("vivi",t.getMessage());
               L.d("vivi",t.toString());
            }
        });


    }

	/**
     * 获取设备号
     * @return
     */
    private String getDeviceId(){


        TelephonyManager tm = (TelephonyManager) getSystemService(Context.TELEPHONY_SERVICE);


        String deviceId = tm.getDeviceId();
        L.d("vivi",deviceId);

        return deviceId;
    }
```



* 权限
* 
```
    <!--读取手机状态的权限-->
    <uses-permission android:name="android.permission.READ_PHONE_STATE"/>
```


* 结果

```
OK   {"msg":"OK","result":{"accessToken":"A0BdgCrMG22CwfSKrlsEPAs3ZAozND","securiyCode":"5jUduYno","time":null,"userId":"402882823a9d5e0d013a9dabe3680002","userName":"home","verId":"V1.0"},"status":1,"verId":"1.0"}
```




###  Post表单提交-多个参数-@FieldMap

```

    /**
     * 表单提交要加 @FormUrlEncoded 
     * Post使用map多参数
     * @param params
     * @return
     */
    @FormUrlEncoded
    @POST("login")
    Call<LoginBean> login2(@FieldMap Map<String,String>params);
```

* 调用 

```

        /**
         * 初始化
         */
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(baseUrl)
                .addConverterFactory(ScalarsConverterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        Map<String, String> hashMap = new HashMap<String, String>();
        hashMap.put("loginName", "home123");
        hashMap.put("password", "123456");
        hashMap.put("deviceId", getDeviceId());
        hashMap.put("platform", PLATFORM);
        hashMap.put("verId", VERSION_ID);


        //生成对象的Service
        LoginService loginService = retrofit.create(LoginService.class);

        //调用方法得到Call
        Call<LoginBean> loginCall = loginService.login2(hashMap);


        //异步执行
        loginCall.enqueue(new Callback<LoginBean>() {
            @Override
            public void onResponse(Call<LoginBean> call, Response<LoginBean> response) {

                L.d("vivi",response.message()+"   "+response.body().toString());
            }

            @Override
            public void onFailure(Call<LoginBean> call, Throwable t) {

                L.d("vivi",t.getMessage());
                L.d("vivi",t.toString());
            }
        });
```
结果与第一个一样，这样就拿到token



### 使用@HeaderMap过滤请求 动态添加请求动


@Headers()
这个是写在方法上的
如果请求头是固定的，就可以使用这个

@Header与@HeaderMap作为参数使用的
请求头不固定，要动态添加 就使用，2个用法就和@Field 与@FieldMap一样

* 接口

```
 
    @POST("p/userCenter")
    Call<String> getUserCenter(@HeaderMap Map<String,String> headers);


```

* 调用

```
 private void getUserInfo(){
        
        /**
         * 初始化
         */
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(baseUrl)
                .addConverterFactory(ScalarsConverterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        //生成对象的Service
        LoginService loginService = retrofit.create(LoginService.class);
      /*  SimpleDateFormat     dateFormat   = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

        String              date  = dateFormat.format(new Date());*/

        /**
         * 这里测试参数写死了，实际可以改变，
         */
        Map<String, String> headers = new HashMap<String, String>();
        headers.put("userId", "402882823a9d5e0d013a9dabe3680002");
        headers.put("userName", "home");
        headers.put("accessToken", "A0BdgCrMG22CwfSKrlsEPAs3ZAozND");
        //当前时间，因为后台加密，原因写死 
        headers.put("time", "2016-10-27 13:28:07");
        headers.put("verId","V1.0");

    //这个是加载后的数据后台接收后，同样有一个比对
        String validateCode = "674BA63B66D0D4A1E6C9EED16CE193C7";
        headers.put("code", validateCode);


        Call<String> userCall = loginService.getUserCenter(headers);
        userCall.enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {
                L.d("vivi",response.message()+"   "+response.body());
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {
                L.d("vivi",t.getMessage());
                L.d("vivi",t.toString());
            }
        });

    }
```

* 结果
```
OK   {"msg":"OK","result":{"availablePredeposit":28,"favoriteLength":2,"messageCount":1,"nickName":"home123","portraitPic":"2016/10/27/92f2272d-cf41-4f22-bf46-905575197441.jpg","score":602,"subCountsDtoList":[{"status":"UNPAY","subCounts":15},{"status":"PADYED","subCounts":1},{"status":"SUCCESS","subCounts":1},{"status":"CLOSE","subCounts":3}],"userId":"402882823a9d5e0d013a9dabe3680002","userMobile":"13800138000","userName":"home"},"status":1,"verId":"1.0"}
```

* 如果传过去是一个空的map就请求不到数据

```
OK   {"msg":"401 user validate failed" ,"result":null,"status":"401","verId":"1.0"}
```


@Headers()
@Header()
@HeaderMap
基本上是用于过滤请求的


源码：
<https://github.com/ln0491/RetrofitDemo2>


