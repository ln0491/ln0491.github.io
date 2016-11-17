---
title: javaBase64使用
date: 2016-11-17 11:06:11
categories: java
tags: base64
---

## 测试

	public static void main(String[] args) {

		String str = "Hello, world!";
		System.out.println("未加密前：" + str + "  长度:" + str.length());

		String encoderString = toBase64Encode(str);
		System.out.println("加密: " + encoderString + "  长度:" + encoderString.length());

		String decoderString = toDecodeFromeBase64(encoderString);
		System.out.println("解密： " + decoderString + "  长度:" + decoderString.length());

		String path = "D:/tmp/ba.png";

		String imgString = fileToBase64String(path);
		System.out.println("图片转base64 : " + imgString);

		boolean flag = base64StringToFile(imgString);
		System.out.println("flag = " + flag);
	}
## 使用Base64加密字符串

	/**
	 * 使用Base64加密字符串
	* @Title: toBase64Encode
	* @Description: TODO(这里用一句话描述这个方法的作用)
	* @param @param str
	* @param @return    设定文件
	* @return String    返回类型
	* @throws
	 */
	public static String toBase64Encode(String str) {
		String result = null;

		try {
			result = new BASE64Encoder().encode(str.getBytes("utf8"));
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();

		}

		return result;
	}
## 使用Base64解码
	/**
	 * 使用Base64解码
	* @Title: toDecodeFromeBase64
	* @Description: TODO(这里用一句话描述这个方法的作用)
	* @param @param str
	* @param @return    设定文件
	* @return String    返回类型
	* @throws
	 */
	public static String toDecodeFromeBase64(String str) {
		String result = null;
		byte[] decodeBuffer;

		try {
			BASE64Decoder decoder = new BASE64Decoder();
			decodeBuffer = decoder.decodeBuffer(str);

			result = new String(decodeBuffer, "utf8");
		} catch (IOException e) {
			e.printStackTrace();
		}

		return result;
	}

## 将一个图片文件转化为Bases64字符串
	/**
	 * 根据路径是加密一个图片
	* @Title: fileToBase64String
	* @Description: TODO(这里用一句话描述这个方法的作用)
	* @param @param path
	* @param @return    设定文件
	* @return String    返回类型
	* @throws
	 */
	public static String fileToBase64String(String path) {
		String result = null;

		InputStream in = null;
		byte[] data;
		try {
			in = new FileInputStream(path);
			// 声明字符数组长度为流的长度
			data = new byte[in.available()];

			// 读取文件到流中
			in.read(data);
			// 转化为字符串
			BASE64Encoder encoder = new BASE64Encoder();
			// 加密
			result = encoder.encode(data);

		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		} finally {
			IOUtils.close(in);
		}

		return result;
	}
## 将base64字符串转化文件
	/**
	 * 将base64字符串转化文件
	* @Title: base64StringToFile
	* @Description: TODO(这里用一句话描述这个方法的作用)
	* @param @param base64String
	* @param @return    设定文件
	* @return boolean    返回类型
	* @throws
	 */
	public static boolean base64StringToFile(String base64String) {

		boolean flag = false;

		if (base64String == null) {
			return flag;

		}
		BASE64Decoder decoder = new BASE64Decoder();
		OutputStream out = null;
		// Base64解码
		try {
			// 读取字符数据中
			byte[] data = decoder.decodeBuffer(base64String);
			// 调整数据
			for (int i = 0; i < data.length; ++i) {
				if (data[i] < 0) {// 调整异常数据
					data[i] += 256;
				}
			}

			// 生成jpeg图片
			String imgFilePath = "d:/tmp/test22.png";// 新生成的图片
			out = new FileOutputStream(imgFilePath);
			out.write(data);
			out.flush();

			flag = true;

		} catch (IOException e) {
			e.printStackTrace();
			flag = false;
		} finally {
			IOUtils.close(out);
		}

		return flag;
	}



