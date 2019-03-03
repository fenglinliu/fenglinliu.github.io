---
tags: [Spring Boot]
---
## <center>Spring Boot 基础</center>

#### 使用eclipse工具搭建一个简单的Spring Boot

##### 1.配置java 运行环境

1.1 配置jdk环境

1.2配置环境变量

创建JAVA_HOME变量，值为jdk路径；创建CLASSPATH变量，填值有两种方式，（1）使用%JAVA_HOME%\lib（2）使用jdk安装路径\lib；配置path变量，添值也有两种方式，（1）使用%JAVA_HOME%\bin（2）使用jdk安装路径\bin

##### 2.配置maven

2.1配置maven环境变量

下载maven解压，创建MAVEN_HOME,值为maven的解压路径；

配置path变量，同上。

检查是否配置成功，在控制台输入mvn -v命令

##### 3.下载STS插件，更快速的开发

基本的运行环境搭建完成，搭建Spring boot 的项目。Eclipse 提供了Spring Tool  Suite(STS) 插件，使用插件可以更快速的开发。

打开eclipse ,help->eclipse Marketplace->选择Popular->选择STS->Installed,下载插件。

下载完成，可以再new->project 看到spring目录，选中Spring starter project->next就可以

##### 4.创建Spring boot

只需编写pom.xml 和一个 java 类

