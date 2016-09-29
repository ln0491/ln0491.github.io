---
title: Bitmap和Base64的相互转换
date: 2016-09-29 15:58:17
categories: android
tags: 工具类
---

## Bitmap和Base64的相互转换  

```android
public class Base64Util {
	public static void gcBitmap(Bitmap bitmap) {
		if (bitmap != null && !bitmap.isRecycled()) {
			bitmap.recycle(); // 回收图片所占的内存
			bitmap = null;
			System.gc(); // 提醒系统及时回收
		}
	}

	/**
	 * 
	 * @Title: bitmapToBase64 
	 * @Description: TODO(Bitmap 转换为字符串)       
	 * @param @param bitmap
	 * @param @return    设定文件
	 * @return String    返回类型    
	 * @throws
	 */

	@SuppressLint("NewApi")
	public static String bitmapToBase64(Bitmap bitmap) {

		// 要返回的字符串
		String reslut = null;

		ByteArrayOutputStream baos = null;

		try {
			
			if (bitmap != null) {
				
				baos = new ByteArrayOutputStream();
				/**
				 * 压缩只对保存有效果bitmap还是原来的大小
				 */
				bitmap.compress(CompressFormat.JPEG, 30, baos);
				
				baos.flush();
				baos.close();
				// 转换为字节数组
				byte[] byteArray = baos.toByteArray();
				
				// 转换为字符串
				reslut = Base64.encodeToString(byteArray, Base64.DEFAULT);
			} else {
				return null;
			}
		} catch (IOException e) {
			e.printStackTrace();
		} finally {

			try {
				if (baos != null) {
					baos.close();
				}
			} catch (IOException e) {
				e.printStackTrace();
			}

		}
		return reslut;

	}
	
	/**
	 * 
	 * @Title: base64ToBitmap 
	 * @Description: TODO(base64l转换为Bitmap)       
	 * @param @param base64String
	 * @param @return    设定文件
	 * @return Bitmap    返回类型    
	 * @throws
	 */
	public static Bitmap base64ToBitmap(String base64String){
		
		byte[] decode = Base64.decode(base64String, Base64.DEFAULT);
		
		Bitmap bitmap = BitmapFactory.decodeByteArray(decode, 0, decode.length);
		
		return bitmap;
	}
}
```

将将bitmap转换成base64的字符串，直接把字符串传给服务器