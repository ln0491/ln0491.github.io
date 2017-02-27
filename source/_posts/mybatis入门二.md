---
title: mybatis入门二
date: 2017-02-27 10:12:13
categories: mybatis
tags: mybatis
---

### mybatis入门二 实现数据的增删改查

#### 在mapper.xml中添类与数据库表关系映射
configuration.xml
```
	<!--添加别名 -->
	<typeAliases>
		<!-- <typeAlias alias="UserDo" type="com.ghg.md.domain.UserDo"/> -->
		<!--配置基本包名实体类路径 -->
		<package name="com.ghg.md.domain" />
	</typeAliases>
```
mapper.xml
```
<mapper namespace="com.ghg.md.dao.UserMapper">


	<resultMap type="UserDo" id="userDo">
		<id column="id"  property="id"/>
		<result column="username" property="userName"/>
		<result column="userage" property="userAge"/>
		<result column="useraddress"  property="userAddress"/>
	</resultMap>

	<!-- 根据ID查询用户 -->
	<select id="selectUserByID" parameterType="int"	resultMap="userDo">
		select * from user where id=#{id}
	</select>
	<!-- 查询全部用户信息 -->
	<select id="getAllUser" resultMap="userDo">
		select * from user
	</select>

</mapper>
```
如果不配置
<package name="com.ghg.md.domain" />
那么resultMap type="UserDo" 中type就要写全路径
```
<resultMap type="com.ghg.md.domain.UserDo" id="userDo">
		<id column="id"  property="id"/>
		<result column="username" property="userName"/>
		<result column="userage" property="userAge"/>
		<result column="useraddress"  property="userAddress"/>
	</resultMap>

```

#### 完整的dao mapper
```
package com.ghg.md.dao;

import java.util.List;

import org.apache.ibatis.annotations.Param;

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
	/**
	 * 根据名称模糊查询
	* @Title: selectUsers
	* @Description: TODO(这里用一句话描述这个方法的作用)
	* @param @param username
	* @param @return    设定文件
	* @return List<UserDo>    返回类型
	* @throws
	 */
	public List<UserDo> selectUsers(@Param("username")String username);
	
	
	/**
	 * 添加用户
	* @Title: addUser
	* @Description: TODO(这里用一句话描述这个方法的作用)
	* @param @param userDo    设定文件
	* @return void    返回类型
	* @throws
	 */
	public void addUser(@Param("userDo")UserDo userDo);
	
	
	
	public void updateUser(@Param("userDo")UserDo userDo);
	
	
	public void deleteUser(@Param("id") int id);
	
}

```

##### mapper.xml
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.ghg.md.dao.UserMapper">


	<resultMap type="com.ghg.md.domain.UserDo" id="userDo">
		<id column="id" property="id" />
		<result column="username" property="userName" />
		<result column="userage" property="userAge" />
		<result column="useraddress" property="userAddress" />
	</resultMap>

	<!-- 根据ID查询用户 -->
	<select id="selectUserByID" parameterType="int" resultMap="userDo">
		select * from user where id=#{id}
	</select>
	<!-- 查询全部用户信息 -->
	<select id="getAllUser" resultMap="userDo">
		select * from user
	</select>

	<!-- 模糊查询 -->
	<select id="selectUsers" parameterType="String" resultMap="userDo">
		select * from user
		<where>
			<if test="username != null">
				username LIKE CONCAT('%',#{username},'%')
			</if>
		</where>
	</select>

	<!--添加用户 -->
	<insert id="addUser" parameterType="com.ghg.md.domain.UserDo">
		INSERT INTO
		USER(username,userage,useraddress)
		VALUES(#{userDo.userName},#{userDo.userAge},#{userDo.userAddress});
	</insert>
	<!--更新用户 -->
	<update id="updateUser" parameterType="com.ghg.md.domain.UserDo">
		UPDATE USER SET
		<if test="userDo.userName!=null">
			username= #{userDo.userName},
		</if>
		<if test="userDo.userAge!=null">
			userage=#{userDo.userAge},
		</if>
		<if test="userDo.userAddress!=null">
			useraddress =#{userDo.userAddress}
		</if>
		WHERE id =#{userDo.id}
	</update>
	<!--删除用户 -->
	<delete id="deleteUser" parameterType="int">
		DELETE FROM USER
		<where>
			<if test="id!=null">
				id=#{id}
			</if>
		</where>
	</delete>

</mapper>
```

#### 测试
添加
```
		UserDo insertUserDo = new UserDo();
		insertUserDo.setUserAddress("日本海");
		insertUserDo.setUserAge(32);
		insertUserDo.setUserName("宫本");
		userMapper.addUser(insertUserDo);
		session.commit();
```
修改
```
SqlSession session = sqlSessionFactory.openSession();
		UserMapper userMapper = session.getMapper(UserMapper.class);
		
		
		UserDo userDo = userMapper.selectUserByID(6);
		logger.info("查询一个用户：{}", userDo);
		
		userDo.setUserName("刀个大蛇");
		userDo.setUserAddress("东京");
		userDo.setUserAge(101);
		userMapper.updateUser(userDo);
		session.commit();
```

删除

```
SqlSession session = sqlSessionFactory.openSession();
		UserMapper userMapper = session.getMapper(UserMapper.class);
		
		
		UserDo userDo = userMapper.selectUserByID(6);
		logger.info("查询一个用户：{}", userDo);
		
		 userMapper.deleteUser(6);
	
		session.commit();
```
增加，修改，删除 必须提交事务，否则不会写入到数据库.
模糊查询

```
//模糊查询
		List<UserDo> users = userMapper.selectUsers("j");
```
写法
```
1、mysql ：LIKE CONCAT('%',#{empname},'%' ) 或者 LIKE CONCAT('%',‘${empname}’,'%' )
2、oracle：LIKE '%'||#{empname}||'%'
```
