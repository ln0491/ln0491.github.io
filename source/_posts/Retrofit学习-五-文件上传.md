---
title: Retrofit学习(五)-文件上传
date: 2016-10-27 18:31:00
categories: android
tags: Retrofit
---

## Retrofit学习(五)-文件上传

Retrofit学习(一)-集成
<http://www.jianshu.com/p/86e5cddcc753>
Retrofit学习(二)-get请求
<http://www.jianshu.com/p/cdde02a0777c>
Retrofit学习(三)-普通post请求
<http://www.jianshu.com/p/7f252d10fd41>
Retrofit学习(四)-下载文件
<http://www.jianshu.com/p/f61645a770ae>
Retrofit学习(五)-文件上传
<http://www.jianshu.com/p/ca0cb8640c8f>
### 单个文件上传 @Multipart @Part

* 接口

```

    @Multipart
    @POST("user/imgUpLoad")
//    Call<String> uploadOne(@Part("sign") String sign,@Part("appKey") String appKey,@Part("osName") String osName,@Part("memberNo") String memberNo, @Part  MultipartBody.Part file);
//    Call<String> uploadOne(@PartMap Map<String,String> params, @Part  MultipartBody.Part file);

//    Call<String> uploadOne(@Query("sign") String sign, @Query("appKey") String appKey, @Query("osName") String osName, @Query("memberNo") String memberNo, @Part  MultipartBody.Part file);
    Call<String> uploadOne(@QueryMap Map<String,String> params, @Part  MultipartBody.Part file);
```

需要添加@Multipart表示支持文件上传的表单，Content-Type: multipart/form-data
以上4种写法都可以
普通参数@Query，@QueryMap
或者也@Part，这样

文件要使用
@Part  MultipartBody.Part file


* 实现

```


        String path = "/storage/emulated/0/Pictures/1477553156332.jpg";
        File file = new File(path);

        /**
         * 创建请求体，内容是文件
         */
        RequestBody requestFile = RequestBody.create(MediaType.parse("multipart/form-data"), file);
//        final RequestBody requestFile = RequestBody.create(MediaType.parse("image/*"), file);


        /**
         * 创建多部分拿上面的请求体做参数
         * img 是上传是的参数key,根据需要更改为自己的
         */
        MultipartBody.Part body =
                MultipartBody.Part.createFormData("img", file.getName(), requestFile);


        Map<String,String> params = new HashMap<>();
        params.put("sign", SIGN);
        params.put("appKey", APP_KEY);
        params.put("osName", OS_NAME);
        params.put("memberNo", "13410111258");

        /**
         * 初始化
         */
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(BASE_URL)
                .addConverterFactory(ScalarsConverterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        UploadService uploadService = retrofit.create(UploadService.class);
        Call<String> call  = uploadService.uploadOne(params,body);
       // Call<String> call  = uploadService.uploadOne(SIGN,APP_KEY,OS_NAME,"13410111258",body);
        call.enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {
                L.d("vivi",response.message()+"    "+response.body());
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {
                t.printStackTrace();
L.d("vivi",t.getMessage());
            }
        });
```
其中创建Part部分为重要
```
 String path = "/storage/emulated/0/Pictures/1477553156332.jpg";
        File file = new File(path);

        /**
         * 创建请求体，内容是文件
         */
 RequestBody requestFile = RequestBody.create(MediaType.parse("multipart/form-data"), file);
//        final RequestBody requestFile = RequestBody.create(MediaType.parse("image/*"), file);


        /**
         * 创建多部分拿上面的请求体做参数
         * img 是上传是的参数key,根据需要更改为自己的
         */
        MultipartBody.Part body =
                MultipartBody.Part.createFormData("img", file.getName(), requestFile);

```

>结果
```
OK    {"code":"M100001","msg":"成功","info":{"successMessage":"更改成功","memberHeadImg":"http://10.18.200.24:8080/image/cd-img/20161027183654155618134101112581.jpg"},"sign":""}
```
* 再来一个 单个文件上传

```

    @Multipart
    @POST("file//upload")
    Call<String> upload2(@Part   MultipartBody.Part file);

```

* 实现
```
 /**
     * 单个 文件上传
     * <p>
     * /storage/emulated/0/Pictures/1477553156332.jpg
     * /storage/emulated/0/Pictures/1474366085035.jpg
     * /storage/emulated/0/Pictures/1474522550302.jpg
     * /storage/emulated/0/Pictures/1474423699408.jpg
     * /storage/emulated/0/Pictures/1477553128722.jpg
     * /storage/emulated/0/Pictures/1474365776853.jpg
     */
    private void uploadOne2() {


        String baseUrl = "http://10.18.200.140:8080/hellosp/";


        Retrofit retrofit = new Retrofit.Builder().baseUrl(baseUrl).addConverterFactory(ScalarsConverterFactory.create()).addConverterFactory(GsonConverterFactory.create()).build();


        UploadService uploadService = retrofit.create(UploadService.class);


        String path = "/storage/emulated/0/Pictures/1477553156332.jpg";


        File file = new File(path);


        RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), file);


        /**
         * 创建多部分拿上面的请求体做参数
         * img 是上传是的参数key,根据需要更改为自己的
         */
        MultipartBody.Part body = MultipartBody.Part.createFormData("file", file.getName(), requestBody);


        Call<String> call = uploadService.upload2(body);


        call.enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {
                L.d("vivi", response.message() + "    " + response.body());
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {
                t.printStackTrace();
                L.d("vivi", t.getMessage());
            }
        });


    }

```

* 也可以另一种写法

```
 /**
     * 单个 文件上传
     * /storage/emulated/0/Pictures/1477553156332.jpg
     * /storage/emulated/0/Pictures/1474366085035.jpg
     * /storage/emulated/0/Pictures/1474522550302.jpg
     * /storage/emulated/0/Pictures/1474423699408.jpg
     * /storage/emulated/0/Pictures/1477553128722.jpg
     * /storage/emulated/0/Pictures/1474365776853.jpg
     */
    private void uploadOne3() {



        String baseUrl = "http://10.18.200.140:8080/hellosp/";


        Retrofit retrofit = new Retrofit.Builder().baseUrl(baseUrl).addConverterFactory(ScalarsConverterFactory.create()).addConverterFactory(GsonConverterFactory.create()).build();


        UploadService uploadService = retrofit.create(UploadService.class);


        String path = "/storage/emulated/0/Pictures/1477553156332.jpg";


        File file = new File(path);


        RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), file);
        Map<String, RequestBody> params = new HashMap<>();

        params.put("file\"; filename=\"" + file.getName() + "", requestBody);

        Call<String> call = uploadService.upload3(params);


        call.enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {
                L.d("vivi", response.message() + "    " + response.body());
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {
                t.printStackTrace();
                L.d("vivi", t.getMessage());
            }
        });


    }
```
看到不同了吗？参数的拼接不同
第一种
```
     /**
         * 创建请求体，内容是文件
         */
 RequestBody requestFile = RequestBody.create(MediaType.parse("multipart/form-data"), file);
//        final RequestBody requestFile = RequestBody.create(MediaType.parse("image/*"), file);


        /**
         * 创建多部分拿上面的请求体做参数
         * img 是上传是的参数key,根据需要更改为自己的
         */
        MultipartBody.Part body =
                MultipartBody.Part.createFormData("img", file.getName(), requestFile);
```
第二种

```
 RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), file);
        Map<String, RequestBody> params = new HashMap<>();

        params.put("file\"; filename=\"" + file.getName() + "", requestBody);
```
第二种比较适合多文件上传 

file 是参数


### 多个文件上传

* 接口

```

    @Multipart
    @POST("file//upload")
    Call<String> upload22(@PartMap   Map<String,RequestBody> params);
```

* 实现

```
 /**
     * 文件上传
     * <p>
     * /storage/emulated/0/Pictures/1477553156332.jpg
     * /storage/emulated/0/Pictures/1474366085035.jpg
     * /storage/emulated/0/Pictures/1474522550302.jpg
     * /storage/emulated/0/Pictures/1474423699408.jpg
     * /storage/emulated/0/Pictures/1477553128722.jpg
     * /storage/emulated/0/Pictures/1474365776853.jpg
     */
    private void uploadOne22() {


        String baseUrl = "http://10.18.200.140:8080/hellosp/";


        Retrofit retrofit = new Retrofit.Builder().baseUrl(baseUrl).addConverterFactory(ScalarsConverterFactory.create()).addConverterFactory(GsonConverterFactory.create()).build();


        UploadService uploadService = retrofit.create(UploadService.class);


        String path  = "/storage/emulated/0/Pictures/1477553156332.jpg";
        String path2 = "/storage/emulated/0/Pictures/1474366085035.jpg";
        // String path3 = "/storage/emulated/0/Pictures/1474522550302.jpg";
        String path4 = "/storage/emulated/0/Pictures/1474423699408.jpg";
        String path5 = "/storage/emulated/0/Pictures/1477553128722.jpg";
        String path6 = "/storage/emulated/0/Pictures/1474365776853.jpg";


        File file  = new File(path);
        File file2 = new File(path2);
        // File file3 = new File(path3);
        File file4 = new File(path4);
        File file5 = new File(path5);
        File file6 = new File(path6);


        File[] files = new File[]{file, file2, file4, file5, file6};


        Map<String, RequestBody> params = new HashMap<>();


        for(int i = 0; i < files.length; i++) {
            RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), files[i]);
            params.put("file\"; filename=\"" + files[i].getName() + "", requestBody);
        }






      /*  *//**
         * 创建多部分拿上面的请求体做参数
         * img 是上传是的参数key,根据需要更改为自己的
         *//*
        MultipartBody.Part body =
                MultipartBody.Part.createFormData("file", file.getName(), requestBody);
*/

        Call<String> call = uploadService.upload22(params);


        call.enqueue(new Callback<String>() {
            @Override
            public void onResponse(Call<String> call, Response<String> response) {
                L.d("vivi", response.message() + "    " + response.body());
            }

            @Override
            public void onFailure(Call<String> call, Throwable t) {
                t.printStackTrace();
                L.d("vivi", t.getMessage());
            }
        });


    }
```


基本的应用就算完成了 

源码：
<https://github.com/ln0491/RetrofitDemo2>
上传服务器代码 
<https://github.com/ln0491/hellosp>
半吊子springMVC写的有需要的就用吧，上传路径自己改下
在UploadController-addUser方法


