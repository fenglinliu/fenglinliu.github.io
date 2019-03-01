---
tags: [Spring]
---
## <center>**spring aop中的指示器** </center>

#### 1.有哪些指示器

| **execution()**   | 依据返回类型,全类名,方法列表,来匹配切点,   可使用通配符 |
| ----------------- | ------------------------------------------------------- |
| **this()**        | **标注匹配类型的所有方法   必须全类名**                 |
| **within()**      | **标准匹配类型的所有方法   可使用通配符**               |
| **args()**        | **标注匹配的参数列表**                                  |
| **target()**      | **标注匹配类的所有方法   必须全类名**                   |
| **@annotation()** | **标准匹配注解的方法   必须全类名**                     |
| **@within()**     | **标注使用指定注解类的所有方法,及其子类   必须全类名**  |
| **@args()**       | **标注参数注解,此参数类型使用指定注解时   必须全类名**  |
| **@target()**     | **标注匹配注解类的所有方法**                            |

常用的是execution，所以下面重点讲execution

>execution **中第一个\*表示任何返回类型.** 
>
>**要注意一点,类名需要用一个.占位**

![](https://fenglinliu.github.io/assets/img/blog/2019-03-01_140932.png)

#### 2.范例

```java
任何的public方法

execution(public * *(..))

以set开始的方法

execution(* set*(..))

定义在cn.freemethod.business.pack.Say接口中的方法

execution(* cn.freemethod.business.pack.Say.*(..))

任何cn.freemethod.business包中的方法

execution(* cn.freemethod.business.*.*(..))

任何定义在com.xyz.service包或者其子包中的方法

execution(* cn.freemethod.business..*.*(..))

其他表达式
任何在com.xyz.service包中的方法

within(com.xyz.service.*)

任何定义在com.xyz.service包或者其子包中的方法

within(com.xyz.service..*)

任何实现了com.xyz.service.AccountService接口中的方法

this(com.xyz.service.AccountService)

任何目标对象实现了com.xyz.service.AccountService的方法

target(com.xyz.service.AccountService)

一般情况下代理类(Proxy)和目标类(Target)都实现了相同的接口，所以上面的2个基本是等效的。
有且只有一个Serializable参数的方法

args(java.io.Serializable)

只要这个参数实现了java.io.Serializable接口就可以，不管是java.io.Serializable还是Integer，还是String都可以。
目标(target)使用了@Transactional注解的方法

@target(org.springframework.transaction.annotation.Transactional)

目标类(target)如果有Transactional注解中的所有方法

@within(org.springframework.transaction.annotation.Transactional)

任何方法有Transactional注解的方法

@annotation(org.springframework.transaction.annotation.Transactional)

有且仅有一个参数并且参数上类型上有Transactional注解

@args(org.springframework.transaction.annotation.Transactional)

注意是参数类型上有Transactional注解，而不是方法的参数上有注解。
bean的名字为tradeService中的方法

bean(simpleSay)

bean名字为simpleSay中的所有方法。
bean名字能匹配

bean(*Impl)

bean名字匹配*Impl的bean中的所有方法。
```



#### 3.Spring aop 排除方法

* 方式一

```java
execution(* com...*Service+.*(..)) and !execution(* Object.*(..))
```

* 方式二

```xml
<aop:config>
<aop:advisor pointcut="execution(* com.aaa.bbb..*ServiceImpl.*(..))  and !execution(* com.aaa.bbb..imptpatient..*ServiceImpl.*(..))" advice-ref="transactionAdvice"/>
<aop:advisor pointcut="execution(* com.aaa.bbb..*ServiceImpl.*(..))" advice-ref="transactionAdvice"/>
</aop:config>
```

#### 4.Spring AOP @Before @Around @After 等 advice 的执行顺序

AOP进入------》@Around------》@Before------》@Around中被aop的方法自身调用------》@Around------》@After------》@AfterReturning/@AfterThrowing


