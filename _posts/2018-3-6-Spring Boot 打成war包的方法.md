---
tags: [Spring Boot]
---

## </center>Spring Boot 打成war包的方法</center>

Spring Boot写的项目，自身嵌入了tomcat，所以可以直接运行jar包。但是，每次启动jar包创建的都是新的tomcat，这回造成上传文件丢失等问题。因此，我们需要将项目打成war包，部署到tomcat上。

1. **修改pom.xml中的jar为war**

```xml
<groupId>cn.bookcycle.panda</groupId>
<artifactId>panda-payservice</artifactId>
<version>0.0.1-SNAPSHOT</version>
<packaging>jar</packaging>
```

修改为：

```xml
<groupId>cn.bookcycle.panda</groupId>
<artifactId>panda-payservice</artifactId>
<version>0.0.1-SNAPSHOT</version>
<packaging>war</packaging>
```

2. **在pom.xml中添加打war包的maven插件和设置打包的时候跳过单元测试代码**

```xml
<plugin>
	<artifactId>maven-war-plugin</artifactId>
		<configuration>
			<!--如果想在没有web.xml文件的情况下构建WAR，请设置为false-->
			<failOnMissingWebXml>false</failOnMissingWebXml>
			<!--设置war包的名字-->
			<warName>checkroom</warName> 
	    </configuration>
</plugin>

 <!-- 让打包的时候跳过测试代码 -->
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
	<artifactId>maven-surefire-plugin</artifactId>
	<configuration>
		<skip>true</skip>
	</configuration>
</plugin>  
```

3. **在pom.xml中添加servlet包**

```xml
<dependency>  
	<groupId>javax.servlet</groupId>  
	<artifactId>javax.servlet-api</artifactId>  
</dependency>
```

4. **排除Spring Boot内置的tomcat**

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

修改为

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
	<exclusions>
		<exclusion>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-tomcat</artifactId>
		</exclusion>
	</exclusions>  
</dependency>
```

5.**在main方法所属的类的同级包下，新建SpringBootStartApplication类**

```java
public class SpringBootStartApplication extends SpringBootServletInitializer {

	@Override
	protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
		// 注意的Application是启动类，就是main方法所属的类
		return builder.sources(Application.class);
	}
```

6. **通过eclipse开始打war包**

项目右键——》Run As——》Maven Build...——》找到Goals栏，写命令clean package ——》Run

7. **访问项目**

最后，在项所在的文件夹文件夹里找target/***.war

将war包拷贝到Tomcat的webapps下，然后启动Tomcat

访问路径是：http://localhost:端口号/war包名/@RequestMapping中的value值

 **注意：**

* 端口号是Tomcat的端口号，不是Spring Boot中配置的项目端口号

* 如果打包过程中遇到

  [WARNING] The requested profile "pom.xml" could not be activated because it does not exist.

  可以在打包的时候，清空Goals栏下面的Profiles栏的内容