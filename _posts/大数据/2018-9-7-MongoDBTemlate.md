---
tags: [NoSQL]
---
## <center>MongoDBTemlate</center>

#### 1.单个操作

##### 1.1增加

```java
// 方式一：不支持批量操作，若主键存在则会对当前存在的数据进行修改操作
public void saveIdtfCode(IdtfCodePO idtfCode) {
  	// 保存Object到指定的collection
	mongoTemplate.save(idtfCode, ParaConstants.IDTF_RECORD_COLLECTION);
}

// 方式二：支持批量操作，效率高，若主键已存在则会抛出提示主键重复的异常
public void insertAddressList(AddressList addressList) {
	mongoTemplate.insert(addressList, "addressList");
}
```

##### 1.2删除

```java
public void deleteAdAddressList(AddressList addressList) {
	Query query = new Query(Criteria.where("userId").is(addressList.getUserId()));
  	// 根据query条件删除指定collection的数据
	mongoTemplate.remove(query, ParaConstants.IDTF_RECORD_COLLECTION);
}

```

##### 1.3查找

```java
import org.springframework.data.mongodb.core.query.Query;
public String getKeyById(UserPO user) {
	Query query = new Query(Criteria.where(SymbolConstants.APPID).is(user.getAppId()));
  	// 根据查询条件query，查找指定collection中的数据
	List<UserPO> list = mongoTemplate.find(query, UserPO.class, ParaConstants.USER_COLLECTION);
	return list.get(0).getSecretKey();
}
```

##### 1.4修改

```java
@Override
public void updateIdtfCode(IdtfCodePO idtfCode, String paraName) {
	Query query = new Query(Criteria.where(SymbolConstants.MSGID).is(idtfCode.getMsgId()));
  	// 指定更新的参数名和参数值
	Update update = new Update().set(paraName, value);
	// 更新查询返回结果集的第一条，IdtfCodePO.class是指定数据保存的格式
	mongoTemplate.findAndModify(query, update, IdtfCodePO.class, ParaConstants.IDTF_RECORD_COLLECTION);
}
```

#### 2.批量操作

```java
// 对指定collection进行批量操作（仅支持insert、remove、update操作）。UNORDERED表示并行执行
BulkOperations bulkOps = mongoTemplate.bulkOps(BulkOperations.BulkMode.UNORDERED, COLLECTION);
Query query = null;
Update update = null;
AuthDeveloper authDeveloper = null;
// 循环修改
for (int i = 0; i < authDevelopers.size(); ++i) {
	authDeveloper = authDevelopers.get(i);
	query = new Query(Criteria.where("workNumber").is(authDeveloper.getWorkNumber()));
	update = new Update().set("authorityList", authDeveloper.getAuthorityList());
	bulkOps.updateOne(query, update);
}
// 批量提交执行
bulkOps.execute();
```

####3.创建MongoTemplate

引入pom配置

```properties
<dependency>
	<groupId>org.mongodb</groupId>
	<artifactId>mongo-java-driver</artifactId>
	<version>3.5.0</version>
</dependency>
<dependency>
	<groupId>org.springframework.data</groupId>
	<artifactId>spring-data-mongodb</artifactId>
	<version>2.0.0.RELEASE</version>
</dependency> 
```

##### 3.1Spring Boot的创建方式

在application.properties中进行配置

```properties
spring.data.mongodb.host=47.106.122.242
spring.data.mongodb.port=27017
spring.data.mongodb.database=SMSPLATFORM
spring.data.mongodb.username=root
spring.data.mongodb.password=root
```

自动注入

```java
@Autowired
private MongoTemplate mongoTemplate;
```

##### 3.2一般创建方式

配置数据源

```java
 /**
  * 此种方式还可以配置多个数据源，此处演示配置多个数据源
  */
// 第一个数据源，通过name放入容器
@Bean(name="primaryMongoTemplate")
public MongoDbFactory primaryMongoDbFactory(){
	ServerAddress serverAddress = new ServerAddress("host", "port");
	List<MongoCredential> credentialsList = new ArrayList<MongoCredential>();
	credentialsList.add(MongoCredential.createCredential("username", "database", "password".toCharArray()));
	return new SimpleMongoDbFactory(new MongoClient(serverAddress, credentialsList),database);
}

// 第一个数据源，通过name放入容器
@Bean(name="secondMongoTemplate")
public MongoDbFactory secondMongoDbFactory(){
	ServerAddress serverAddress = new ServerAddress("host", "port");
	List<MongoCredential> credentialsList = new ArrayList<MongoCredential>();
	credentialsList.add(MongoCredential.createCredential("username", "database", "password".toCharArray()));
	return new SimpleMongoDbFactory(new MongoClient(serverAddress, credentialsList),database);
}
```

自动注入

```java
@Resource(name="primaryMongoTemplate")
private MongoTemplate primaryMongoTemplate;

@Resource(name="secondMongoTemplate")
private MongoTemplate secondMongoTemplate;
```









