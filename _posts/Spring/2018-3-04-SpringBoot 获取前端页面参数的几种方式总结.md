---
tags: [Spring Boot]
---
## <center>SpringBoot 获取前端页面参数的几种方式总结</center>

Spring Boot 的一个好处就是通过注解可以轻松获取前端页面的参数，之后可以将参数经过一系列处理传送到后台数据库。大概分为以下几种：

1.指定前端url请求参数名称与方法名一致。

2.通过Http ServletRequest来获取前端页面参数

3.通过创建一个JavaBean 对象来封装表单参数或者是请求url 路径中的参数。

4.通过PathVariable 注解来绑定请求路径的参数

5.通过RequestParam注解来获取

6.通过ModeAttribute方式来注入参数

