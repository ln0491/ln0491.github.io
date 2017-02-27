---
title: mybatis入门三
date: 2017-02-27 16:18:55
categories: mybatis
tags: mybatis
---


### 实现关联数据的查询
#### 建立表
```

CREATE TABLE article(
id INT(11) PRIMARY KEY AUTO_INCREMENT,
userid INT(11) NOT NULL,
title VARCHAR(100) NOT NULL,
content TEXT NOT NULL

);

INSERT INTO article VALUES (NULL, 1, 'test_title', 'test_content');
INSERT INTO article VALUES (NULL, 2, 'test_title_2', 'test_content_2');
INSERT INTO article VALUES (NULL, 3, 'test_title_3', 'test_content_3');
INSERT INTO article VALUES (NULL, 1, 'test_title_4', 'test_content_4');
```

#### 建立对应的JAVA类

```
package com.ghg.md.domain;

public class Article {

	private int id;
	//用户对应userID
	private UserDo userDo;
	private String titile;
	private String content;

	private Article() {
		super();
		// TODO Auto-generated constructor stub
	}

	private Article(int id, UserDo userDo, String titile, String content) {
		super();
		this.id = id;
		this.userDo = userDo;
		this.titile = titile;
		this.content = content;
	}

	public UserDo getUserDo() {
		return userDo;
	}

	public void setUserDo(UserDo userDo) {
		this.userDo = userDo;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getTitile() {
		return titile;
	}

	public void setTitile(String titile) {
		this.titile = titile;
	}

	public String getContent() {
		return content;
	}

	public void setContent(String content) {
		this.content = content;
	}

}

```

#### 建立mapper.xml association
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.ghg.md.dao.ArticleMapper">

	<!--UserDo 联合文章进行查询 方法之一的配置 (多对一的方式) -->


	<resultMap type="com.ghg.md.domain.UserDo" id="userDo">
		<id column="uid" property="id" />
		<result column="username" property="userName" />
		<result column="userage" property="userAge" />
		<result column="useraddress" property="userAddress" />
	</resultMap>

	<resultMap type="com.ghg.md.domain.Article" id="article">
		<id column="id" property="id" />
		<result column="title" property="title" />
		<result column="content" property="content" />
		<!--一对多 -->
		<association property="userDo" javaType="com.ghg.md.domain.UserDo" resultMap="userDo" >
		<!--此处也有ID，需要使用别名，对应sql中的 uid  -->
		<!-- 	<id column="uid" property="id" />
			<result column="username" property="userName" />
			<result column="userage" property="userAge" />
			<result column="useraddress" property="userAddress" /> -->
		</association>

	</resultMap>


	<select id="getUserArticles" parameterType="int" resultMap="article">
		SELECT
		user.id uid,user.userName,user.userAddress,article.id,article.title,article.content
		FROM USER,article
		WHERE user.id=article.userid AND user.id=#{id}
	</select>


</mapper>

```

mapper接口
```
public interface ArticleMapper {

	
	public List<Article> getUserArticles(@Param("id") int id);
}

```
测试
```

	private static SqlSessionFactory sqlSessionFactory;
	private static Reader reader;
	private static Logger logger = LoggerFactory.getLogger(Test.class);

	public static void main(String[] args) {

		try {
			reader = Resources.getResourceAsReader("Configuration.xml");
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
		} catch (IOException e) {
			e.printStackTrace();
		}

		SqlSession session = sqlSessionFactory.openSession();
		
		ArticleMapper articleMapper = session.getMapper(ArticleMapper.class);
		
		List<Article> articles = articleMapper.getUserArticles(1);
		
		for(Article a:articles){
		logger.debug("用户内容:{}\n",a);
		}
	}

```

#### 复杂关系一对一与一对多
```
/*
SQLyog Enterprise v12.09 (64 bit)
MySQL - 5.6.27-log : Database - mybatis
*********************************************************************
*/


/*!40101 SET NAMES utf8 */;

/*!40101 SET SQL_MODE=''*/;

/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
CREATE DATABASE /*!32312 IF NOT EXISTS*/`mybatis` /*!40100 DEFAULT CHARACTER SET utf8 */;

USE `mybatis`;

/*Table structure for table `author` */

DROP TABLE IF EXISTS `author`;

CREATE TABLE `author` (
  `author_id` INT(11) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '作者ID主键',
  `author_username` VARCHAR(30) NOT NULL COMMENT '作者用户名',
  `author_password` VARCHAR(32) NOT NULL COMMENT '作者密码',
  `author_email` VARCHAR(50) NOT NULL COMMENT '作者邮箱',
  `author_bio` VARCHAR(1000) DEFAULT '这家伙很赖，什么也没留下' COMMENT '作者简介',
  `register_time` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '注册时间',
  PRIMARY KEY (`author_id`)
) ENGINE=INNODB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;

/*Data for the table `author` */

INSERT INTO `author`(`author_id`,`author_username`,`author_password`,`author_email`,`author_bio`,`register_time`) VALUES (1,'张三','123456','123@qq.com','张三是个新手，刚开始注册','2015-10-29 10:23:59'),(2,'李四','123asf','lisi@163.com','魂牵梦萦 ','2015-10-29 10:24:29'),(3,'王五','dfsd342','ww@sina.com','康熙王朝','2015-10-29 10:25:23'),(4,'赵六','123098sdfa','zhaoliu@qq.com','花午骨','2015-10-29 10:26:09'),(5,'钱七','zxasqw','qianqi@qq.com','这家伙很赖，什么也没留下','2015-10-29 10:27:04'),(6,'张三丰','123456','zhangsf@qq.com','这家伙很赖，什么也没留下','2015-10-29 11:48:00'),(7,'张无忌','qwertyuiop','wuji@163.com','这家伙很赖，什么也没留下','2015-10-29 11:48:24');

/*Table structure for table `blog` */

DROP TABLE IF EXISTS `blog`;

CREATE TABLE `blog` (
  `blog_id` INT(11) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT 'BlogId主键',
  `blog_title` VARCHAR(255) NOT NULL COMMENT 'blog标题',
  `author_id` INT(11) UNSIGNED NOT NULL COMMENT '作者ID外键',
  PRIMARY KEY (`blog_id`),
  KEY `fk_author_id` (`author_id`),
  CONSTRAINT `fk_author_id` FOREIGN KEY (`author_id`) REFERENCES `author` (`author_id`)
) ENGINE=INNODB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8;

/*Data for the table `blog` */

INSERT  INTO `blog`(`blog_id`,`blog_title`,`author_id`) VALUES (1,'小张的Blog',1),(2,'小李',2),(3,'王五不是人',3),(4,'赵地人',4),(5,'钱钱钱',5);

/*Table structure for table `posts` */

DROP TABLE IF EXISTS `posts`;

CREATE TABLE `posts` (
  `post_id` INT(11) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '文章主键ID',
  `post_subject` VARCHAR(255) NOT NULL COMMENT '文章主题，标题',
  `post_body` TEXT NOT NULL COMMENT '文章内容最大3000个字符',
  `blog_id` INT(11) UNSIGNED NOT NULL COMMENT 'Blog主键做外键',
  `createtime` DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '文章创建时间',
  PRIMARY KEY (`post_id`),
  KEY `fk_blog_id` (`blog_id`),
  CONSTRAINT `fk_blog_id` FOREIGN KEY (`blog_id`) REFERENCES `blog` (`blog_id`)
) ENGINE=INNODB AUTO_INCREMENT=8 DEFAULT CHARSET=utf8mb4;

/*Data for the table `posts` */

INSERT  INTO `posts`(`post_id`,`post_subject`,`post_body`,`blog_id`,`createtime`) VALUES (1,'Mybatis入门一','什么是 MyBatis ？\r\nMyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以对配置和原生Map使用简单的 XML 或注解，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。',1,'2015-10-29 10:32:21'),(2,'Mybatis入门二','要使用 MyBatis， 只需将 mybatis-x.x.x.jar 文件置于 classpath 中即可。',1,'2015-10-29 10:32:52'),(3,'Oracle学习','Oracle Database，又名Oracle RDBMS，或简称Oracle。是甲骨文公司的一款关系数据库管理系统',2,'2015-10-29 10:33:26'),(4,'JAVA学习一','Java是由Sun Microsystems公司于1995年5月推出的Java面向对象程序设计语言和Java平台的总称',3,'2015-10-29 10:34:17'),(5,'PL/SQL','PL/SQL也是一种程序语言，叫做过程化SQL语言（Procedural Language/SQL）。PL/SQL是Oracle数据库对SQL语句的扩展',4,'2015-10-29 10:37:52'),(6,'CSS标签选择器','标签选择器\r\nID选择器\r\n类选择器\r\n特殊选择器',5,'2015-10-29 10:39:44'),(7,'javascript','js:是前端脚本语言',2,'2015-10-29 10:40:18');

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;
```


#### 建立实体类
Author
```
package com.ghg.md.domain;

import java.util.Date;

public class Author {
	// 作者ID
	private int authorId;

	// 作者用户名
	private String authorUserName;

	// 作者密码
	private String authorPassword;

	// 作者邮箱
	private String authorEmail;

	// 作者介绍
	private String authorBio;

	// 注册时间
	private Date registerTime;

	public int getAuthorId() {
		return authorId;
	}

	public void setAuthorId(int authorId) {
		this.authorId = authorId;
	}

	public String getAuthorUserName() {
		return authorUserName;
	}

	public void setAuthorUserName(String authorUserName) {
		this.authorUserName = authorUserName;
	}

	public String getAuthorPassword() {
		return authorPassword;
	}

	public void setAuthorPassword(String authorPassword) {
		this.authorPassword = authorPassword;
	}

	public String getAuthorEmail() {
		return authorEmail;
	}

	public void setAuthorEmail(String authorEmail) {
		this.authorEmail = authorEmail;
	}

	public String getAuthorBio() {
		return authorBio;
	}

	public void setAuthorBio(String authorBio) {
		this.authorBio = authorBio;
	}

	public Date getRegisterTime() {
		return registerTime;
	}

	public void setRegisterTime(Date registerTime) {
		this.registerTime = registerTime;
	}

	private Author() {
		super();
		// TODO Auto-generated constructor stub
	}

	private Author(int authorId, String authorUserName, String authorPassword, String authorEmail, String authorBio,
			Date registerTime) {
		super();
		this.authorId = authorId;
		this.authorUserName = authorUserName;
		this.authorPassword = authorPassword;
		this.authorEmail = authorEmail;
		this.authorBio = authorBio;
		this.registerTime = registerTime;
	}

	@Override
	public String toString() {
		return "Author [authorId=" + authorId + ", authorUserName=" + authorUserName + ", authorPassword="
				+ authorPassword + ", authorEmail=" + authorEmail + ", authorBio=" + authorBio + ", registerTime="
				+ registerTime + "]";
	}

}

```
Blog 
```
package com.ghg.md.domain;

public class Blog {
	//博客ID
    private int blogId;
    
    //标题
    private String blogTitle;
    
    //博客作者
    private Author author;

	private Blog() {
		super();
		// TODO Auto-generated constructor stub
	}

	public int getBlogId() {
		return blogId;
	}

	public void setBlogId(int blogId) {
		this.blogId = blogId;
	}

	public String getBlogTitle() {
		return blogTitle;
	}

	public void setBlogTitle(String blogTitle) {
		this.blogTitle = blogTitle;
	}

	public Author getAuthor() {
		return author;
	}

	public void setAuthor(Author author) {
		this.author = author;
	}

	@Override
	public String toString() {
		return "Blog [blogId=" + blogId + ", blogTitle=" + blogTitle + ", author=" + author + "]";
	}
    
    
}

```

#### 一对一
```
public interface BlogMapper {

	public Blog findBlogById(@Param("blogId")int blogId);
	
	
	public List<Blog> findBlogByName(@Param("blogTitle") String blogTitle);
}

```
建立mapper.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ghg.md.dao.BlogMapper">

	<!--使用resultMap映射 type使用别名，单独使用Author关联 -->
	<resultMap type="Author" id="authorResultMap">
		<!--主键 -->
		<id property="authorId" column="author_id" />
		<!--普通属性与表中的字段对应 -->
		<result property="authorUserName" column="author_username" />
		<result property="authorPassword" column="author_password" />
		<result property="authorEmail" column="author_email" />
		<result property="authorBio" column="author_bio" />
		<result property="registerTime" column="register_time" />
	</resultMap>

	<!--使用ResultMap -->
	<resultMap type="Blog" id="blogResultMap">
		<!--主键 -->
		<id property="blogId" column="blog_id" />
		<!--标题 -->
		<result property="blogTitle" column="blog_title" />
		<!--一对一关联 第一种 -->
		<association property="author" resultMap="authorResultMap" />
	</resultMap>

	<!-- 根据ID查询 -->
	<select id="findBlogById" parameterType="int" resultMap="blogResultMap">
		SELECT
		blog.blog_id,blog.blog_title,author.author_id,author.author_username,author.author_password,author.author_email,author.author_bio,author.register_time
		FROM blog,author
		WHERE blog.author_id=author.author_id
		AND blog.blog_id=#{blogId}
	</select>
	<!-- 根据名字查询 -->
	<select id="findBlogByName" parameterType="String" resultMap="blogResultMap">
		SELECT
		blog.blog_id,blog.blog_title,author.author_id,author.author_username,author.author_password,author.author_email,author.author_bio,author.register_time
		FROM blog,author
		WHERE blog.author_id=author.author_id
		AND blog_title LIKE "%"#{blogTitle}"%"
	</select>

</mapper>
```
测试
```
	private static SqlSessionFactory sqlSessionFactory;
	private static Reader reader;
	private static Logger logger = LoggerFactory.getLogger(Test.class);

	public static void main(String[] args) {

		try {
			reader = Resources.getResourceAsReader("Configuration.xml");
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
		} catch (IOException e) {
			e.printStackTrace();
		}

		SqlSession session = sqlSessionFactory.openSession();
		
		BlogMapper blogMapper = session.getMapper(BlogMapper.class);
		
		Blog blog = blogMapper.findBlogById(1);
		
		
		
		List<Blog> list = blogMapper.findBlogByName("小张");
		
		for(Blog b: list){
			logger.debug("id查询Blog  {}",b);
		}
		
		
```
### 一对多
#### Posts


```
package com.ghg.md.domain;

import java.util.Date;

public class Posts {
	// 文章ID
	private int postId;

	// 文件主题
	private String postSubject;

	// 主体内容
	private String postBody;
	// 文章建立时间
	private Date createTime;

	private Posts() {
		super();
		// TODO Auto-generated constructor stub
	}

	public int getPostId() {
		return postId;
	}

	public void setPostId(int postId) {
		this.postId = postId;
	}

	public String getPostSubject() {
		return postSubject;
	}

	public void setPostSubject(String postSubject) {
		this.postSubject = postSubject;
	}

	public String getPostBody() {
		return postBody;
	}

	public void setPostBody(String postBody) {
		this.postBody = postBody;
	}

	public Date getCreateTime() {
		return createTime;
	}

	public void setCreateTime(Date createTime) {
		this.createTime = createTime;
	}

}

```
#### 修改blog类
```
 //博客ID
    private int blogId;
    
    //标题
    private String blogTitle;
    
    //博客作者
    private Author author;
    
    //文章List
    private List<Posts> posts;
....
```
#### 修改blogmapper.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ghg.md.dao.BlogMapper">

	<!--使用resultMap映射 type使用别名，单独使用Author关联 -->
	<resultMap type="Author" id="authorResultMap">
		<!--主键 -->
		<id property="authorId" column="author_id" />
		<!--普通属性与表中的字段对应 -->
		<result property="authorUserName" column="author_username" />
		<result property="authorPassword" column="author_password" />
		<result property="authorEmail" column="author_email" />
		<result property="authorBio" column="author_bio" />
		<result property="registerTime" column="register_time" />
	</resultMap>
	<!--文章Map -->
	<resultMap type="Posts" id="postsResultMap">
		<id property="postId" column="post_id" />
		<result property="postSubject" column="post_subject" />
		<result property="postBody" column="post_body" />
		<result property="createTime" column="createtime" />
	</resultMap>

	<!--使用ResultMap -->
	<resultMap type="Blog" id="blogResultMap">
		<!--主键 -->
		<id property="blogId" column="blog_id" />
		<!--标题 -->
		<result property="blogTitle" column="blog_title" />
		<!--一对一关联 第一种 -->
		<association property="author" resultMap="authorResultMap" />

		<!--一对多关联 -->
		<collection property="posts" resultMap="postsResultMap"
			ofType="Posts" />
	</resultMap>



	<!-- 根据ID查询 -->
	<select id="findBlogById" parameterType="int" resultMap="blogResultMap">
		SELECT blog.blog_id,blog.blog_title,
		author.author_id,author.author_username,author.author_password,author.author_email,author.author_bio,author.register_time,
		posts.post_id,posts.post_subject,posts.post_body,posts.createtime,posts.blog_id
		FROM blog,author,posts
		WHERE blog.author_id=author.author_id
		AND blog.blog_id=posts.blog_id
		AND blog.blog_id=#{blogId}
	</select>
	<!-- 根据名字查询 -->
	<select id="findBlogByName" parameterType="String" resultMap="blogResultMap">
		SELECT
		blog.blog_id,blog.blog_title,author.author_id,author.author_username,author.author_password,author.author_email,author.author_bio,author.register_time
		FROM blog,author
		WHERE blog.author_id=author.author_id
		AND blog_title
		LIKE "%"#{blogTitle}"%"
	</select>

</mapper>
```
测试
```
BlogMapper blogMapper = session.getMapper(BlogMapper.class);
		
		Blog blog = blogMapper.findBlogById(1);
		logger.debug("id查询Blog  {}",blog);
		
```
结果

```
17:57:28.950 [main] DEBUG com.ghg.md.test.Test- id查询Blog  Blog [blogId=1, blogTitle=小张的Blog, author=Author [authorId=1, authorUserName=张三, authorPassword=123456, authorEmail=123@qq.com, authorBio=张三是个新手，刚开始注册, registerTime=Thu Oct 29 10:23:59 CST 2015], posts=[Posts [postId=1, postSubject=Mybatis入门一, postBody=什么是 MyBatis ？
MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以对配置和原生Map使用简单的 XML 或注解，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。, createTime=Thu Oct 29 10:32:21 CST 2015], Posts [postId=2, postSubject=Mybatis入门二, postBody=要使用 MyBatis， 只需将 mybatis-x.x.x.jar 文件置于 classpath 中即可。, createTime=Thu Oct 29 10:32:52 CST 2015]]]
```