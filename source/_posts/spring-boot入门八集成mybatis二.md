---
title: spring-boot入门八集成mybatis二
date: 2017-03-07 09:26:09
categories: spring-boot
tags: spring-boot
---

### spring-boot入门八集成mybatis二使用XML文件

#### pom文件
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ghgcn</groupId>
	<artifactId>mb</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.7</java.version>
	</properties>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.1.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.2.0</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.21</version>
		</dependency>
		<!--devtools -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional>
		</dependency>
		
		<!-- 数据源 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.0.14</version>
		</dependency>
	</dependencies>
	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```
####  application数据源配置

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis
spring.datasource.username=root
spring.datasource.password=root
#连接池的配置信息  
spring.datasource.initialSize=5  
spring.datasource.minIdle=5  
spring.datasource.maxActive=20  
spring.datasource.maxWait=60000  
spring.datasource.timeBetweenEvictionRunsMillis=60000  
spring.datasource.minEvictableIdleTimeMillis=300000  
spring.datasource.validationQuery=SELECT 1 FROM DUAL  
spring.datasource.testWhileIdle=true  
spring.datasource.testOnBorrow=false  
spring.datasource.testOnReturn=false  
spring.datasource.poolPreparedStatements=true  
spring.datasource.maxPoolPreparedStatementPerConnectionSize=20  
spring.datasource.filters=stat,wall,log4j  
spring.datasource.connectionProperties=druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000  
```
#### 数据源配置类
```
package com.ghgcn.mb.config;

import java.sql.SQLException;

import javax.sql.DataSource;


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.transaction.annotation.EnableTransactionManagement;

import com.alibaba.druid.pool.DruidDataSource;


@Configuration
/*@ConfigurationProperties
@PropertySource(value={"classpath:config/jdbc.properties"})*/
@EnableTransactionManagement
public class DruidDBConfig {
	private Logger logger = LoggerFactory.getLogger(DruidDBConfig.class);  
    
    @Value("${spring.datasource.url}")  
    private String dbUrl;  
      
    @Value("${spring.datasource.username}")  
    private String username;  
      
    @Value("${spring.datasource.password}")  
    private String password;  
      
    @Value("${spring.datasource.driver-class-name}")  
    private String driverClassName;  
      
    @Value("${spring.datasource.initialSize}")  
    private int initialSize;  
      
    @Value("${spring.datasource.minIdle}")  
    private int minIdle;  
      
    @Value("${spring.datasource.maxActive}")  
    private int maxActive;  
      
    @Value("${spring.datasource.maxWait}")  
    private int maxWait;  
      
    @Value("${spring.datasource.timeBetweenEvictionRunsMillis}")  
    private int timeBetweenEvictionRunsMillis;  
      
    @Value("${spring.datasource.minEvictableIdleTimeMillis}")  
    private int minEvictableIdleTimeMillis;  
      
    @Value("${spring.datasource.validationQuery}")  
    private String validationQuery;  
      
    @Value("${spring.datasource.testWhileIdle}")  
    private boolean testWhileIdle;  
      
    @Value("${spring.datasource.testOnBorrow}")  
    private boolean testOnBorrow;  
      
    @Value("${spring.datasource.testOnReturn}")  
    private boolean testOnReturn;  
      
    @Value("${spring.datasource.poolPreparedStatements}")  
    private boolean poolPreparedStatements;  
      
    @Value("${spring.datasource.maxPoolPreparedStatementPerConnectionSize}")  
    private int maxPoolPreparedStatementPerConnectionSize;  
      
    @Value("${spring.datasource.filters}")  
    private String filters;  
      
    @Value("{spring.datasource.connectionProperties}")  
    private String connectionProperties;  
      
    @Bean     //声明其为Bean实例  
    @Primary  //在同样的DataSource中，首先使用被标注的DataSource  
    public DataSource dataSource(){  
        DruidDataSource datasource = new DruidDataSource();  
          
        datasource.setUrl(this.dbUrl);  
        datasource.setUsername(username);  
        datasource.setPassword(password);  
        datasource.setDriverClassName(driverClassName);  
          
        //configuration  
        datasource.setInitialSize(initialSize);  
        datasource.setMinIdle(minIdle);  
        datasource.setMaxActive(maxActive);  
        datasource.setMaxWait(maxWait);  
        datasource.setTimeBetweenEvictionRunsMillis(timeBetweenEvictionRunsMillis);  
        datasource.setMinEvictableIdleTimeMillis(minEvictableIdleTimeMillis);  
        datasource.setValidationQuery(validationQuery);  
        datasource.setTestWhileIdle(testWhileIdle);  
        datasource.setTestOnBorrow(testOnBorrow);  
        datasource.setTestOnReturn(testOnReturn);  
        datasource.setPoolPreparedStatements(poolPreparedStatements);  
        datasource.setMaxPoolPreparedStatementPerConnectionSize(maxPoolPreparedStatementPerConnectionSize);  
        try {  
            datasource.setFilters(filters);  
        } catch (SQLException e) {  
            logger.error("druid configuration initialization filter", e);  
        }  
        datasource.setConnectionProperties(connectionProperties);  
          
        return datasource;  
    }  
    
   
}

```
#### 添加Mybaits配置
```
# mybatis
mybatis.config-location=classpath:mybatis-config.xml
#或者
#mybatis.mapper-locations=classpath:com/ghgcn/mbxml/mapper/*Mapper.xml
#类的别名
#mybatis.type-aliases-package=com.ghgcn.mbxml.domain

```



#### mybatis配置
config配置参考
<http://www.mybatis.org/mybatis-3/zh/index.html>


#### mapper普通的接口加上@Mapper注解
```
package com.ghgcn.mbxml.mapper;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import com.ghgcn.mbxml.domain.Author;

@Mapper
public interface AuthorMapper {
	
	public int add(Author author);
	
	public int update(Author author);
	
	
	public int delete(@Param("id")int id);
	
	public Author findAuthor(@Param("id")int id);
	
	public List<Author> findAuthorList();
}

```
#### mapper.xml
参考<<http://www.mybatis.org/mybatis-3/zh/index.html>>
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ghgcn.mbxml.mapper.AuthorMapper">

	<cache />
	<!--使用resultMap映射 type使用别名，单独使用Author关联 -->
	<resultMap type="Author" id="authorResultMap">
		<!--主键 -->
		<id property="authorId" column="author_id" />
		<!--普通属性与表中的字段对应 -->
		<result property="authorUsername" column="author_username" />
		<result property="authorPassword" column="author_password" />
		<result property="authorEmail" column="author_email" />
		<result property="authorBio" column="author_bio" />
		<result property="registerTime" column="register_time" />
	</resultMap>

	<select id="findAuthor" parameterType="int" resultMap="authorResultMap">
		select *
		from author where author_id = #{id}
	</select>

	<select id="findAuthorList" resultMap="authorResultMap">
		select * from author
	</select>

	<!--添加 -->
	<insert id="add" parameterType="Author">
		INSERT INTO
		author(author_username,author_password,author_email,author_bio)
		VALUES(#{authorUsername},#{authorPassword},#{authorEmail},#{authorBio})
	</insert>


	<update id="update" parameterType="Author">
		UPDATE author SET
		author_username=
		#{authorUsername},author_password=#{authorPassword}
		,author_email=#{authorEmail}
		,author_bio=#{authorBio},register_time=#{registerTime}
		WHERE author_id=#{authorId}
	</update>

	<delete id="delete" parameterType="int">
		DELETE FROM author WHERE author_id= #{id}
	</delete>
</mapper>
```


#### Application
```
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}

```
#### 测试
```
package com.ghgcn.mbxml;

import java.sql.Connection;
import java.util.Date;
import java.util.List;

import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.ghgcn.mbxml.domain.Author;
import com.ghgcn.mbxml.mapper.AuthorMapper;


@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class)
public class ApplicationTests {

	@Autowired
	private AuthorMapper authorMapper;

	@Test
	@Rollback
	public void Insert() throws Exception {

		Author author = new Author();

		author.setAuthorBio("无可奈何花落去枯萎123 ");
		author.setAuthorEmail("qq@qq.com123");
		author.setAuthorPassword("test123123");
		author.setAuthorUsername("张三123");

		authorMapper.add(author);
	}

	@Test
	@Rollback
	public void findById() throws Exception {

		Author author = authorMapper.findAuthor(1);
		System.out.println(author.toString());

	}

	@Test
	@Rollback
	public void findByAll() throws Exception {

		List<Author> authorList = authorMapper.findAuthorList();
		System.out.println(authorList.toString());

	}

	@Test
	@Rollback
	public void delete() throws Exception {

		authorMapper.delete(20);

	}
	
	@Test
	@Rollback
	public void update() throws Exception {

		Author author = authorMapper.findAuthor(1);
		
		author.setAuthorBio("我修改了");
		author.setAuthorEmail("test@test.qq.com");
		author.setAuthorPassword("testtestsetse");
		author.setAuthorUsername("魂牵梦萦载载");
		author.setRegisterTime(new Date());
		authorMapper.update(author);

	}

}
```
源码<https://github.com/ln0491/spring-boot-mybatis-xml>