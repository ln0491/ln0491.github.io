---
title: my new post
date: 2016-09-28 09:35:12
tags: test
---
		Volley自定义Reques
		<!--more-->
### Volley自定义Reques
```android
public class GsonRequest<T> extends Request<T> {

	private final Listener<T> mListener;
	private Gson mGson;
	private Class<T> mClazz;

	public GsonRequest(int method, String url, Class<T> clazz, Listener<T> listener, ErrorListener errorListener) {
		super(method, url, errorListener);
		this.mListener = listener;
		mClazz = clazz;
	}

	public GsonRequest(String url, Class<T> clazz, Listener<T> listener, ErrorListener errorListener) {
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

```