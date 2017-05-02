---
layout: post
title: MyBatis-Generator-Start
data: 2017/5/2  
categories: MyBatis

---

&emsp;**Mybatis Generator（MBG）是一个Mybaits的代码生成器，可以自动生成Mybatis所需的各种文件，包括匹配表结构的Java POJO、MyBatis/iBATIS 兼容 SQL 映射 XML 文件、对应的Mapper接口等。**


##开始使用MybatisGenerator

1. ###环境配置

	- **开发环境：**
	eclipse4.1
	
	- **依赖：**
		- [mybatis-3.4.4.jar](https://github.com/mybatis/mybatis-3/releases)
		- [mybatis-generator-core-1.3.5.jar](https://github.com/mybatis/generator/releases)
		- [mysql-connector-java-5.1.41-bin.jar](https://www.mysql.com/products/connector/)
	<br>
	- **使用Maven构建：**
		- 传统方式（Java代码）<br>
	```xml
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.41</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.4.4</version>
		</dependency>
		<dependency>
			<groupId>org.mybatis.generator</groupId>
			<artifactId>mybatis-generator-maven-plugin</artifactId>
			<version>1.3.0</version>
		<dependency>
	```
		- Maven插件方式<br>
	```xml
		<dependencies>
			<dependency>
				<groupId>mysql</groupId>
				<artifactId>mysql-connector-java</artifactId>
				<version>5.1.41</version>
			</dependency>
			<dependency>
				<groupId>org.mybatis</groupId>
				<artifactId>mybatis</artifactId>
				<version>3.4.4</version>
			</dependency>
		</dependencies>
		<build>
			<pluginManagement>
				<plugins>
					<plugin>
						<groupId>org.mybatis.generator</groupId>
						<artifactId>mybatis-generator-maven-plugin</artifactId>
						<version>1.3.5</version>
						<configuration>
							<verbose>true</verbose>		<-- 是否输出生成信息 -->
							<overwrite>true</overwrite>	<-- 是否覆盖 -->
						</configuration>
						<dependencies>
							<dependency>
								<groupId>mysql</groupId>
								<artifactId>mysql-connector-java</artifactId>
								<version>5.1.41</version>
							</dependency>
						</dependencies>
					</plugin>
				</plugins>
			</pluginManagement>
		</build>
	```

2.  ###MyBatis Generator 配置文件
	generatorConfig.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
  PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
  "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>

	<context id="DB2Tables" targetRuntime="MyBatis3">
		<--  配置插件（例如第一个插件会为生成的每个POJO对象添加一个toString方法），非必须  -->
		<plugin type="org.mybatis.generator.plugins.ToStringPlugin"></plugin>
		<plugin type="org.mybatis.generator.plugins.EqualsHashCodePlugin"></plugin>
		<plugin type="org.mybatis.generator.plugins.SerializablePlugin"></plugin>
		<plugin type="org.mybatis.generator.plugins.MapperConfigPlugin"></plugin>
		
		<--  数据库配置信息  -->
		<jdbcConnection driverClass="com.mysql.jdbc.Driver"
			connectionURL="jdbc:mysql://localhost:3306/mybatis" userId="root" password="imseven">
		</jdbcConnection>
		
		<--  POJO对象生成器配置，指定了生成路径和包，注意这里会将targetPackage放入targetProject指定的路径下（eclipse中）  -->
		<javaModelGenerator targetPackage="com.mybatis.model"
			targetProject="src">
			<property name="enableSubPackages" value="true" />
			<property name="trimStrings" value="true" />
		</javaModelGenerator>
		
		<--  映射器生成器，指定生成路径和包  -->
		<sqlMapGenerator targetPackage="com.mybatis.mapper"
			targetProject="src">
			<property name="enableSubPackages" value="true" />
		</sqlMapGenerator>

		<--  DAO对象（mapper接口）生成器，指定生成路径和包  -->
		<javaClientGenerator type="XMLMAPPER"
			targetPackage="com.mybatis.dao" targetProject="src">
			<property name="enableSubPackages" value="true" />
		</javaClientGenerator>

		<--  用于选择数据库中的表，生成对应的映射器、POJO、DAO  -->
		<table tableName="t_role" domainObjectName="Role">
			<generatedKey column="id" sqlStatement="MySql" identity="true" />
		</table>
		<table tableName="patent" domainObjectName="Patent"></table>

	</context>
</generatorConfiguration>
```
更多配置信息：[MBG XML配置参考](http://mbg.cndocs.tk/configreference/xmlconfig.html "MBG XML配置参考")
3. ###自动生成
	- **Java代码方式**
	
	```java
	import java.io.InputStream;
	import java.util.ArrayList;
	import java.util.List;
	import org.apache.ibatis.io.Resources;
	import org.mybatis.generator.api.MyBatisGenerator;
	import org.mybatis.generator.config.Configuration;
	import org.mybatis.generator.config.xml.ConfigurationParser;
	import org.mybatis.generator.internal.DefaultShellCallback;
	public class MyGenerator {
	public static void main(String[] args) {
			String resource = "generatorConfig.xml";
			List<String> warnings = new ArrayList<String>();
			boolean overwrite = true;
			InputStream in;
			ConfigurationParser cp = new ConfigurationParser(warnings);
			Configuration config;
			try {
				in = Resources.getResourceAsStream(resource);
				config = cp.parseConfiguration(in);
				DefaultShellCallback callback = new DefaultShellCallback(overwrite);
				MyBatisGenerator myBatisGenerator = new MyBatisGenerator(config, callback, warnings);
				myBatisGenerator.generate(null);
				System.out.println("done");
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	}
	```
	运行此类，即可自动生成Mybatis所需的POJO、DAO、Mapper配置文件，**刷新**项目。
	- **Maven插件生成**
	右键项目->Run As->Maven Build...->打开下图界面
	![构建界面](http://sssssseven.github.io/assets/images/mybatis/learn-day1/generator-maven.png)
	配置，运行，成功之后刷新项目。
	注意，这里需要你的项目为Maven项目才可以。
<br>

4. ###配置mybatis-config.xml
	Mybatis Generator并没有为我们自动生成Mybatis的配置文件[mybatis-config.xml](https://github.com/sssssseven/MyBatis-learn/blob/master/MyBaits-Start/mybatis-config.xml)，需要我们根据生成的各种文件手动配置。

###项目源码
- [Java代码方式生成](https://github.com/sssssseven/MyBatis-learn/tree/master/Mybatis-generator-start/ByJava)
- [Maven插件方式生成](https://github.com/sssssseven/MyBatis-learn/tree/master/Mybatis-generator-start/ByMaven)