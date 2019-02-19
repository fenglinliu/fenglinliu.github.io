---
tags: [Spring]
---
## <center>Spring MVC</center>

**Spring MVC的流程**：<br>
(1)Http请求：浏览器发送Http请求，请求被Spring MVC的DispatcherServlet捕获<br>
(2)寻找处理器：DispatcherServlet对URL进行解析，得到请求资源标识符URI，谈后从HandlerMapping中查找处理request的Controller。<br>
(3)调用处理器：DispatcherServlet将请求提交到Controller<br>
(4)调用业务处理：Controller进行业务逻辑处理<br>
(5)返回业务结果：Controller业务处理后，返回ModelAndView<br>
(6)处理视图映射：DispatcherServlet查询一个或多个ViewResoler视图解析器<br>
(7)返回模型：找到ModelAndView指定的视图后，将模型数据传给视图<br>
(8)Http响应：视图负责将结果显示到客户端<br>

