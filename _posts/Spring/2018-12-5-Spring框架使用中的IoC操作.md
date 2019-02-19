---
tags: [Spring]
---
## <center>Spring框架使用中的IoC操作</center>

##### 场景1：Spring的bean如果要在实例化过程中修改其某一个成员变量

首先我们知道Bean的生命周期

1.实例化Bean对象：Spring容器根据配置中的bean定义中实例化bean。<br>
2.设置对象属性：Spring 使用依赖注入填充所有属性<br>
3.检查Aware相关接口并设置相关依赖：如果 bean 实现 BeanNameAware 接口，则工厂通过传递 bean 的 ID 来调用 setBeanName()递Bean的ID。<br>
如果 bean 实现 BeanFactoryAware 接口，工厂通过传递自身的实例来调用 setBeanFactory()。<br>
5.BeanPostProcessors前置处理：如果存在与 bean 关联的任何 BeanPostProcessors，则调用 preProcessBeforeInitialization() 方法。
6.调用Bean的初始化方法：如果为bean指定了 init 方法（ 的 init-method 属性），那么将调用它。<br>
7.BeanPostProcessors后置处理：如果存在与 bean 关联的任何 BeanPostProcessors，则将调用 postProcessAfterInitialization() 方法。<br>
8.使用Bean<br>

Spring的bean如果要在实例化过程中修改其某一个成员变量让类实现：

```java
// postProcessBeforeInstantiation方法的作用在目标对象被实例化之前调用的方法，可以返回目标实例的一个代理用来代替目标实例
// beanClass参数表示目标对象的类型，beanName是目标实例在Spring容器中的name
// 返回值类型是Object，如果返回的是非null对象，接下来除了postProcessAfterInitialization方法会被执行以外，其它bean构造的那些方法都不再执行。否则那些过程以及postProcessAfterInitialization方法都会执行
Object postProcessBeforeInstantiation(Class<?> beanClass, String beanName) throws BeansException;

// postProcessAfterInstantiation方法的作用在目标对象被实例化之后并且在属性值被populate之前调用
// bean参数是目标实例(这个时候目标对象已经被实例化但是该实例的属性还没有被设置)，beanName是目标实例在Spring容器中的name
// 返回值是boolean类型，如果返回true，目标实例内部的返回值会被populate，否则populate这个过程会被忽视
boolean postProcessAfterInstantiation(Object bean, String beanName) throws BeansException;

// postProcessPropertyValues方法的作用在属性中被设置到目标实例之前调用，可以修改属性的设置
// pvs参数表示参数属性值(从BeanDefinition中获取)，pds代表参数的描述信息(比如参数名，类型等描述信息)，bean参数是目标实例，beanName是目标实例在Spring容器中的name
// 返回值是PropertyValues，可以使用一个全新的PropertyValues代替原先的PropertyValues用来覆盖属性设置或者直接在参数pvs上修改。如果返回值是null，那么会忽略属性设置这个过程(所有属性不论使用什么注解，最后都是null)
PropertyValues postProcessPropertyValues(
    PropertyValues pvs, PropertyDescriptor[] pds, Object bean, String beanName)
    throws BeansException;


总结：

    InstantiationAwareBeanPostProcessor接口继承BeanPostProcessor接口，它内部提供了3个方法，再加上BeanPostProcessor接口内部的2个方法，所以实现这个接口需要实现5个方法。InstantiationAwareBeanPostProcessor接口的主要作用在于目标对象的实例化过程中需要处理的事情，包括实例化对象的前后过程以及实例的属性设置
    postProcessBeforeInstantiation方法是最先执行的方法，它在目标对象实例化之前调用，该方法的返回值类型是Object，我们可以返回任何类型的值。由于这个时候目标对象还未实例化，所以这个返回值可以用来代替原本该生成的目标对象的实例(比如代理对象)。如果该方法的返回值代替原本该生成的目标对象，后续只有postProcessAfterInitialization方法会调用，其它方法不再调用；否则按照正常的流程走
    postProcessAfterInstantiation方法在目标对象实例化之后调用，这个时候对象已经被实例化，但是该实例的属性还未被设置，都是null。如果该方法返回false，会忽略属性值的设置；如果返回true，会按照正常流程设置属性值
    postProcessPropertyValues方法对属性值进行修改(这个时候属性值还未被设置，但是我们可以修改原本该设置进去的属性值)。如果postProcessAfterInstantiation方法返回false，该方法不会被调用。可以在该方法内对属性值进行修改
    父接口BeanPostProcessor的2个方法postProcessBeforeInitialization和postProcessAfterInitialization都是在目标对象被实例化之后，并且属性也被设置之后调用的
    Instantiation表示实例化，Initialization表示初始化。实例化的意思在对象还未生成，初始化的意思在对象已经生成
```



##### 场景2：在Bean创建后，和Bean销毁前添加逻辑处理

```java
在java的实际开发过程中，我们可能常常需要使用到init method和destroy method，比如初始化一个对象（bean）后立即初始化（加载）一些数据，在销毁一个对象之前进行垃圾回收等等。 
发现实际上可以有两种方式来实现init method和destroy method。

一、@Bean注解方式： 
首先要创建一个至少拥有两个方法的类，一个方法充当init method，另一个充当destroy method。
package springTest2;
public class Test1 {
    public void init() {
        System.out.println("this is init method1");
    }
    public Test1() {
        super();
        System.out.println("构造函数1");
    }
    public void destroy() {
        System.out.println("this is destroy method1");
    }
}
创建好了这个类，我们就可以使用@Bean注解的方式指定两个方法，以让他们生效。
package springTest2;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;@Configuration@ComponentScan("springTest2") public class ConfigTest {@Bean(initMethod = "init", destroyMethod = "destroy") Test1 test1() {
        return new Test1();
    }
}
这里边的@Configguration注解是告诉spring这个类是一个配置类，相当于我们的xml文件，@ComponentScan则是指定需要spring来扫描的包，相当于xml中的context:component-scan属性。
而@Bean后边的initMethod和destroyMethod就是在声明这是一个baen的同时指定了init和destroy方法，方法名从功能实现上来说可以随意。
到这里我们就已经用第一种方式写好了，为了验证成功与否，再写一个main方法验证一下：
package springTest2;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class MainTest {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ConfigTest.class);
        System.out.println("#################################");
        context.close();
    }
}


二、JSR-250注解的方式（需要导入jsr250-api的jar包）
首先依然是创建一个拥有构造方法在内的三个方法的java类：
package springTest2;
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
public class Test2 {@PostConstruct public void init() {
        System.out.println("this is init method2");
    }
    public Test2() {
        super();
        System.out.println("构造函数2");
    }@PreDestroy public void destroy() {
        System.out.println("this is destroy method2");
    }
}
很显然，这里和上一个类不同的是，在init和destroy方法上加入了两个注解，@PostConstruct和上边@Bean后的initMethod相同，而@PreDestroy则是和destroyMethod做用相同。
既然这里有了区别，已经指定了init method和destroy method，那么后边声明bean的时候自然也会有不同，也就不需要再指定一遍：
package springTest2;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;@Configuration@ComponentScan("springTest2") public class ConfigTest {@Bean Test2 test2() {
        return new Test2();
    }
}
所以，如上代码中只需要简单的声明这是一个bean就可以了，类上边的两个注解和上一个例子中的意思相同。
再测试一下：
package springTest2;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
public class MainTest {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(ConfigTest.class);
        System.out.println("#################################");
        context.close();
    }
}
```



##### 场景3：Spring如何动态地加载一个bean到bean容器中,不是通过配置文件配置的？

首先：通过ApplicationContext获得Spring的BeanFactory<br>

```
applicationContext = ContextLoader.getCurrentWebApplicationContext();
DefaultListableBeanFactory beanFactory = (DefaultListableBeanFactory)applicationContext.getAutowireCapableBeanFactory();
```

然后，根据obj的类型、创建一个新的bean、添加到Spring容器中<br>

```
// 根据obj的类型、创建一个新的bean
BeanDefinition beanDefinition = new GenericBeanDefinition();
beanDefinition.setBeanClassName(obj.getClass().getName());
		
// 添加到Spring容器中,方式一（非单例）
beanFactory.registerBeanDefinition(obj.getClass().getName(), beanDefinition);
		
// 添加到Spring容器中,方式二（单例）
applicationContext.getAutowireCapableBeanFactory().applyBeanPostProcessorsAfterInitialization(obj, obj.getClass().getName());
beanFactory.registerSingleton(obj.getClass().getName(), obj);
```

