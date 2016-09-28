---
title: Volley自定义Request
date: 2016-09-28 14:16:01
categories: android
tags: volley
---

#### 自定义Request

    public class StringRequest extends Request<String> {
            //监听器
        private final Listener<String> mListener;

        //构造方法 这时一定要调用super
        public StringRequest(int method, String url, Listener<String> listener, ErrorListener errorListener) {
            super(method, url, errorListener);
            this.mListener = listener;
        }

        //调用上面的方法
        public StringRequest(String url, Listener<String> listener, ErrorListener errorListener) {
            this(0, url, listener, errorListener);
        }
        
        //只是deliverResponse()方法中的实现很简单，仅仅是调用了mListener中的onResponse()方法，并将response内容传入即可，这样就可以将服务器响应的数据进行回调了
        protected void deliverResponse(String response) {
            this.mListener.onResponse(response);
        }

        protected Response<String> parseNetworkResponse(NetworkResponse response) {
            String parsed;
            try {
                //parseCharset字符编码
                parsed = new String(response.data, HttpHeaderParser.parseCharset(response.headers));
            } catch (UnsupportedEncodingException var4) {
                parsed = new String(response.data);
            }
            //头信息
            return Response.success(parsed, HttpHeaderParser.parseCacheHeaders(response));
        }
    }

由于Request类中的deliverResponse()和parseNetworkResponse()是两个抽象方法，
因此StringRequest中需要对这两个方法进行实现。deliverResponse()方法中的实现很简单，仅仅是调用了mListener中的onResponse()方法，并将response内容传入即可，这样就可以将服务器响应的数据进行回调了。
parseNetworkResponse()方法中则应该对服务器响应的数据进行解析,其中数据是以字节的形式存放在NetworkResponse的data变量中的，这里将数据取出然后组装成一个String，并传入Response的success()方法中即可。

#####自定义GsonRequest

    
    package com.liu.volleydemo.requestion;

    import com.android.volley.NetworkResponse;
    import com.android.volley.ParseError;
    import com.android.volley.Request;
    import com.android.volley.Response;
    import com.android.volley.toolbox.HttpHeaderParser;
    import com.google.gson.Gson;
    
    import java.io.UnsupportedEncodingException;
    
    /**
     * @Description: 描述
     * @AUTHOR 刘楠  Create By 2016/9/26 0026 18:11
     */
    public class GsonRequest<T> extends Request<T> {
        private final Response.Listener<T> mListener;
        private       Gson                 mGson;
        private       Class<T>             mClazz;
    
        public GsonRequest(int method, String url, Class<T> clazz, Response.Listener<T> listener, Response.ErrorListener errorListener) {
            super(method, url, errorListener);
            this.mListener = listener;
            mGson= new Gson();
            mClazz = clazz;
        }
    
        public GsonRequest(String url, Class<T> clazz, Response.Listener<T> listener, Response.ErrorListener errorListener) {
            this(Method.GET, url, clazz, listener, errorListener);
        }
    
        @Override
        protected void deliverResponse(T response) {
    
            mListener.onResponse(response);
    
        }
    
        @Override
        protected Response<T> parseNetworkResponse(NetworkResponse response) {
    
            try {
                /**
                 * 获取数据
                 */
                String jsonString = new String(response.data, HttpHeaderParser.parseCharset(response.headers));
                // 转化反回
                return Response.success(mGson.fromJson(jsonString, mClazz), HttpHeaderParser.parseCacheHeaders(response));
    
            } catch (UnsupportedEncodingException e) {
                e.printStackTrace();
                return Response.error(new ParseError(e));
            }
    
        }
    
    }

=================BEAN==========

package com.liu.volleydemo.bean;

import java.util.List;
        
        /**
         * @Description: 描述
         * @AUTHOR 刘楠  Create By 2016/9/26 0026 18:36
         */
        public class WeixinSelected {
        
        
           
        
            public String       reason;
           
        
            public ResultEntity result;
            public int error_code;
        
            @Override
            public String toString() {
                return "WeixinSelected{" +
                        "reason='" + reason + '\'' +
                        ", result=" + result +
                        ", error_code=" + error_code +
                        '}';
            }
        
            public static class ResultEntity {
                public int totalPage;
                public int ps;
                public int pno;
        
                @Override
                public String toString() {
                    return "ResultEntity{" +
                            "totalPage=" + totalPage +
                            ", ps=" + ps +
                            ", pno=" + pno +
                            ", list=" + list +
                            '}';
                }
        
        
        
                public List<ListEntity> list;
        
                public static class ListEntity {
                    public String firstImg;
                    public String id;
                    public String source;
                    public String title;
                    public String url;
                    public String mark;
        
                    @Override
                    public String toString() {
                        return "ListEntity{" +
                                "firstImg='" + firstImg + '\'' +
                                ", id='" + id + '\'' +
                                ", source='" + source + '\'' +
                                ", title='" + title + '\'' +
                                ", url='" + url + '\'' +
                                ", mark='" + mark + '\'' +
                                '}';
                    }
                }
            }
        }

请求

     final RequestQueue requestQueue = MyVolley.getRequestQueue(this);

        String       url    ="http://v.juhe.cn/weixin/query";
        final String appkey ="24834287692b8d54cf634668ce5de0b4";

       GsonRequest<WeixinSelected> gsonRequest = new GsonRequest<WeixinSelected>(Request.Method.POST,url, WeixinSelected.class, new Response.Listener<WeixinSelected>() {
            @Override
            public void onResponse(WeixinSelected weixinSelected) {

                Log.d("vivi", "onResponse: "+weixinSelected.toString());

            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError volleyError) {

                volleyError.printStackTrace();
                Log.d("vivi", "onErrorResponse: "+volleyError.getMessage());
            }
        }){
            @Override
            protected Map<String, String> getParams() throws AuthFailureError {

                Map<String,String> params = new HashMap<>();
                params.put("key",appkey);
                return params;
            }
        };

        requestQueue.add(gsonRequest);

>日志

    WeixinSelected{reason='success', result=ResultEntity{totalPage=25, ps=20, pno=1, list=[ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-8255071.jpg/640', id='wechat_20160915039850', source='功夫体育', title='女排冠军们“高”人一等，谁敢娶？', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160915039850', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-8575838.jpg/640', id='wechat_20151201058612', source='龍神迷失', title='中国首辆上牌的布加迪威龙，以为车牌很一般，最后发现车牌很霸气', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20151201058612', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-8206632.jpg/640', id='wechat_20160102022165', source='篮球频道', title='【球迷关注点】书豪:一度想退役不再打球 曾被科比一句蔑视激怒', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160102022165', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-8064767.jpg/640', id='wechat_20160908053966', source='娱乐圈扒姐', title='某知名女星在节目中威胁选手收贿，私下爱搞潜规则', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160908053966', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986866.jpg/640', id='wechat_20160906017693', source='颜小白的篮球梦', title='科比跟乔丹比到底差在哪里？仅仅是身体素质吗', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906017693', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986843.jpg/640', id='wechat_20160906017663', source='嘻嘻哈哈', title='神动图|这手感绝对不差！', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906017663', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986833.jpg/640', id='wechat_20160906017658', source='嘻嘻哈哈', title='江湖流传的这些奇葩书，简直刷新我的三观~', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906017658', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986819.jpg/640', id='wechat_20160906017552', source='笑你妹', title='【搞笑段子】有心无力啊，办起事来几分钟完事~', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906017552', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986802.jpg/640', id='wechat_20160906017288', source='TechWeb', title='在iPhone7发布之前我们先来看看它的跑分吧', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906017288', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986693.jpg/640', id='wechat_20160906016354', source='陇南生活网', title='甘肃：将发生严重腐败问题的地区部门直接评为“一般”', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906016354', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986675.jpg/640', id='wechat_20160906016213', source='云上的空母', title='万吨两栖舰上的袖珍火炮，射速为每分钟200发', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906016213', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986645.jpg/640', id='wechat_20160906016204', source='云上的空母', title='全球最大口径的加特林机关炮，一眨眼打出50发炮弹', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906016204', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986534.jpg/640', id='wechat_20160906015882', source='钛媒体', title='三星耗10亿美元召回Note7，并称损失令人real心痛钛晨报', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20160906015882', mark=''}, ListEntity{firstImg='http://zxpic.gtimg.com/infonew/0/wechat_pics_-7986438.jpg/640', id='wechat_20160906015702', source='摄影笔记', title='一篇文章看懂尼康数码单反', url='http://v.juhe.cn/weixin/redirect?wid=wechat_20


源码：
<https://github.com/ln0491/VolleyDemo>