---
tags: [Spring Boot]
---
## <center>spring boot项目改造成spring cloud一般过程</center>

#### 1.注册中心的搭建

##### (五号标题)

#### 2.spring boot项目改造（生产者）

##### 2.1引入pom配置

```xml
  <dependencies>
    <!-- 注册中心客户端 -->
    <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
  </dependencies>
```

##### 2.2增加主类的注解

spring boot的启动主类中添加注解`@EnableDiscoveryClient`

```java
@SpringBootApplication
@EnableDiscoveryClient
public class SMSApplication {
	public static void main(String[] args) {
		SpringApplication.run(SMSApplication.class, args);
	}
}
```

##### 2.3修改properties文件

```xml
#增加项目在注册中心的名字
spring.application.name=sms-service
#增加注册中心的地址
eureka.client.serviceUrl.defaultZone=http://localhost:8090/eureka/
```

#### 3.spring boot项目改造（消费者）

#####3.1引入pom配置

```xml
  <dependencies>
    <!-- 注册中心客户端 -->
    <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
    </dependency>
  </dependencies>
```

##### 3.2增加主类的注解

spring boot的启动主类中添加注解`@EnableDiscoveryClient`和创建RestTemplate的Spring Bean实例，并通过`@LoadBalanced`注解开启客户端负载均衡

```java
@EnableDiscoveryClient
@SpringBootApplication
public class ConsumerApplication {
	public static void main(String[] args) {
		SpringApplication.run(ConsumerApplication.class ,args);
	}
	
	@Bean
	@LoadBalanced
	RestTemplate restTemplate() {
		return new RestTemplate();
	}
}
```

##### 3.3创建消费者

```java
@RestController
public class SmsController {
	
	/**
	 * 自动注入在项目主类中开启了客户端负载均衡的restTemplate实例
	 */
	@Autowired
	private RestTemplate restTemplate;
	
	/**
	 * 给接口增加消费者（相当于一个中间代理）
	 * 
	 * @param requestBody
	 * @return
	 */
	@RequestMapping(value = "/sms", method = RequestMethod.POST)
	public String smsConsumer(@RequestBody (required=false) String requestBody) {
		return restTemplate.postForObject("http://sms-service/sms", requestBody, String.class);
	}
}
```

