---
tags: [Spring]
---
## <center>Spring AOP</center>

##### 1.代理模式

***代理模式**：代理模式的核心作用就是通过代理，控制对对象的访问。它的设计思路是：定义一个抽象角色，让代理角色和真实角色分别去实现它。（代理模式由三部分组成：抽象角色、代理类、实现类）<br>
<u>真实角色：实现抽象角色，定义真实角色所要实现的业务逻辑，供代理角色调用。<br>
<u>代理角色：代理角色：实现抽象角色，在真实角色的实现方法前后，加上自己的逻辑<br>

代理模式可以分为三种：

**静态代理：**我们自己<u>创建一个代理类</u><br>

**动态代理：**程序自动<u>帮我们生成一个代理类</u>，动态代理又分为jdk代理和cglib代理<br>

* **JDK代理：**JDK动态代理是利用反射机制生成一个实现抽象角色的匿名代理类，在调用具体方法前调用InvokeHandler 来处理<br>

  缺点：JDK 实现动态代理需要实现类通过接口定义业务方法<br>
  （JDK 实现动态代理需要实现类通过接口定义业务方法，那对于没有接口的类，如何实现动态代理呢，这就需要 CGLIB 了）

* **CGLIB代理：**GLIB 采用了非常底层的字节码技术，通过字节码技术为一个类创建子类（代理类），并在子类中采用方法拦截的技术拦截所有父类方法的调用，顺势织入横切逻辑。<br>

  缺点：因为采用的是继承，所以不能对final修饰的类或有final修饰的方法的类进行代理<br>

```java
// 静态代理

/**
 * 明星接口类（抽象角色）
 */
public interface Star {

    /**
     * 唱歌方法
     */
    void sing();

}
----------------------------------------------------
/**
 * 真实明星类（真实角色）
 */
public class RealStar implements Star {

    @Override
    public void sing() {
        System.out.println("明星本人开始唱歌……");
    }
}
----------------------------------------------------
/**
 * 明星的静态代理类（代理角色）
 */
public class ProxyStar implements Star {

    /**
     * 接收真实的明星对象
     */
    private Star star;

    /**
     * 通过构造方法传进来真实的明星对象
     * @param star star
     */
    public ProxyStar(Star star) {
        this.star = star;
    }

    @Override
    public void sing() {
        System.out.println("代理先进行谈判……");
        // 唱歌只能明星自己唱
        this.star.sing();
        System.out.println("演出完代理去收钱……");
    }

}
----------------------------------------------------
/**
 * 测试客户端
 */
public class Client {

    /**
     * 测试静态代理结果
     * @param args args
     */
    public static void main(String[] args) {
        Star realStar = new RealStar();
        Star proxy = new ProxyStar(realStar);

        proxy.sing();
    }
}
```

```java
// JDK 动态代理

/**
 * 明星接口类（抽象角色）
 */
public interface Star {

    /**
     * 唱歌方法
     */
    void sing();

}
----------------------------------------------------
/**
 * 真实明星类（真实角色）
 */
public class RealStar implements Star {

    @Override
    public void sing() {
        System.out.println("明星本人开始唱歌……");
    }
}
----------------------------------------------------
/**
 * 动态代理[处理类](这个处理类，是一个公用方法，不能看作代理类)
 */
public class JdkProxyHandler {

    /**
     * 用来接收真实明星对象
     */
    private Object realStar;

    /**
     * 通过构造方法传进来真实的明星对象
     *
     * @param star star
     */
    public JdkProxyHandler(Star star) {
        super();
        this.realStar = star;
    }

    /**
     * 给真实对象生成一个代理对象实例
     *
     * @return Object
     */
    public Object getProxyInstance() {
        return Proxy.newProxyInstance(realStar.getClass().getClassLoader(),
                realStar.getClass().getInterfaces(), (proxy, method, args) -> { // 第二个参数是，代理类要实现的接口列表（也就是说代理类一定要实现抽象接口）
                    System.out.println("代理先进行谈判……");
                    // 唱歌需要明星自己来唱
                    Object object = method.invoke(realStar, args);
                    System.out.println("演出完代理去收钱……");
                    return object;
                });
    }
}
----------------------------------------------------
/**
 * 测试客户端
 */
public class Client {

    /**
     * 测试JDK动态代理结果
     * @param args args
     */
    public static void main(String[] args) {
        Star realStar = new RealStar();
        // 创建一个代理对象实例
        Star proxy = (Star) new JdkProxyHandler(realStar).getProxyInstance();

        proxy.sing();
    }
}
```

```java
// CGLib 动态代理
/**
 * cglib代理处理类(这个处理类，是一个公用方法，不能看作代理类)
 */
public class CglibProxyHandler implements MethodInterceptor {

    /**
     * 维护目标对象
     */
    private Object target;

    public Object getProxyInstance(final Object target) {
        this.target = target;
        // Enhancer类是CGLIB中的一个字节码增强器，它可以方便的对你想要处理的类进行扩展
        Enhancer enhancer = new Enhancer();
        // 将被代理的对象设置成父类
        enhancer.setSuperclass(this.target.getClass());
        // 回调方法，设置拦截器
        enhancer.setCallback(this);
        // 动态创建一个代理类
        return enhancer.create();
    }

    @Override
    public Object intercept(Object object, Method method, Object[] args,
            MethodProxy methodProxy) throws Throwable {

        System.out.println("代理先进行谈判……");
        // 唱歌需要明星自己来唱
        Object result = methodProxy.invokeSuper(object, args);
        System.out.println("演出完代理去收钱……");
        return result;
    }
}
使用 CGLIB 需要实现 MethodInterceptor 接口，并重写intercept 方法，在该方法中对原始要执行的方法前后做增强处理。该类的代理对象可以使用代码中的字节码增强器来获取。接下来写个客户端测试程序。

/**
 * 测试客户端
 */
public class Client {

    /**
     * 测试Cglib动态代理结果
     * @param args args
     */
    public static void main(String[] args) {
        Star realStar = new RealStar();
        Star proxy = (Star) new CglibProxyHandler().getProxyInstance(realStar);

        proxy.sing();
    }
}
```



##### 2.AOP

Spring的AOP就是采用代理模式这种思想，为目标代码的前后添加逻辑处理

<u>JDK 动态代理和 CGLIB 动态代理均是实现 Spring AOP 的基础</u> 

Spring AOP 中的代理使用逻辑了：如果目标对象实现了接口，默认情况下会采用 JDK 的动态代理实现 AOP；如果目标对象没有实现了接口，则采用 CGLIB 库，Spring 会自动在 JDK 动态代理和 CGLIB 动态代理之间转换。 

**Spring的aop怎么实现的?**

每个Bean都会被JDK或者Cglib 代理，如果一个bean没有实现任何接口，就用JDK代理。<br>
每个 Bean 会有多个“方法拦截器”。注意：拦截器分为两层，外层由 Spring 内核控制流程，内层拦截器是用户设置，也就是 AOP。<br>
当代理方法被调用时，先经过外层拦截器，外层拦截器根据方法的各种信息判断该方法应该执行哪些“内层拦截器”。内层拦截器的设计就是责任链的设计。<br>

 AOP 分成 2 个部分来:<br>
(1)代理的创建（按步骤）：<br>
首先，需要创建代理工厂，代理工厂需要 3 个重要的信息：拦截器数组，目标对象接口数组，目标对象。<br>
创建代理工厂时，默认会在拦截器数组尾部再增加一个默认拦截器 —— 用于最终的调用目标方法。<br>
当调用 getProxy 方法的时候，会根据接口数量大余 0 条件返回一个代理对象（JDK or  Cglib）。<br>
注意：创建代理对象时，同时会创建一个外层拦截器，这个拦截器就是 Spring 内核的拦截器。用于控制整个 AOP 的流程。<br>
(2)代理的调用<br>
当对代理对象进行调用时，就会触发外层拦截器。<br>
外层拦截器根据代理配置信息，创建内层拦截器链。创建的过程中，会根据表达式判断当前拦截是否匹配这个拦截器。而这个拦截器链设计模式就是职责链模式。<br>
当整个链条执行到最后时，就会触发创建代理时那个尾部的默认拦截器，从而调用目标方法。最后返回。<br>

**什么场景下用AOP**？<br>
AOP是对OOP开发模式的补足，像日志记录、事务管理等场景（系统中的每个业务对象都要加入的功能需求就被称为横切关注点）

