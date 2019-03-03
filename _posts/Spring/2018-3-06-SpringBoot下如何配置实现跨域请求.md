---
tags: [Spring Boot]
---
## <center>SpringBoot下如何配置实现跨域请求</center>

什么是跨域请求？

跨域请求，就是说浏览器在执行脚本文件的ajax请求时，甲苯文件所在的服务地址和请求的服务地址不一样。说白了就是ip、网络协议、端口都一样的时候，就是同一个域，否则就是跨域。就是由于Netscape提出一个著名的安全策略-同源策略造成的，就是浏览器对JavaScript施加的安全限制。是防止外网的脚本恶意攻击服务器的一种措施。

SpringBoot 工程如何解决跨域问题？

那么如何在SpringBoot中处理跨域问题呢？方法有很多，这里着重讲一种——利用@Configuration配置跨域。 

代码实现如下：

    @Configuration
    public class CorsConfig {
        private CorsConfiguration buildConfig() {
            CorsConfiguration corsConfiguration = new CorsConfiguration();
            corsConfiguration.addAllowedOrigin("*"); // 1 设置访问源地址
            corsConfiguration.addAllowedHeader("*"); // 2 设置访问源请求头
            corsConfiguration.addAllowedMethod("*"); // 3 设置访问源请求方法
            return corsConfiguration;
        }
    
        @Bean
        public CorsFilter corsFilter() {
            UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
            source.registerCorsConfiguration("/**", buildConfig()); // 4 对接口配置跨域设置
            return new CorsFilter(source);
        }
    }

**“*”代表全部。

其中addAllowedOrigin(String origin)方法是追加访问源地址。如果不使用”*”（即允许全部访问源），则可以配置多条访问源来做控制。 

例如：

    corsConfiguration.addAllowedOrigin("http://www.aimaonline.cn/"); 
    corsConfiguration.addAllowedOrigin("http://test.aimaonline.cn/"); 

查看CorsConfiguration类的官方文档（http://docs.spring.io/spring/docs/4.2.x/javadoc-api/org/springframework/web/cors/CorsConfiguration.html#addAllowedOrigin-java.lang.String-） 

我们可以找到官方对setAllowedOrigins(List allowedOrigins)和addAllowedOrigin(String origin)方法的介绍。

addAllowedOrigin是追加访问源地址，而setAllowedOrigins是可以直接设置多条访问源。 

但是有一点请注意，我查看setAllowedOrigins方法源码时发现，源码如下

    public void setAllowedOrigins(List<String> allowedOrigins) {
            this.allowedOrigins = allowedOrigins != null?new ArrayList(allowedOrigins):null;
    }

根据源码可以得知，setAllowedOrigins会覆盖this.allowedOrigins。所以在配置访问源地址时， 

addAllowedOrigin方法要写在setAllowedOrigins后面，当然了，一般情况下这两个方法也不会混着用。

addAllowedHeader、addAllowedMethod、registerCorsConfiguration方法和addAllowedOrigin的源码差不太多，这里就不一一介绍了。感兴趣的朋友可以自行查阅。

其他实现跨域请求方法

当然。除了用这种初始化配置的方法设置跨域问题，在官方的文档中也介绍了其他实现跨域请求的方法（http://spring.io/blog/2015/06/08/cors-support-in-spring-framework）。

例如在接口上使用@CrossOrgin注解:

    @RestController
    @RequestMapping("/account")
    public class AccountController {
    
         @CrossOrigin
         @GetMapping("/{id}")
         public Account retrieve(@PathVariable Long id) {
             // ...
         }
     
         @DeleteMapping("/{id}")
         public void remove(@PathVariable Long id) {
             // ...
         }
     }

对于上述代码，官方给出如下一段说明：

You can add to your @RequestMapping annotated handler method a @CrossOrigin annotation in order to enable CORS on it (by default @CrossOrigin allows all origins and the HTTP methods specified in the @RequestMapping annotation).

意思就是可以直接在@RequestMapping接口上使用@CrossOrigin实现跨域。@CrossOrigin默认允许所有访问源和访问方法。

还有一种方法是直接对整个Controller进行跨域设置：

    @CrossOrigin(origins = "http://domain2.com", maxAge = 3600)
    @RestController
    @RequestMapping("/account")
    public class AccountController {
    
         @GetMapping("/{id}")
         public Account retrieve(@PathVariable Long id) {
             // ...
         }
     
         @DeleteMapping("/{id}")
         public void remove(@PathVariable Long id) {
             // ...
         }
     }

这里，可以对@CrossOrigin设置特定的访问源，而不是使用默认配置。