---
tags: [Spring Boot]
---
## <center>RedisTemplate中string类型的使用</center>

### 简述

- 在使用springboot时，我们有时候会整合Redis进行相关操作，首先在pom.xml中添加redis相关依赖

  ```xml
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-data-redis</artifactId>
          </dependency>
  ```

- 在application.properties中添加相关配置，更具体的配置可以自行寻找

  ```properties
  #=========redis基础配置=========
  spring.redis.database=0
  spring.redis.host=192.168.180.130
  spring.redis.port=6379
  # 连接超时时间 单位 ms（毫秒）
  spring.redis.timeout=3000
  
  #=========redis线程池设置=========
  # 连接池中的最大空闲连接，默认值也是8。
  spring.redis.pool.max-idle=200
  
  #连接池中的最小空闲连接，默认值也是0。
  spring.redis.pool.min-idle=200
  
  # 如果赋值为-1，则表示不限制；pool已经分配了maxActive个jedis实例，则此时pool的状态为exhausted(耗尽)。
  spring.redis.pool.max-active=2000
  
  # 等待可用连接的最大时间，单位毫秒，默认值为-1，表示永不超时
  spring.redis.pool.max-wait=1000
  ```

### 操作类

配置好后，使用的操作类主要有两种

- RedisTemplate
- StringRedisTemplate

【RedisTemplate】
RedisTemplate是最基本的操作类，它默认的序列化方式是JdkSerializationRedisSerializer，在存值时，键值会被序列化为字节数组，可读性差，取值时也是一样，如果redis中存的值正常的字符串形式，取值时将返回null
【StringRedisTemplate】
StringRedisTemplate继承于 RedisTemplate<String, String>，默认的序列化方式是StringRedisSerializer，存值取值都是按照字符串的形式

- 如果存的是字符串，建议直接使用StringRedisTemplate
- 如果是对象，可采取以下两种方法

1. 使用RedisTemplate存，取值时可以直接强转

2. 使用StringRedisTemplate，存值得时候使用json工具类将对象转化为json字符串，取值时再将json字符串转为对象

   ### string类型的实例

   采用springboot中的单元测试

   ```java
   import java.util.ArrayList;
   import java.util.HashMap;
   import java.util.List;
   import java.util.Map;
   import java.util.concurrent.TimeUnit;
   
   import org.junit.Test;
   import org.junit.runner.RunWith;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.boot.test.context.SpringBootTest;
   import org.springframework.data.redis.core.StringRedisTemplate;
   import org.springframework.test.context.junit4.SpringRunner;
   
   @RunWith(SpringRunner.class)
   @SpringBootTest
   public class StringDemo {
   
       @Autowired
       private StringRedisTemplate redisTemplate;
       
       @Test
       public void demo1() {
           //设置k,v
           redisTemplate.opsForValue().set("name", "bpf");
           //取值
           System.out.println(redisTemplate.opsForValue().get("name"));
       }
       
       @Test
       public void demo2() throws InterruptedException {
           //设置k,v以及有效时长，TimeUnit为单位
           redisTemplate.opsForValue().set("name", "bpf", 10, TimeUnit.SECONDS);
           Thread.sleep(11000);
           System.out.println(redisTemplate.opsForValue().get("name"));
       }
       
       @Test
       public void demo3() {
           redisTemplate.opsForValue().set("key", "hello world");
           //从偏移量开始对给定key的value进行覆写，若key不存在，则前面偏移量为空
           redisTemplate.opsForValue().set("key", "redis", 6);
           System.out.println(redisTemplate.opsForValue().get("key"));
       }
       
       @Test
       public void demo4() {
           redisTemplate.opsForValue().set("name", "test");
           //若key不存在，设值
           redisTemplate.opsForValue().setIfAbsent("name", "test2");
           System.out.println(redisTemplate.opsForValue().get("name"));//还是test
       }
       
       @Test
       public void demo5() {
           //批量k,v设值
           Map<String, String> map = new HashMap<String, String>();
           map.put("k1", "v1");
           map.put("k2", "v2");
           map.put("k3", "v3");
           redisTemplate.opsForValue().multiSet(map);
           
           //批量取值
           List<String> keys = new ArrayList<String>();
           keys.add("k1");
           keys.add("k2");
           keys.add("k3");
           List<String> values = redisTemplate.opsForValue().multiGet(keys);
           System.out.println(values);
           
           //批量设值，若key不存在，设值
           //redisTemplate.opsForValue().multiSetIfAbsent(map);
           
       }
       
       @Test
       public void demo6() {
           redisTemplate.opsForValue().set("name", "bpf");
           //设值并返回旧值，无旧值返回null
           System.out.println(redisTemplate.opsForValue().getAndSet("ttt", "calvin"));
       }
       
       @Test
       public void demo7() {
           //自增操作，原子性。可以对long进行double自增，但不能对double进行long自增，会抛出异常
           redisTemplate.opsForValue().increment("count", 1L);//增量为long
           System.out.println(redisTemplate.opsForValue().get("count"));
           
           redisTemplate.opsForValue().increment("count", 1.1);//增量为double
           System.out.println(redisTemplate.opsForValue().get("count"));
       }
       
       @Test
       public void demo8() {
           //key不存在，设值
           redisTemplate.opsForValue().append("str", "hello");
           System.out.println(redisTemplate.opsForValue().get("str"));
           //key存在，追加
           redisTemplate.opsForValue().append("str", " world");
           System.out.println(redisTemplate.opsForValue().get("str"));
   
       }
       
       @Test
       public void demo9() {
           redisTemplate.opsForValue().set("key", "hello world");
           //value的长度
           System.out.println(redisTemplate.opsForValue().size("key"));//11
       }
       
       @Test
       public void demo10() {      
           redisTemplate.opsForValue().set("bitTest","a");
           // 'a' 的ASCII码是 97  转换为二进制是：01100001
           // 'b' 的ASCII码是 98  转换为二进制是：01100010
           // 'c' 的ASCII码是 99  转换为二进制是：01100011
           
           //因为二进制只有0和1，在setbit中true为1，false为0，因此我要变为'b'的话第六位设置为1，第七位设置为0
           redisTemplate.opsForValue().setBit("bitTest",6, true);
           redisTemplate.opsForValue().setBit("bitTest",7, false);
           System.out.println(redisTemplate.opsForValue().get("bitTest"));
           
           //判断offset处是true1还是false0
           System.out.println(redisTemplate.opsForValue().getBit("bitTest",7));
       }
   
   }
   ```

