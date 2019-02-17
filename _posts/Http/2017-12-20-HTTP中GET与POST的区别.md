---
tags: [Http]
---

**1. 最直观的区别**
-------------

就是GET把参数包含在URL中，POST通过request body传递参数

 

**2. GET和POST漫谈**
-----------------

**GET和POST本质上就是TCP链接，并无差别。但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同。**

 - **GET和POST都基于TCP**
 GET和POST是什么？<font color=red>HTTP协议中的两种发送请求的方法.</font>
 HTTP是什么？<font color=red>HTTP是基于TCP/IP的关于数据如何在万维网中如何通信的协议。</font>

	HTTP设定了好几个服务类别，有GET, POST, PUT, DELETE等等 。 HTTP规定，当执行GET请求的时候，要求把传送的数据放在url中以方便记录；如果是POST请求，就要把数据放在request body中。然而，<font color=red>HTTP只是个行为准则，而TCP才是GET和POST怎么实现的基本。</font>

	HTTP的底层是TCP/IP。所以GET和POST的底层也是TCP/IP，既GET/POST都是TCP链接。GET和POST能做的事情是一样一样的。你要给GET加上request body，给POST带上url参数，技术上是完全行的通的。

 - **浏览器、服务器对URL的限制**
业界不成文的规定是，（大多数）浏览器通常都会限制<font color=red>url长度在2K个字节，而（大多数）服务器最多处理64K大小的url。超过的部分，恕不处理。</font>如果你用GET服务，在request body偷偷藏了数据，不同服务器的处理方式也是不同的，有些服务器会帮你读出数据，有些服务器直接忽略，所以，虽然GET可以带request body，也不能保证一定能被接收到哦。

**3.真正的区别**
-----------------
**GET产生一个TCP数据包；POST产生两个TCP数据包。**

对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；
而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。

因为POST需要两步，时间上消耗的要多一点，看起来GET比POST更有效。因此Yahoo团队有推荐用GET替换POST来优化网站性能。

 **总结**
 1.  GET与POST应该遵循HTTP的规则使用。
 2. 在网络环境好的情况下，发一次包的时间和发两次包的时间差别基本可以无视。而在网络环境差的情况下，两次包的TCP在验证数据包完整性上，有非常大的优点。
 3. 并不是所有浏览器都会在POST中发送两次包，Firefox就只发送一次。

参考文章：[99%的人都理解错了HTTP中GET与POST的区别 ](https://mp.weixin.qq.com/s?timestamp=1513736906&src=3&ver=1&signature=sXD3UZBnA6Gg05Ksax9*BTdiFvnBc*Pug6yuiPZR0GLB39LLjFAGjMhCgznla*IiRpdEQjmrzLNKIUQ*c*3Ve1OosWe*1hUF1xVWYxEsXxQSzeWXAWXnZCuepLUCJQsIbYR3z1SqOiN0FWCZz8AxoHcAUxfaLB8*v6-bH18q820=)
