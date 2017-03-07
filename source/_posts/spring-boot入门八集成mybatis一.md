---
title: spring-boot入门八集成mybatis一
date: 2017-03-07 09:26:04
categories: spring-boot
tags: spring-boot
---

### spring-boot入门八集成mybatis一使用注解

#### pom文件
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.ghgcn</groupId>
	<artifactId>bootandmybatis</artifactId>
	<version>1.0</version>
	<packaging>war</packaging>
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
		<!--spring-mybatis -->
		<dependency>
			<groupId>org.mybatis.spring.boot</groupId>
			<artifactId>mybatis-spring-boot-starter</artifactId>
			<version>1.2.0</version>
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
可以不添加spring-boot-starter-jdbc。因为，mybatis-spring-boot-starter依赖中存在spring-boot-starter-jdbc.
#### 数据源

```
spring.datasource.driver-class-name=com.mysql.jdbc.Driver

spring.datasource.url=jdbc:mysql://localhost:3306/mybatis
spring.datasource.username=root
spring.datasource.password=root


```
#### sql表
```
CREATE TABLE `author` (
  `author_id` int(11) unsigned NOT NULL AUTO_INCREMENT COMMENT '作者ID主键',
  `author_username` varchar(30) NOT NULL COMMENT '作者用户名',
  `author_password` varchar(32) NOT NULL COMMENT '作者密码',
  `author_email` varchar(50) NOT NULL COMMENT '作者邮箱',
  `author_bio` varchar(1000) DEFAULT '这家伙很赖，什么也没留下' COMMENT '作者简介',
  `register_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '注册时间',
  PRIMARY KEY (`author_id`)
) ENGINE=InnoDB AUTO_INCREMENT=16 DEFAULT CHARSET=utf8

```

#### 实体类
```
package com.ghgcn.mb.domain;

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
建立字段和表中的字段一致，不然就要使用别名
####applicatin.properties
```
spring.datasource.url=jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8&useSSL=true
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

#### mapper
```
package com.ghgcn.mb.mapper;

import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;

import com.ghgcn.mb.domain.User;

@Mapper
public interface UserMapper {
	@Select("SELECT * FROM USER WHERE NAME = #{name}")
	public User findByName(@Param("name") String name);

	@Insert("INSERT INTO USER(NAME, AGE) VALUES(#{name}, #{age})")
	public int insert(@Param("name") String name, @Param("age") Integer age);
	@Select("select * from user where id = #{id}")
	public User findUserById(@Param("id") long id);
}

```

这里直接使用@Mapper就可以了
@Mapper就是我们要与mybatis融合关键的一步，只要一个注解就搞定了。

#### 测试
```
package com.ghgcn.mb;

import java.util.Date;
import java.util.List;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.annotation.Rollback;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import com.ghgcn.mb.domain.Author;
import com.ghgcn.mb.mapper.AuthorMapper;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = Application.class)
public class ApplicationTests {

	@Autowired
	private AuthorMapper authorMapper;

	@Test
	@Rollback
	public void Insert() throws Exception {

		Author author = new Author();

		author.setAuthorBio("无可奈何花落去枯萎 ");
		author.setAuthorEmail("qq@qq.com");
		author.setAuthorPassword("test123");
		author.setAuthorUsername("张三");

		authorMapper.add(author);

	}

	@Test
	@Rollback
	public void findById() throws Exception {

		Author author = authorMapper.findAuthor(14);
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

		authorMapper.delete(11);

	}
	
	@Test
	@Rollback
	public void update() throws Exception {

		Author author = authorMapper.findAuthor(3);
		
		author.setAuthorBio("我修改了");
		author.setAuthorEmail("test@test.qq.com");
		author.setAuthorPassword("testtestsetse");
		author.setAuthorUsername("ppppppppppppp");
		author.setRegisterTime(new Date());
		authorMapper.update(author);

	}

}
```
简单集成完成

#### 配置数据源
```
<!-- 数据源 -->
		<dependency>
			<groupId>com.alibaba</groupId>
			<artifactId>druid</artifactId>
			<version>1.0.14</version>
		</dependency>
```
#### 配置类
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
mybatis使用自动使用这个数据源

### 以下使用注解可以不需要
#### 配置sqlSession可选
```
@Configuration
//加上这个注解，使得支持事务
@EnableTransactionManagement
public class MyBatisConfig implements TransactionManagementConfigurer {
  @Autowired
  private DataSource dataSource;

  @Override
  public PlatformTransactionManager annotationDrivenTransactionManager() {
       return new DataSourceTransactionManager(dataSource);
  }

  @Bean(name = "sqlSessionFactory")
  public SqlSessionFactory sqlSessionFactoryBean() {
      SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
      bean.setDataSource(dataSource);

      try {
          return bean.getObject();
      } catch (Exception e) {
          e.printStackTrace();
          throw new RuntimeException(e);
      }
  }

  @Bean
  public SqlSessionTemplate sqlSessionTemplate(SqlSessionFactory sqlSessionFactory) {
      return new SqlSessionTemplate(sqlSessionFactory);
  }
}
```
启动
```
connection  com.alibaba.druid.proxy.jdbc.ConnectionProxyImpl@31ab04ce
```

#### 配置MyBatis配置文件的路径
```
package com.ghgcn.mb.config;

import org.mybatis.spring.mapper.MapperScannerConfigurer;
import org.springframework.boot.autoconfigure.AutoConfigureAfter;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 扫描mybatis的接口
 * 
 * 
 *
 */
@Configuration
// 因为这个对象的扫描，需要在MyBatisConfig的后面注入，所以加上下面的注解
@AutoConfigureAfter(MyBatisConfig.class)
public class MyBatisMapperScannerConfig {
	@Bean
	public MapperScannerConfigurer mapperScannerConfigurer() {
		MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
		// 获取之前注入的beanName为sqlSessionFactory的对象
		mapperScannerConfigurer.setSqlSessionFactoryBeanName("sqlSessionFactory");
		// 指定xml配置文件的路径
		mapperScannerConfigurer.setBasePackage("com.ghgcn.mb.mapper");
		return mapperScannerConfigurer;
	}
}
```
源码<https://github.com/ln0491/spring-boot-mybatis-anno>

参考<http://www.cnblogs.com/zhuxiaojie/p/5836159.html>