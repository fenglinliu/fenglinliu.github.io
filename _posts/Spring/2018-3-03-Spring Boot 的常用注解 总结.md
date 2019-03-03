---
tags: [Spring Boot]
---
## <center>Spring Boot 的常用注解 总结</center>

#### @RestController和@RequestMapping注解

4.0重要的一个新的改进是@RestController注解，它继承自@Controller注解。4.0之前的版本，Spring MVC 的组件都使用@Controller来标识当前类是一个控制器servlet。使用这个特性，我们可以开发REST服务的时候不需要使用@Controller而专门的@RestController

当你实现一个RESTful web services 的时候，response将一直通过response body 发送。为了简化开发，Spring 4.0 提供了一个专门版本的controller。

@RequestMapping 注解提供路由信息。它告诉Spring 任何来自“/”路径的http请求都应该被映射到home的方法。@RestController 注解告诉Spring 以字符串的形式渲染结果，并直接返回给调用者。

注：@RestController 和@RequestMapping 注解是Spring MVC 注解（

不是Spring boot 特定部分）

#### @EnableAutoConfigurstion注解

第二个类级别注解是@EnableAutoConfiguration。这个注解告诉Spring Boot根据添加的jar依赖猜测你想如何配置Spring。由于spring-boot-starter添加了 和 Spring MVC ,所以auto-Configuration:设计auto-Configuration的目的是更好的使用“Startervpoms”,但是这两个概念没有直接的联系。你可以自由地挑选stater poms 以外的jar 依赖，并且Spring Boot 将仍旧尽最大努力去自动配置你的应用。

你可以通过将@EnableAutoConfiguration 或@SpringBootApplication 注解添加到一个@Configuration 类上来选择自动配置。

注：你只需要添加一个@EnableAutoConfiguration 注解。我们建议你将它添加到主@Configuration类上。

如果发现应用了你不想要的特定自动配置类，你可以使用@EnableAutoConfiguration 注解的排除属性来禁用他们。

#### @Configuration

Spring Boot 提倡基于Java 的配置。尽管你可以使用一个XML 源来调用SpringApplication.run(),我们通常建议你使用@Configuration 类作为主要来源。一般定义main方法的类也是主要@Configuration的一个很好候选。你不需要将所有的@Configuration 放进一个单独的类。@Import 注解可以用来导入其@Configuration类。

如果你绝对需要使用基于XML的配置，我们建议你仍旧从一个@Configuration 类开始。你可以使用附近的@ImportResource 注解加载XML 配置文件。

@Configuration 注解该类。等价与XML 中配置beans;用@Bean标注方法等价于xml中配置bean

#### @SpringBootApplication

很多Spring Boot 开发者总是使用@Configuration,@EnableAutoConfiguration和@ComponentScan注解他们的main类。由于这些注解被如此频繁的一块使用，Spring Boot 提供一个方法的@SpringBootApplication 选择。该@SpringBootApplication 注解等价于以默认属性使用@Configuration,@EnableAutoConfiguration和@ComponentScan。

#### @Profiles

Spring profiles 提供了一种隔离应用程序配置的方式，并让这些配置只能在特定的环境下生效。任何@Component或@Configuration都能被@Prpfile标记，从而限制加载它的时机。

#### @ResponseBody

表示该方法的返回结果直接写入HTTP response body 中，一般在异步获取数据时使用，在使用@RequestMapping 后，返回值通常解析为跳转路径，而是直接写入HTTP response body 中。比如异步获取json数据，加上@responsebody 后，会直接返回json数据。

#### @Component

泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注。一般公共的方法需用上这个注解

#### @AutoWired

byType 方式。把配置好的Ben拿来用，完成属性、方法的组装，它可以对类成员变量、方法及构造函数进行标注，完成自动装配的工作。当加上（required = false）时，就算找不到bean也不报错。

#### @RequestParam

用在方法的参数前面

#### @PathVariable

路径变量

### 全局处理异常的：

#### @ControllerAdvice:

包含@Component。可以被扫描到，统一处理异常。

#### @ExceptionHandler（Exception.class）:

用在方法上面表示遇到这个异常就执行以下方法。

