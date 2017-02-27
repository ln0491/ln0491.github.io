---
title: mybatis入门一
date: 2017-02-27 08:55:47
categories: mybatis
tags: mybatis
---

### mybatis入门一

#### 环境搭建

新建MAVEN普通项目，添加依赖
```
	<properties>
		<mybatis.version>3.4.2</mybatis.version>
		<mysql.version>5.1.34</mysql.version>
		<slf4j.version>1.7.23</slf4j.version>
		<logback.verison>1.2.1</logback.verison>
	</properties>
		<dependencies>
			<!--mybatis -->
			<dependency>
				<groupId>org.mybatis</groupId>
				<artifactId>mybatis</artifactId>
				<version>${mybatis.version}</version>
			</dependency>
			<!--mysql -->
			<dependency>
				<groupId>mysql</groupId>
				<artifactId>mysql-connector-java</artifactId>
				<version>${mysql.version}</version>
			</dependency>
			<!-- slf4j -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${slf4j.version}</version>
		</dependency>
		<!--logback -->
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>${logback.verison}</version>
		</dependency>
	</dependencies>
```
![mybatis1.png](https://ln0491.github.io/img/mybatis1.png)

##### 创建数据库表
```

CREATE TABLE USER (

id INT(11) PRIMARY KEY AUTO_INCREMENT,
username VARCHAR(50) NOT NULL,
userage INT(5) DEFAULT NULL,
useraddress VARCHAR(200) DEFAULT NULL
);

INSERT INTO USER VALUES(NULL,'summer',50,'广州'),(NULL,'jack',25,'深圳'),(NULL,'john',18,'上海');
```

#### 设置mybatis 配置文件:Configuration.xml，在resources目录下

参考<http://www.mybatis.org/mybatis-3/zh/getting-started.html>
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<environments default="development">
		<environment id="development">
			<transactionManager type="JDBC" />

			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.jdbc.Driver" />
				<property name="url" value="jdbc:mysql://127.0.0.1:3306/company" />
				<property name="username" value="root" />
				<property name="password" value="root" />

			</dataSource>
		</environment>
	</environments>
	<mappers>
		<mapper resource="org/mybatis/md/dao/UserMapper.xml" />
	</mappers>
</configuration>
```


#### 建立与数据库对应的 java class,以及映射文件
```
package com.ghg.md.domain;

public class UserDo {
	/**
	 * 用户ID
	 */
	private int id;
	/**
	 * 用户名
	 */
	private String userName;
	/**
	 * 用户年龄
	 */
	private int userAge;
	/**
	 * 用户地址
	 */
	private String userAddress;
	public UserDo() {
		super();
		// TODO Auto-generated constructor stub
	}
	
	public UserDo(int id, String userName, int userAge, String userAddress) {
		super();
		this.id = id;
		this.userName = userName;
		this.userAge = userAge;
		this.userAddress = userAddress;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getUserName() {
		return userName;
	}

	public void setUserName(String userName) {
		this.userName = userName;
	}

	public int getUserAge() {
		return userAge;
	}

	public void setUserAge(int userAge) {
		this.userAge = userAge;
	}

	public String getUserAddress() {
		return userAddress;
	}

	public void setUserAddress(String userAddress) {
		this.userAddress = userAddress;
	}

	public String toString() {
		return "UserDo [id=" + id + ", userName=" + userName + ", userAge=" + userAge + ", userAddress=" + userAddress
				+ "]";
	}

	

}

```
dao接口
```
package com.ghg.md.dao;

import java.util.List;

import com.ghg.md.domain.UserDo;

/**
 * dao
* @ClassName: UserMapper
* @Description: TODO(这里用一句话描述这个类的作用)
* @author 刘楠
* @date 2017-2-27 上午9:25:12
*
 */
public interface UserMapper {

	
	/**
	 * 获取所有用户
	* @Title: getAllUser
	* @Description: TODO(这里用一句话描述这个方法的作用)
	* @param @return    设定文件
	* @return List<UserDo>    返回类型
	* @throws
	 */
	public List<UserDo> getAllUser();
	
	
	
	/**
	 * 根据ID查询用户
	* @Title: selectUserByID
	* @Description: TODO(这里用一句话描述这个方法的作用)
	* @param @param id
	* @param @return    设定文件
	* @return UserDo    返回类型
	* @throws
	 */
	public UserDo selectUserByID(int id);
}

```
配置文件

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ghg.md.dao.UserMapper">


	<!-- 根据ID查询用户 -->
	<select id="selectUserByID" parameterType="int"
		resultType="com.ghg.md.domain.UserDo">
		select * from user where id=#{id}
	</select>
	<!-- 查询全部用户信息 -->
	<select id="getAllUser" resultType="com.ghg.md.domain.UserDo">
		select * from user
	</select>

</mapper>
```
![mybatis2.png](https://ln0491.github.io/img/mybatis2.png)
#### 测试
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

import com.ghg.md.domain.UserDo;

public class Test {

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

		// 查询一个用户
		UserDo userDo = session.selectOne("com.ghg.md.dao.UserMapper.selectUserByID", 1);
		// 查询全部用户
		List<UserDo> list = session.selectList("com.ghg.md.dao.UserMapper.getAllUser");

		logger.info("查询一个用户：{}", userDo);
		logger.info("查询全部用户：{}", list);
		

	}

}


```
结果：

```
10:03:53.622 [main] INFO  com.ghg.md.test.Test- 查询一个用户：UserDo [id=1, userName=summer, userAge=50, userAddress=广州]
10:03:53.625 [main] INFO  com.ghg.md.test.Test- 查询全部用户：[UserDo [id=1, userName=summer, userAge=50, userAddress=广州], UserDo [id=2, userName=jack, userAge=25, userAddress=深圳], UserDo [id=3, userName=john, userAge=18, userAddress=上海]]

```
#### 为userDo添加别名,在configuration.xml

```
<configuration>
<!--添加别名  -->
	<typeAliases> 
        <typeAlias alias="UserDo" type="com.ghg.md.domain.UserDo"/> 
    </typeAliases> 

```

mapper.xml中可以直接使用，不需要全路径
```
<mapper namespace="com.ghg.md.dao.UserMapper">


	<!-- 根据ID查询用户 -->
	<select id="selectUserByID" parameterType="int"
		resultType="UserDo">
		select * from user where id=#{id}
	</select>
	<!-- 查询全部用户信息 -->
	<select id="getAllUser" resultType="UserDo">
		select * from user
	</select>

</mapper>
```

