---
tags: [REST、RESTful]
---

## <center>REST和Restful</center>

#### 1.什么是Rest？

**Rest是Representation State Transfer(表述性状态转移)的缩写，它是一种软件架构风格。**

​	Rest利用现有的技术HTTP+URI+XML来实现要求的架构风格，其中HTTP协议用来统一接口，URI用于定位资源，XML或JSON(它们从表述格式角度描述)、文本或二进制流(它们从内容角度描述)作为资源的表述。

##### 1.1 Rest请求资源过程

​	首先，访问一个UR，经过HTTP，将资源的表述从服务器转移到客户端或者从客户端转移到服务器

##### 1.2 Rest的6个特点

​	①客户端服务器的 ②无状态的 ③可缓存的 ④统一接口 ⑤分层系统 ⑥按需编码



#### 2.什么是Restful

​         **RESTful指实现了REST软件架构风格的Web服务**

##### 2.1 Restful成熟度模型

​	Restful成熟度模型用于描述一个web服务有多么地Restful。Leonard Richardson提出过REST成熟度模型，简称Richardson成熟度模型 。

* **第0级：HTTP作为传输方式**（一个URI，一个HTTP方法 ）

  这种做法相当于把 HTTP 这个应用层协议降级为传输层协议用 ，所有信息都放入HTTP的请求体中。 

* **第1级：引入了资源的概念**（多个URI，一个HTTP方法）

  引入资源的概念，用一个HTTP方法，不同的URI只是作为不同的调用入口。 因此，在这个级别，资源是被"GETful"接口操作而不是HTTP动词 

* **第2级：使用HTTP动词**（多个URI，多个HTTP方法）

   让不同的URI代表不同的资源，同时使用多个HTTP方法操作这些资源 (资源名称为基URI的一部分，而不是查询参数 )，<u>我们现在看到的大多数所谓RESTful API做到的也就是这个级别</u>

   - GET用于查询资源；
   - POST创建新资源；
   - PUT更新(全部更行)已存在的资源；
   - PATCH更新(部分更新)已存在的资源；
   - DELETE删除已存在的资源。
   - HEAD用于查询资源是否存在；
   - OPTIONS获取信息，关于资源的哪些属性是客户端可以改变的。 

* **第3级：使用超媒体**（多个URI，多个HTTP方法）

   Hypermedia API的设计被称为HATEOAS。 在资源的表达中增加了链接信息。通过返回的信息，服务器告诉客户端下一步可以做什么，以及需要操纵的资源的URI。 客户端可以根据链接来执行后续动作 ，这使得使得用户不查文档，也知道下一步应该做什么。

   超媒体的一个明显好处是它允许服务器在不中断客户端的情况下更改其URI方案。

##### 2.2 Restful风格 V.S. RPC风格 

|             |                          方法信息                          |            作用域信息            |
| ----------- | :--------------------------------------------------------: | :------------------------------: |
| **Restful** |                标准的HTTP方法(比如GET、PUT)                |        URI显示定义作用域         |
| **RPC**     | 采用HTTP协议的POST方法，方法信息在SOAP协议包或HTTP协议包中 | 作用域在SOAP协议包或HTTP协议包中 |

> 注：
>
> ①方法信息只方法名等
>
> ②作用域信息是对资源的过滤、分页、排序等条件

##### 2.2 Restful API设计参考规范（以[GitHub API](https://api.github.com/)为参考）

|      操作类型      |                           API示例                            |
| :----------------: | :----------------------------------------------------------: |
|      创建资源      |             POST http://api.example.com/v1/user              |
|      查询资源      | GET http://api.example.com/v1/user?user_id=ID 或 http://api.example.com/v1/user/{ID} |
| 更新(全部更新)资源 |           PUT http://api.example.com/v1/user/{ID}            |
| 更新(部分更新)资源 |          PATCH http://api.example.com/v1/user/{ID}           |
|      删除资源      |          DELETE http://api.example.com/v1/user/{ID}          |

* 过滤信息

如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。 放在url后面，以value1=parameter1&value2=parameter2的形式

* 返回结果

通常以JSON对象形式返回

> 注：
>
> 网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以GitHub API中的名词也应该使用复数 。
>
> 但是，在我国像微信API、以及阿里巴巴 Java开发手册这些名词都是单数，所以我们就用单数。



