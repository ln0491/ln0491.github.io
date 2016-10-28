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


### 多个文件上传

