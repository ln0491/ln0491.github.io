---
title: spring-boot入门四自定义属性
date: 2017-03-01 14:29:28
categories: spring-boot
tags: spring-boot
---

### spring-boot入门四自定义属性

#### 新建application.properties
```
book.author=john
book.name=Spring boot入门自定义属性
book.price=18.3
```

#### 修改Contoller
```

	@Value("${book.author}")
	private String bookAuthor;
	@Value("${book.name}")
	private String bookName;
	@Value("${book.price}")
	private Double bookPrice;
	
	@RequestMapping(value="/index",method=RequestMethod.GET)
	public String index(HttpServletRequest request){
		return "Hello Spring boot"+bookAuthor+"-->"+bookName+"-->"+bookPrice;
		
	}
	
```


访问http://localhost:8080/index
```
Hello Spring bootjohn-->Spring boot入门自定义属性-->18.3
```

#### 多个属性 ConfigurationProperties指定前后缀来读取
如果很多属性的话，使用VALUE就比较麻烦
* 建立实体类
```
package com.ghgcn.bt.domain;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/**
 * 
* @ClassName: Book
* @Description: @Component 表示是Spring的组件
* @ConfigurationProperties 加载固定的application.properties属性文件
* 
* @author 刘楠
* @date 2017-3-1 下午3:03:46
*
 */
@Component
@ConfigurationProperties(prefix="book")
public class Book {

	
	private String author;
	
	private String name;
	
	private Double price;

	public String getAuthor() {
		return author;
	}

	public void setAuthor(String author) {
		this.author = author;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public Double getPrice() {
		return price;
	}

	public void setPrice(Double price) {
		this.price = price;
	}

	@Override
	public String toString() {
		return "Book [author=" + author + ", name=" + name + ", price=" + price + "]";
	}
	
	
}

```

* 注入Controller
```
@RestController
public class IndexController {

	@Autowired
	private Book book;
	
	@RequestMapping(value="/index",method=RequestMethod.GET)
	public String index(HttpServletRequest request){
		return "Hello Spring boot"+book.toString();
		
	}
	
	
```


访问http://localhost:8080/index
```
Hello Spring bootBook [author=john, name=Spring boot入门自定义属性, price=18.3]
```

#### 其它指定的properties
```
@Component
@ConfigurationProperties(prefix="book")
@PropertySource(value={"classpath:config/author.properties"})
public class Book {

	
	private String author;
	
	private String name;
	
	private Double price;
```
内容如上
这里要注意哦，有一个问题，如果你使用的是1.5以前的版本，那么可以通过locations指定properties文件的位置，这样：
@ConfigurationProperties(prefix = "config2",locations="classpath:config/author.properties")
但是1.5版本后就没有这个属性了，找了半天发现添加@Configuration和@PropertySource(“classpath:test.properties”)后才可以读取。


访问http://localhost:8080/index
```
Hello Spring bootBook [author=john, name=Spring boot入门自定义属性, price=18.3]
```

####配置随机值
rd.properties
```
dudu.secret=${random.value}
dudu.numb=${random.int}
dudu.bignumber=${random.long}
dudu.uuid=${random.uuid}
dudu.number.less.than.ten=${random.int(10)}
dudu.num.in.range=${random.int[1024,65536]}

```
类
```
@ConfigurationProperties(prefix="dudu")
@PropertySource(value={"classpath:config/rd.properties"})
@Component
public class Dudu {
	private String secret;
	private int numb;
	private long bignumber;
	private String uuid;
	private boolean number;
	private int num;
```

