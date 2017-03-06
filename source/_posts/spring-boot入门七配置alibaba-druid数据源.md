---
title: spring-boot入门七配置alibaba druid数据源
date: 2017-03-06 17:55:51
categories: spring-boot
tags: spring-boot
---

### spring-boot入门七配置alibaba druid数据源


#### pom.xml依赖

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ghgcn</groupId>
	<artifactId>boot</artifactId>
	<version>1.0</version>
	<packaging>jar</packaging>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.1.RELEASE</version>
	</parent>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<!--spring-jdbc -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-jdbc</artifactId>
		</dependency>
		<!--test -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
		</dependency>

		<!-- mysql -->
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency>
		<!-- 数据源 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.0.14</version>
		</dependency>
		<!--devtools -->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<optional>true</optional>
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

#### Application通用没什么东西
```
package com.ghgcn.boot;


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;


@SpringBootApplication
public class BootApplication {

	private static Logger logger =LoggerFactory.getLogger(BootApplication.class);
	
	
	public static void main(String[] args) {

		SpringApplication.run(BootApplication.class, args);
		
	}

}

```

#### application.properties
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
也可以新建一个配置文件，不一定要用这个jdbc.properties

#### DbConfig

```
package com.ghgcn.boot.config;

import java.sql.SQLException;

import javax.sql.DataSource;


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Component;

import com.alibaba.druid.pool.DruidDataSource;

@Configuration
//@Component
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
    
    //设置模版与数据源
    @Bean
    public JdbcTemplate jdbcTemplate(){
    	JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(dataSource());
        return jdbcTemplate;
    }
}

```
* 第二种
```

@Configuration
@ConfigurationProperties
@PropertySource(value={"classpath:config/jdbc.properties"})
```
#### do
```
package com.ghgcn.boot.business.domain;

import java.util.Date;


public class Author {

	
	private int authorId ;//int(11) unsigned NOT NULL作者ID主键
	private String authorUsername;//varchar(30) NOT NULL作者用户名
	private String authorPassword;//varchar(32) NOT NULL作者密码
	private String authorEmail;//varchar(50) NOT NULL作者邮箱
	private String authorBio;//varchar(1000) NULL作者简介
	private Date  registerTime ;//datetime NULL注册时
	
	
	
	public int getAuthorId() {
		return authorId;
	}
	public void setAuthorId(int authorId) {
		this.authorId = authorId;
	}
	public String getAuthorUsername() {
		return authorUsername;
	}
	public void setAuthorUsername(String authorUsername) {
		this.authorUsername = authorUsername;
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
	@Override
	public String toString() {
		return "Author [authorId=" + authorId + ", authorUsername=" + authorUsername + ", authorPassword="
				+ authorPassword + ", authorEmail=" + authorEmail + ", authorBio=" + authorBio + ", registerTime="
				+ registerTime + "]";
	}
	
	
}

```
#### dao层
```
package com.ghgcn.boot.business.dao;

import java.util.List;

import com.ghgcn.boot.business.domain.Author;

public interface AuthorDao {
	public int add(Author author);

	public int update(Author author);

	public int delete(Long id);

	public Author findAuthor(int id);

	public List<Author> findAuthorList();
}

```
实现
```
package com.ghgcn.boot.business.dao.imp;

import java.util.List;

import javax.annotation.Resource;

import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

import com.ghgcn.boot.business.dao.AuthorDao;
import com.ghgcn.boot.business.domain.Author;

@Repository
public class AuthorDaoImp implements AuthorDao {

	@Resource
	private JdbcTemplate jdbcTemplate;
	// 插入语句
	private final String INSERT_SQL = "INSERT INTO author(author_username,author_password,author_email,author_bio,register_time)VALUES(?,?,?,?,?)";
	// 更新
	private final String UPATE_SQL = "UPDATE author SET author_username= ?,author_password= ? ,author_email=? author_bio=?,register_time=? WHERE author_id=?";

	private final String SELECT_ALL_SQL = "SELECT * FROM author";
	private final String SELECT_ONE_SQL = "SELECT * FROM author WHERE author_id =?";
	private final String DEL_SQL = "DELETE FROM author WHERE author_id=?";


	
	@Override
	public int add(Author author) {
		return jdbcTemplate.update(INSERT_SQL, new Object[] { author.getAuthorUsername(), author.getAuthorPassword(),
				author.getAuthorPassword(), author.getAuthorEmail(), author.getAuthorBio(), author.getRegisterTime() });
	}

	@Override
	public int update(Author author) {
		return jdbcTemplate.update(UPATE_SQL, new Object[] { author.getAuthorUsername(), author.getAuthorPassword(),
				author.getAuthorPassword(), author.getAuthorEmail(), author.getAuthorBio(), author.getRegisterTime(),
				author.getAuthorId() });
	}

	@Override
	public int delete(Long id) {
		return jdbcTemplate.update(DEL_SQL, new Object[] { id });
	}

	@Override
	public Author findAuthor(int id) {
		List<Author> list = jdbcTemplate.query(SELECT_ONE_SQL, new Object[] { id }, new BeanPropertyRowMapper<Author>(Author.class));
		if (null != list && list.size() > 0) {
			Author auhtor = list.get(0);
			return auhtor;
		} else {
			return null;
		}
	}

	@Override
	public List<Author> findAuthorList() {
		
		List<Author> list = jdbcTemplate.query(SELECT_ALL_SQL, new Object[]{}, new BeanPropertyRowMapper<Author>(Author.class));
		return list;
	}

}

```

#### servcie
```
package com.ghgcn.boot.business.service;

import java.util.List;

import com.ghgcn.boot.business.domain.Author;

public interface AuthorServcie {
	public int add(Author author);

	public int update(Author author);

	public int delete(Long id);

	public Author findAuthor(int id);

	public List<Author> findAuthorList();
}

```
实现
```
package com.ghgcn.boot.business.service.impl;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.ghgcn.boot.business.dao.AuthorDao;
import com.ghgcn.boot.business.domain.Author;
import com.ghgcn.boot.business.service.AuthorServcie;

@Service
public class AuthorServcieImpl implements AuthorServcie {
	
	@Autowired
	private AuthorDao authorDao;

	@Override
	public List<Author> findAuthorList() {
		System.out.println("service--->"+authorDao.toString());
		return authorDao.findAuthorList();
	}

	@Override
	public int add(Author author) {
		return authorDao.add(author);
	}

	@Override
	public int update(Author author) {
		return authorDao.update(author);
	}

	@Override
	public int delete(Long id) {
		return authorDao.delete(id);
	}

	@Override
	public Author findAuthor(int id) {
		// TODO Auto-generated method stub
		return authorDao.findAuthor(id);
	}
	
	
	
	
	

}

```

#### controller
```
package com.ghgcn.boot.business.controller;

import java.util.List;

import javax.servlet.http.HttpServletRequest;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import com.ghgcn.boot.business.domain.Author;
import com.ghgcn.boot.business.service.AuthorServcie;

@RestController
public class HomeController {

	@Autowired
	private AuthorServcie authorServcie;
	
	
	@RequestMapping(value="/",method=RequestMethod.GET)
	public List<Author> home(){
		
		return authorServcie.findAuthorList();
	}
	
	@RequestMapping(value="/{id}",method=RequestMethod.GET)
	public Author getAuthor(HttpServletRequest request, @PathVariable("id") int id){
		
		return authorServcie.findAuthor(id);
	}
}

```

#### 测试
```
package com.ghgcn.boot.business.dao;


import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;



@SpringBootApplication
@WebAppConfiguration
@RunWith(SpringJUnit4ClassRunner.class)// 1
public class AuthorDaoTest {
	
	private MockMvc mockMvc;// 2

	@Autowired
	private WebApplicationContext webApplicationContext; // 
	
	private Logger logger = LoggerFactory.getLogger(AuthorDaoTest.class);
	
	
	@Before
	public void before() {
	   mockMvc = MockMvcBuilders.webAppContextSetup(webApplicationContext).build(); // 4
	}
	
	@Test
	public void findAuthorList() throws Exception{
		
		MvcResult mvcResult =mockMvc.perform(MockMvcRequestBuilders.get("/")).andReturn();
		
		MvcResult mvcResult2 =mockMvc.perform(MockMvcRequestBuilders.get("/3")).andReturn();
		
		
		logger.info("mvcResult{}\n",mvcResult);
		logger.info("mvcResult2{}\n",mvcResult2);
		
	}
}

```

源码<https://github.com/ln0491/spring-boot-demo>
