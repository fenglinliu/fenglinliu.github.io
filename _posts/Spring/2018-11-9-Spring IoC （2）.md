---
tags: [Spring]
---
## <center>Spring IoC （2）</center>

**Spring的IOC优点**：<br>
它将最小化应用程序中的代码量。<br>
(它将使您的应用程序易于测试，因为它不需要单元测试用例中的任何单例或 JNDI 查找机制。)<br>
它以最小的影响和最少的侵入机制促进松耦合。（最主要的好处）<br>
它支持即时的实例化和延迟加载服务。<br>

**Spring 中的 IoC 的实现原理**：<br>
1.初始化IoC容器<br>
2.读取配置文件<br>
3.解析配置文件，将IoC容器修改为BeanDefinition（个BeanDefinition描述了一个bean实例,实例包含属性值,构造函数参数值,以及更多实现信息）<br>
4.将BeanDefinition实例化为Java对象<br>
5.注入对象之间的依赖关系<br>

**spring 中有多少种 IOC 容器？**

BeanFactory - BeanFactory 就像一个包含 bean 集合的工厂类。它会在客户端要求时实例化 bean。<br>
ApplicationContext - ApplicationContext 接口扩展了 BeanFactory 接口。它在 BeanFactory 基础上提供了一些额外的功能。<br>

**beanfactory和applicationcontext是什么关系，使用有什么区别？**<br>
ApplicationContext 接口扩展了 BeanFactory 接口<br>，扩展的功能有如下：<br>
(1)实现了MessageSource接口<br>
(2)可以激活已经注册的监听器<br>
(1)ResourceLoader支持<br>



**Spring的bean？Spring的bean的默认范围？Bean的生命周期？**<br>
**Spring bean：**Bean 是基于用户提供给容器的配置元数据创建的用户应用程序主干的对象。Bean 由 Spring IoC 容器管理（实例化、装配）<br>
**Spring的bean的默认范围：**<br>
Spring bean 支持 5 种 scope：<br>
Singleton - 每个 Spring IoC 容器仅有一个单实例。（默认都是Singleton）<br>
Prototype - 每次请求都会产生一个新的实例。<br>
Request - 每一次 HTTP 请求都会产生一个新的实例，并且该 bean 仅在当前 HTTP 请求内有效。<br>
Session - 每一次 HTTP 请求都会产生一个新的 bean，同时该 bean 仅在当前 HTTP session 内有效。<br>
Global-session - 类似于标准的 HTTP Session 作用域，不过它仅仅在基于 portlet 的 web 应用中才有意义。Portlet 规范定义了全局 Session 的概念，它被所有构成某个 portlet web 应用的各种不同的 portlet 所共享。在 global session 作用域中定义的 bean 被限定于全局 portlet Session 的生命周期范围内。如果你在 web 中使用 global session 作用域来标识 bean，那么 web 会自动当成 session 类型来使用。<br>
<u>仅当用户使用支持 Web 的 ApplicationContext 时，最后三个才可用。</u><br>

**Bean的生命周期：**可以简述为以下九步<br>
1.实例化Bean对象：Spring容器根据配置中的bean定义中实例化bean。<br>
2.设置对象属性：Spring 使用依赖注入填充所有属性<br>
3.检查Aware相关接口并设置相关依赖：如果 bean 实现 BeanNameAware 接口，则工厂通过传递 bean 的 ID 来调用 setBeanName()递Bean的ID。<br>
如果 bean 实现 BeanFactoryAware 接口，工厂通过传递自身的实例来调用 setBeanFactory()。<br>
5.BeanPostProcessors前置处理：如果存在与 bean 关联的任何 BeanPostProcessors，则调用 preProcessBeforeInitialization() 方法。
6.调用Bean的初始化方法：如果为bean指定了 init 方法（ 的 init-method 属性），那么将调用它。<br>
7.BeanPostProcessors后置处理：如果存在与 bean 关联的任何 BeanPostProcessors，则将调用 postProcessAfterInitialization() 方法。<br>
8.使用Bean<br>
9.容器关闭之前，调用Bean的销毁方法：如果 bean 实现 DisposableBean 接口，当 spring 容器关闭时，会调用 destory()；如果为 bean 指定了 destroy 方法（ 的 destroy-method 属性），那么将调用它。<br>

