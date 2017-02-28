---
title: mybatis入门四延时加载、缓存
date: 2017-02-28 09:01:03
categories: mybatis
tags: mybatis
---

### mybatis入门四延时加载、缓存

#### mybatis一级缓存
mybatis，默认开启了一级缓存，sqlSession中默认有一个hashMap

当查询时，先去hashMap中查找，

如果有就直接，取出，不再操作数据库

如果没有就去数据库查找，并放在haspMap中

当做事物时，如添加，删除，修改，后有commit时会清空一级缓存

```
package com.ghg.md.dao;

import org.apache.ibatis.annotations.Param;

import com.ghg.md.domain.Author;

public interface AuthorMapper {
	
	
	public Author findAuthorById(@Param("id") int id);

}

```

mapper.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ghg.md.dao.AuthorMapper">

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
	
	<select id="findAuthorById" parameterType="int" resultMap="authorResultMap">
		select * from author where author_id = #{id}
	</select>
	
</mapper>
```

测试

```
package com.ghg.md.test;

import java.io.IOException;
import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.ghg.md.dao.AuthorMapper;
import com.ghg.md.dao.BlogMapper;
import com.ghg.md.domain.Author;
import com.ghg.md.domain.Blog;

public class Test6 {
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
		
		AuthorMapper authorMapper = session.getMapper(AuthorMapper.class);
		
		
		Author authorById = authorMapper.findAuthorById(1);
		logger.debug("author id -->{}",authorById);
		Author authorById1 = authorMapper.findAuthorById(1);
		logger.debug("author id -->{}",authorById1);
		Author authorById2 = authorMapper.findAuthorById(2);
		logger.debug("author id -->{}",authorById2);

	}

}

```
结果
```
09:08:47.539 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==>  Preparing: select * from author where author_id = ? 
09:08:47.568 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==> Parameters: 1(Integer)
09:08:47.582 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- <==      Total: 1
09:08:47.582 [main] DEBUG com.ghg.md.test.Test- author id -->Author [authorId=1, authorUserName=张三, authorPassword=123456, authorEmail=123@qq.com, authorBio=张三是个新手，刚开始注册, registerTime=Thu Oct 29 10:23:59 CST 2015]
09:08:47.584 [main] DEBUG com.ghg.md.test.Test- author id -->Author [authorId=1, authorUserName=张三, authorPassword=123456, authorEmail=123@qq.com, authorBio=张三是个新手，刚开始注册, registerTime=Thu Oct 29 10:23:59 CST 2015]
09:08:47.584 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==>  Preparing: select * from author where author_id = ? 
09:08:47.584 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==> Parameters: 2(Integer)
09:08:47.585 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- <==      Total: 1
09:08:47.585 [main] DEBUG com.ghg.md.test.Test- author id -->Author [authorId=2, authorUserName=李四, authorPassword=123asf, authorEmail=lisi@163.com, authorBio=魂牵梦萦 , registerTime=Thu Oct 29 10:24:29 CST 2015]

```
同一条记录只查询了一次，不同的才会继续查询

#### 延时加载

##### 看一个没有延时加载的BlogMapper.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ghg.md.dao.BlogMapper">

	<!--使用ResultMap -->
	<resultMap type="Blog" id="blogResultMap">
		<!--主键 -->
		<id property="blogId" column="blog_id" />
		<!--标题 -->
		<result property="blogTitle" column="blog_title" />
		
		<!--关联作者 外键 column：外键 select:引用Authro的namespace.方法 -->
	
		<association property="author" javaType="Author" column="author_id"
			select="com.ghg.md.dao.AuthorMapper.findAuthorById" />

		<!--一对多  ofType属性指定集合中元素的对象类型。-->
		<collection property="posts" javaType="ArrayList" ofType="Posts" column="blog_id" select="com.ghg.md.dao.PostsMapper.findPostsByBlogId" />
	</resultMap>



	<!-- 根据ID查询 -->
	<select id="findBlogById" parameterType="int" resultMap="blogResultMap">
		<!-- SELECT
		blog.blog_id,blog.blog_title,
		author.author_id,author.author_username,author.author_password,author.author_email,author.author_bio,author.register_time,
		posts.post_id,posts.post_subject,posts.post_body,posts.createtime,posts.blog_id
		FROM blog,author,posts
		WHERE blog.author_id=author.author_id
		AND
		blog.blog_id=posts.blog_id
		AND blog.blog_id=#{blogId} -->
		select * from blog where blog_id = #{blogId}
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
package com.ghg.md.test;

import java.io.IOException;
import java.io.Reader;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.ghg.md.dao.BlogMapper;
import com.ghg.md.dao.PostsMapper;
import com.ghg.md.domain.Blog;
import com.ghg.md.domain.Posts;



public class Test6 {
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
		
		Blog blogById = blogMapper.findBlogById(2);
		
		
		logger.debug("Blogid : {},title: {}",blogById.getBlogId(),blogById.getBlogTitle());
		logger.debug("==========开始延时加载====================");
		logger.debug("BlogPost: {}",blogById.getPosts());
		logger.debug("BlogAuthor: {}",blogById.getAuthor());
		

	}

}

```
结果
```
09:23:26.760 [main] DEBUG c.ghg.md.dao.BlogMapper.findBlogById- ==>  Preparing: select * from blog where blog_id = ? 
09:23:26.787 [main] DEBUG c.ghg.md.dao.BlogMapper.findBlogById- ==> Parameters: 2(Integer)
09:23:26.801 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ====>  Preparing: select * from author where author_id = ? 
09:23:26.801 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ====> Parameters: 2(Integer)
09:23:26.803 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- <====      Total: 1
09:23:26.804 [main] DEBUG c.g.m.d.P.findPostsByBlogId- ====>  Preparing: select * from posts where blog_id =? 
09:23:26.804 [main] DEBUG c.g.m.d.P.findPostsByBlogId- ====> Parameters: 2(Integer)
09:23:26.806 [main] DEBUG c.g.m.d.P.findPostsByBlogId- <====      Total: 2
09:23:26.806 [main] DEBUG c.ghg.md.dao.BlogMapper.findBlogById- <==      Total: 1
09:23:26.807 [main] DEBUG com.ghg.md.test.Test- Blogid : 2,title: 小李
09:23:26.808 [main] DEBUG com.ghg.md.test.Test- ==========开始延时加载====================
09:23:26.808 [main] DEBUG com.ghg.md.test.Test- BlogPost: [Posts [postId=3, postSubject=Oracle学习, postBody=Oracle Database，又名Oracle RDBMS，或简称Oracle。是甲骨文公司的一款关系数据库管理系统, createTime=Thu Oct 29 10:33:26 CST 2015], Posts [postId=7, postSubject=javascript, postBody=js:是前端脚本语言, createTime=Thu Oct 29 10:40:18 CST 2015]]
09:23:26.808 [main] DEBUG com.ghg.md.test.Test- BlogAuthor: Author [authorId=2, authorUserName=李四, authorPassword=123asf, authorEmail=lisi@163.com, authorBio=魂牵梦萦 , registerTime=Thu Oct 29 10:24:29 CST 2015]

```

先把所有的有关联的查询都执行过了，也就是一次性全部查出
##### 添加延时加载
configuration.xml
```
<configuration>
	<!--资源文件 -->
	<properties resource="jdbc.properties" />

	<settings>
		<!-- 开启延时加载 默认为false--> 
		<setting name="lazyLoadingEnabled" value="true" />
		<!--关闭立即加载 默认为true -->
		<setting name="aggressiveLazyLoading" value="false" />
	</settings>
```
测试还是上面的方法，查询结果
```
package com.ghg.md.test;

import java.io.IOException;
import java.io.Reader;


import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.ghg.md.dao.BlogMapper;
import com.ghg.md.domain.Blog;




public class Test6 {
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
		
		Blog blogById = blogMapper.findBlogById(2);
		
		
		logger.debug("Blogid : {},title: {}",blogById.getBlogId(),blogById.getBlogTitle());
		logger.debug("==========开始延时加载Posts====================");
		logger.debug("BlogPost: {}",blogById.getPosts());
		logger.debug("==========开始延时加载Author====================");
		logger.debug("BlogAuthor: {}",blogById.getAuthor());
		

	}

}

```
结果
```
09:42:07.696 [main] DEBUG c.ghg.md.dao.BlogMapper.findBlogById- ==>  Preparing: select * from blog where blog_id = ? 
09:42:07.723 [main] DEBUG c.ghg.md.dao.BlogMapper.findBlogById- ==> Parameters: 2(Integer)
09:42:07.770 [main] DEBUG c.ghg.md.dao.BlogMapper.findBlogById- <==      Total: 1
09:42:07.771 [main] DEBUG com.ghg.md.test.Test- Blogid : 2,title: 小李
09:42:07.772 [main] DEBUG com.ghg.md.test.Test- ==========开始延时加载Posts====================
09:42:07.772 [main] DEBUG c.g.m.d.P.findPostsByBlogId- ==>  Preparing: select * from posts where blog_id =? 
09:42:07.773 [main] DEBUG c.g.m.d.P.findPostsByBlogId- ==> Parameters: 2(Integer)
09:42:07.776 [main] DEBUG c.g.m.d.P.findPostsByBlogId- <==      Total: 2
09:42:07.776 [main] DEBUG com.ghg.md.test.Test- BlogPost: [Posts [postId=3, postSubject=Oracle学习, postBody=Oracle Database，又名Oracle RDBMS，或简称Oracle。是甲骨文公司的一款关系数据库管理系统, createTime=Thu Oct 29 10:33:26 CST 2015], Posts [postId=7, postSubject=javascript, postBody=js:是前端脚本语言, createTime=Thu Oct 29 10:40:18 CST 2015]]
09:42:07.776 [main] DEBUG com.ghg.md.test.Test- ==========开始延时加载Author====================
09:42:07.776 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==>  Preparing: select * from author where author_id = ? 
09:42:07.777 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==> Parameters: 2(Integer)
09:42:07.778 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- <==      Total: 1
09:42:07.778 [main] DEBUG com.ghg.md.test.Test- BlogAuthor: Author [authorId=2, authorUserName=李四, authorPassword=123asf, authorEmail=lisi@163.com, authorBio=魂牵梦萦 , registerTime=Thu Oct 29 10:24:29 CST 2015]

```
在需要使得的使用的时候，才去执行查询

#### 二级缓存
##### 开启二级缓存

```
<settings>

		<!-- 开启延时加载 -->
		<setting name="lazyLoadingEnabled" value="true" />
		<!-- 关闭立即加载 -->
		<setting name="aggressiveLazyLoading" value="false" />
		<!--开启二级缓存 -->
		<setting name="cacheEnabled" value="true" />
	</settings>
```


##### 在authorMapper中开启二级缓存

```
<!--开启本mapper下的二级缓冲  -->
<cache />
```
##### mybatis二级缓存与一级缓存
sqlSession是互不影响的，一级缓存

Mapper(namespace)是二级缓存

多个sqlSession可以共享一个Mapper的二级缓存区域

二级缓存按namespace分，其它的mapper也有自己的二级缓存区域namespace

第一个namespace的mapper都有一个二级缓存区域，2个mapper的namespace如果相同，这2个mapper执行sql查询数据将存在相同的二级缓存区域中.
二级缓存是mapper范围级别的，除了在configuration.xml设置二级缓存的总开关，还在在个体的mapper.xml中开启二级缓存
##### 实现序列化
![http://images2015.cnblogs.com/blog/710715/201510/710715-20151031172732575-896798847.png](http://images2015.cnblogs.com/blog/710715/201510/710715-20151031172732575-896798847.png)

##### 测试
```
package com.ghg.md.test;

import java.io.IOException;
import java.io.Reader;


import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.ghg.md.dao.AuthorMapper;
import com.ghg.md.dao.BlogMapper;
import com.ghg.md.domain.Author;
import com.ghg.md.domain.Blog;




public class Test7 {
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

		SqlSession sqlSession1 = sqlSessionFactory.openSession();
        AuthorMapper authorMapper1 = sqlSession1.getMapper(AuthorMapper.class);
        // 第一次查询
        Author author1 = authorMapper1.findAuthorById(2);
        logger.debug("sqlSession1 {}",author1);
        //必须关闭不数据无法写到缓存区域
        sqlSession1.close();

        // 获取会话工厂
        SqlSession sqlSession3 = sqlSessionFactory.openSession();
        AuthorMapper authorMapper3 = sqlSession3.getMapper(AuthorMapper.class);
        // 第一次查询
        Author author3 = authorMapper3.findAuthorById(2);
        logger.debug("author3 {}",author3);
        sqlSession3.close();


	}

}

```

##### 结果
```
09:53:03.059 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==>  Preparing: select * from author where author_id = ? 
09:53:03.087 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==> Parameters: 2(Integer)
09:53:03.101 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- <==      Total: 1
09:53:03.101 [main] DEBUG com.ghg.md.test.Test- sqlSession1 Author [authorId=2, authorUserName=李四, authorPassword=123asf, authorEmail=lisi@163.com, authorBio=魂牵梦萦 , registerTime=Thu Oct 29 10:24:29 CST 2015]
09:53:03.107 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@3457949e]
09:53:03.108 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@3457949e]
09:53:03.108 [main] DEBUG o.a.i.d.pooled.PooledDataSource- Returned connection 878154910 to pool.
09:53:03.109 [main] DEBUG com.ghg.md.dao.AuthorMapper- Cache Hit Ratio [com.ghg.md.dao.AuthorMapper]: 0.5
09:53:03.109 [main] DEBUG com.ghg.md.test.Test- author3 Author [authorId=2, authorUserName=李四, authorPassword=123asf, authorEmail=lisi@163.com, authorBio=魂牵梦萦 , registerTime=Thu Oct 29 10:24:29 CST 2015]

```
中间有这样
```
09:53:03.109 [main] DEBUG com.ghg.md.dao.AuthorMapper- Cache Hit Ratio [com.ghg.md.dao.AuthorMapper]: 0.5
```
缓存
##### 中间增加commit操作
mapper接口
```
package com.ghg.md.dao;

import org.apache.ibatis.annotations.Param;

import com.ghg.md.domain.Author;

public interface AuthorMapper {
	
	
	public Author findAuthorById(@Param("id") int id);

	public int addAuthor(Author author);
}

```
xml
```

	<!--添加 -->
	<insert id="addAuthor" parameterType="Author">
		INSERT INTO author(author_username,author_password,author_email,author_bio)
		VALUES(#{authorUserName},#{authorPassword},#{authorEmail},#{authorBio})
	</insert>
```
测试
```
package com.ghg.md.test;

import java.io.IOException;
import java.io.Reader;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.ghg.md.dao.AuthorMapper;
import com.ghg.md.dao.BlogMapper;
import com.ghg.md.domain.Author;
import com.ghg.md.domain.Blog;

public class Test7 {
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

		SqlSession sqlSession1 = sqlSessionFactory.openSession();
		AuthorMapper authorMapper1 = sqlSession1.getMapper(AuthorMapper.class);
		// 第一次查询
		Author author1 = authorMapper1.findAuthorById(2);
		logger.debug("sqlSession1 {}", author1);
		// 必须关闭不数据无法写到缓存区域
		sqlSession1.close();
		logger.debug("============开始添加========");
		SqlSession sqlSession2 = sqlSessionFactory.openSession();
		AuthorMapper authorMapper2 = sqlSession2.getMapper(AuthorMapper.class);
		// 第一次查询
		Author newAuthor = new Author();
		newAuthor.setAuthorEmail("qq.com@qq.com");
		newAuthor.setAuthorPassword("fdsfds");
		newAuthor.setAuthorUserName("郭靖");
		newAuthor.setAuthorBio("射雕英雄传");

		authorMapper2.addAuthor(newAuthor);

		sqlSession2.commit();
		sqlSession2.close();

		logger.debug("============添加完成========");

		// 获取会话工厂
		SqlSession sqlSession3 = sqlSessionFactory.openSession();
		AuthorMapper authorMapper3 = sqlSession3.getMapper(AuthorMapper.class);
		// 第一次查询
		Author author3 = authorMapper3.findAuthorById(2);
		logger.debug("author3 {}", author3);
		sqlSession3.close();

	}

}

```
结果
```
10:08:51.418 [main] DEBUG c.ghg.md.dao.AuthorMapper.addAuthor- ==>  Preparing: INSERT INTO author(author_username,author_password,author_email,author_bio) VALUES(?,?,?,?) 
10:08:51.419 [main] DEBUG c.ghg.md.dao.AuthorMapper.addAuthor- ==> Parameters: 郭靖(String), fdsfds(String), qq.com@qq.com(String), 射雕英雄传(String)
10:08:51.429 [main] DEBUG c.ghg.md.dao.AuthorMapper.addAuthor- <==    Updates: 1
10:08:51.429 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Committing JDBC Connection [com.mysql.jdbc.JDBC4Connection@2be4d228]
10:08:51.459 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@2be4d228]
10:08:51.459 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@2be4d228]
10:08:51.459 [main] DEBUG o.a.i.d.pooled.PooledDataSource- Returned connection 736416296 to pool.
10:08:51.459 [main] DEBUG com.ghg.md.test.Test- ============添加完成========
10:08:51.460 [main] DEBUG com.ghg.md.dao.AuthorMapper- Cache Hit Ratio [com.ghg.md.dao.AuthorMapper]: 0.0
10:08:51.460 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Opening JDBC Connection
10:08:51.460 [main] DEBUG o.a.i.d.pooled.PooledDataSource- Checked out connection 736416296 from pool.
10:08:51.460 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Setting autocommit to false on JDBC Connection [com.mysql.jdbc.JDBC4Connection@2be4d228]
10:08:51.460 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==>  Preparing: select * from author where author_id = ? 
10:08:51.460 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- ==> Parameters: 2(Integer)
10:08:51.461 [main] DEBUG c.g.m.d.AuthorMapper.findAuthorById- <==      Total: 1
10:08:51.461 [main] DEBUG com.ghg.md.test.Test- author3 Author [authorId=2, authorUserName=李四, authorPassword=123asf, authorEmail=lisi@163.com, authorBio=魂牵梦萦 , registerTime=Thu Oct 29 10:24:29 CST 2015]
10:08:51.461 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Resetting autocommit to true on JDBC Connection [com.mysql.jdbc.JDBC4Connection@2be4d228]
10:08:51.462 [main] DEBUG o.a.i.t.jdbc.JdbcTransaction- Closing JDBC Connection [com.mysql.jdbc.JDBC4Connection@2be4d228]
10:08:51.462 [main] DEBUG o.a.i.d.pooled.PooledDataSource- Returned connection 736416296 to pool.

```

看到中间有commit时，会清空缓存
当做事物时，如添加，删除，修改，后有commit时会清空一级缓存