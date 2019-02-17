---
tags: [REST、RESTful]
---
## <center>Spring Boot中http请求的发送与接收</center>

#### 1.get请求

1. get发送请求格式：http://localhost:8080/test?name=Tom&age=13
   ```java
   // 方式一
   String uri = "http://localhost:8080/test?name={name}&age={age}";
   Map<String, Object> params = new HashMap<String, Object>();
   params.put("name", "Tom");
   params.put("age", "13");
   RestTemplate restTemplate = new RestTemplate();
   ResponseEntity<String> result = restTemplate.getForEntity(uri, String.class, params); // 方式一返回内容：<200,result,{Content-Type=[text/plain;charset=UTF-8], Content-Length=[6], Date=[Tue, 24 Jul 2018 11:59:04 GMT]}>
   // 可以通过一下方式获取result的不同字段的值
   result.getStatusCode() // 200
   result.getBody() // result
   result.getHeaders() // {Content-Type=[text/plain;charset=UTF-8], Content-Length=[6], Date=[Tue, 24 Jul 2018 11:59:04 GMT]}
   
   // 方式二
   String uri = "http://localhost:8080/test?name={name}&age={age}";
   Map<String, Object> params = new HashMap<String, Object>();
   params.put("name", "Tom");
   params.put("age", "13");
   RestTemplate restTemplate = new RestTemplate();
   String result = restTemplate.getForObject(uri, String.class, params); // 方式二返回内容：result
   
   
   注意：参数除了可以放在map中，还可以依次放在第三个参数的位置，如下所示
   restTemplate.getForObject("http://admin-service/dpmt?workNumber={workNumber}", String.class, workNumber);
   restTemplate.getForObject("http://admin-service/dpmt?workNumber={workNumber}&name={name}", String.class, workNumber, name);
   ```

   接收请求代码
   ```java
   // 方式一
   @RequestMapping(value="/test", method=RequestMethod.GET)
   @ResponseBody
   public String testGet(String name, String age) {
   	String result = "result";
   	logger.info("name:{} age:{}", name, age);
   	return result;
   }
   
   // 方式二
   @RequestMapping(value="/test", method=RequestMethod.GET)
   @ResponseBody
   public String testGet(HttpServletRequest request) {
   	String result = "result";
   	String name = request.getParameter("name");
   	String age = request.getParameter("age");
   	logger.info("name:{} age:{}", name, age);
   	return result;
   }
   
   // 方式三
   @RequestMapping(value="/test", method=RequestMethod.GET)
   @ResponseBody
   public String testGet(Person person) { // 建立一个带有name和age属性的Persion类
   	String result = "result";
   	String name = person.getName();
   	String age = person.getAge();
   	logger.info("name:{} age:{}", name, age);
   	return result;
   }
   
   // 方式四
   @RequestMapping(value="/test", method=RequestMethod.GET)
   @ResponseBody
   public String testGet(@RequestParam("name") String name, @RequestParam("age") String age) {
   	String result = "result";
   	logger.info("name:{} age:{}", name, age);
   	return result;
   }
   ```

2. get发送请求格式http://localhost:8080/test/Tom

   ```java
   // 发送方式就是上面的两种，本质都一样，举一个例子说明
   String uri = "http://localhost:8080/test/{name}";
   Map<String, Object> params = new HashMap<String, Object>();
   params.put("name", "Tom");
   RestTemplate restTemplate = new RestTemplate();
   String result = restTemplate.getForObject(uri, String.class, params);
   ```

   接收请求代码

   ```java
   @RequestMapping(value="/test/{name}", method=RequestMethod.GET)
   @ResponseBody
   public String testGet(@PathVariable("name") String name) {
   	String result = "result";
   	logger.info("name:{}", name);
   	return result;
   }
   ```

#### 2.post请求

1. post发送请求格式：http://localhost:8080/testPost/Tom?age=13&height=170

   ```java
   String uri = "http://localhost:8080/testPost/{name}?age={age}&height={height}";
   RestTemplate restTemplate = new RestTemplate();
   Map<String, Object> params = new HashMap<String, Object>();
   params.put("name", "Tom");
   params.put("age", "13");
   params.put("height", "170");
   String request = "this is request body";
   ResponseEntity<String> result = restTemplate.postForEntity(uri, request, String.class, params);
   ```

   接收请求代码

   ```java
   @RequestMapping(value = "/testPost/{name}", method = RequestMethod.POST)
   @ResponseBody
   public String testPost(@RequestBody (required=false) String requestBody, @PathVariable("name") String name, @RequestParam("age") String age,String height) {
   	String result = "result";
   	logger.info("requestBody:{}", requestBody);
   	logger.info("name:{}", name);
   	logger.info("age:{}", age);
   	return result;
   }
   ```

2. post发送请求格式：http://localhost:8080/testPost/Tom (参数在请求体中)

   ```java
   String uri = "http://localhost:8080/testPost";
   RestTemplate restTemplate = new RestTemplate();
   String request = "this is request body";
   ResponseEntity<String> result = restTemplate.postForEntity(uri, request, String.class);
   ```

   接收请求代码

   ```java
   @RequestMapping(value = "/testPost", method = RequestMethod.POST)
   @ResponseBody
   public String testPost(@RequestBody (required=false) String requestBody) {
   	String result = "result";
   	logger.info("requestBody:{}", requestBody);
   	return result;
   }
   ```

   #### 2.注意

   1.uriVariables' must not be null，如果没有任何参数，官方函数放参数位置的形参不能直接写null，正确的写法是创建一个<u>不含任何参数的HashMap或不含任何属性的Object对象</u>。

   ```java
   String result = restTemplate.getForObject("http://localhost:9999/get", String.class, new HashMap());
   ```

   #### 3.delete等请求

   这些请求推荐使用exchange函数

   实例代码

   ```java
   public  void  deleteQueue(String vhost,String queue){
           HttpHeaders headers = new HttpHeaders();//header参数
           headers.add("authorization",Auth);
           headers.setContentType(MediaType.APPLICATION_JSON);
   
           JSONObject content = new JSONObject();//放入body中的json参数
           content.put("mode","delete");
           content.put("name",queue);
           content.put("vhost","/" + vhost);
   
           String newUrl = url;
   
           HttpEntity<JSONObject> request = new HttpEntity<>(content,headers); //组装请求体和请求头
     
           ResponseEntity<String> response = template.exchange(newUrl,HttpMethod.DELETE,request,String.class);
       }
   
   /*
    * 如果参数都在url上（没有requestBody）
    */
   restTemplate.exchange(url, HttpMethod.DELETE,null, JSONObject.class); // HttpEntity参数可以置空
   
   ```

   如果出现org.springframework.web.client.HttpClientErrorException: 400 null

   检查url是否拼接有问题，这回导致请求不符合服务端要求

