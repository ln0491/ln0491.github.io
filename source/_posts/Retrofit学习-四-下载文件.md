---
title: Retrofit学习(四)-下载文件
date: 2016-10-27 17:01:28
categories: android
tags: Retrofit
---

## Retrofit学习(四)-下载文件

Retrofit学习(一)-集成
<http://www.jianshu.com/p/86e5cddcc753>
Retrofit学习(二)-get请求
<http://www.jianshu.com/p/cdde02a0777c>
Retrofit学习(三)-普通post请求
<http://www.jianshu.com/p/7f252d10fd41>


### @Streaming:用于下载大文件

```

    @Streaming //大文件时要加不然会OOM
    @GET
    Call<ResponseBody> downloadFile(@Url String fileUrl);
```

### 实现

```
 private void startDownload() {

            //http://surl.qq.com/195D0D?qbsrc=51&asr=4286

        String downloadUrl = "195D0D?qbsrc=51&asr=4286";

//
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("http://surl.qq.com/")
                 .addConverterFactory(ScalarsConverterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();


        DownloadService downloadService = retrofit.create(DownloadService.class);

        Call<ResponseBody> responseBodyCall = downloadService.downloadFile(downloadUrl);


        responseBodyCall.enqueue(new Callback<ResponseBody>() {
            @Override
            public void onResponse(Call<ResponseBody> call, final Response<ResponseBody> response) {

                L.d("vivi",response.message()+"  length  "+response.body().contentLength()+"  type "+response.body().contentType());

                //建立一个文件
                final File        file          = FileUtils.createFile(DownLoadActivity.this);
                
                //下载文件放在子线程
                new Thread(){
                    @Override
                    public void run() {
                        super.run();
                        //保存到本地
                        FileUtils.writeFile2Disk(response, file, new HttpCallBack() {
                            @Override
                            public void onLoading(final long current, final long total) {
                                /**
                                 * 更新进度条
                                 */
                                runOnUiThread(new Runnable() {
                                    @Override
                                    public void run() {
                                        L.d("vivi",current+" to "+total);
                                        L.d("vivi"," runOnUiThread  "+ currentThread().getName());
                                        mTv.setText(current+"");
                                        mPb.setMax((int) total);
                                        mPb.setProgress((int) current);


                                    }
                                });
                            }
                        });


                    }
                }.start();

            }

            @Override
            public void onFailure(Call<ResponseBody> call, Throwable t) {

                t.printStackTrace();
                L.d("vivi",t.getMessage()+"  "+t.toString());
            }
        });


    }

```

### 工具类

```
package com.liu.retrofitdemo2.util;

import android.content.Context;
import android.os.Environment;

import com.liu.retrofitdemo2.callback.HttpCallBack;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;

import okhttp3.ResponseBody;
import retrofit2.Response;

/**
 * @Description: 描述
 * @AUTHOR 刘楠  Create By 2016/10/27 0027 15:56
 */
public class FileUtils {


    public static File createFile(Context context){


        File file=null;
        String state = Environment.getExternalStorageState();

        if(state.equals(Environment.MEDIA_MOUNTED)){

            file = new File(Environment.getExternalStorageDirectory().getAbsolutePath()+"/test.apk");
        }else {
            file = new File(context.getCacheDir().getAbsolutePath()+"/test.apk");
        }

        L.d("vivi","file "+file.getAbsolutePath());

        return file;

    }




    public static void writeFile2Disk(Response<ResponseBody> response, File file, HttpCallBack httpCallBack){


        long currentLength = 0;
        OutputStream os =null;

        InputStream is = response.body().byteStream();
        long totalLength =response.body().contentLength();

        try {

            os = new FileOutputStream(file);


            int len ;

            byte [] buff = new byte[1024];

            while((len=is.read(buff))!=-1){

                os.write(buff,0,len);
                currentLength+=len;
                L.d("vivi","当前进度:"+currentLength);
                httpCallBack.onLoading(currentLength,totalLength);
            }
           // httpCallBack.onLoading(currentLength,totalLength,true);


        } catch(FileNotFoundException e) {
            e.printStackTrace();
        } catch(IOException e) {
            e.printStackTrace();
        } finally {
            if(os!=null){
                try {
                    os.close();
                } catch(IOException e) {
                    e.printStackTrace();
                }
            }
            if(is!=null){
                try {
                    is.close();
                } catch(IOException e) {
                    e.printStackTrace();
                }
            }
        }



    }




}

```


源码：
<https://github.com/ln0491/RetrofitDemo2>