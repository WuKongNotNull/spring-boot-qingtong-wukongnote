## Redis介绍

**什么是Redis**

Redis 是一个开源（BSD许可）的、内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件，并提供多种语言的API。

**优点**

1.存取速度快：Redis速度非常快，每秒可执行大约110000次的设值操作，或者执行81000次的读取操作。

2.支持丰富的数据类型：Redis支持开发人员常用的大多数数据类型，例如列表、集合、排序集和散列等。

3.操作具有原子性：所有Redis操作都是原子操作，这确保如果两个客户端并发访问，Redis服务器能接收更新后的值。

4.提供多种功能：Redis提供了多种功能特性，可用作非关系型数据库、缓存中间件、消息中间件等。



**redis下载与安装**

下载路径

https://github.com/microsoftarchive/redis/releases/tag/win-3.2.100

<img src="../../../springboot/springboot笔记.assets/image-20200629130139745.png" alt="image-20200629130139745" style="zoom:50%;" />

解压后放入磁盘某位置就安装成功（免安装）

安装可视化客户端

![image-20200629155409332](../img/image-20200629155409332-5921678.png)





## 使用Spring Boot整合Redis

1.在pom文件中添加Spring Data Redis依赖启动器

```properties
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

```



2.在全局配置文件application.properties中添加Redis数据库连接配置

```properties
spring.redis.host=127.0.0.1
spring.redis.port=6379
spring.redis.password=

```



3.编写实体类

```java
@RedisHash("persons")  
public class Person {
    @Id        
         private String id; //主键是string类型
    @Indexed  
         private String firstname;
    @Indexed
         private String lastname;
         private Address address;
         private List<Family> familyList;//家人
}

```

```java
public class Address {
	@Indexed
	private String city;
	@Indexed
	private String country;
}

```

```java
public class Family {
	@Indexed
	private String type;
	@Indexed
	private String username;}

```



4.编写Repository接口

```java
public interface PersonRepository extends CrudRepository<Person, String> {//（实体类，主键类型）
List<Person> findByLastname(String lastname);
Page<Person> findPersonByLastname(String lastname, Pageable page);
List<Person> findByFirstnameAndLastname(String firstname, String lastname);
List<Person> findByAddress_City(String city);
List<Person> findByFamilyList_Username(String username);
}

```





5.编写单元测试进行接口方法测试以及整合测试

```java
   @Autowired
    public PersonRepository personRepository;

    @Test
    public void selectPerson() {
      List<Person> list = repository.findByAddress_City("北京");
      System.out.println(list);
    
  
    @Test
     void addTest(){
            Person person=new Person();
            person.setId("p001");
        Address address = new Address();
        address.setCity("江苏省");
        address.setCountry("南京市");
        person.setAddress(address);

        List<Family> familyList=new ArrayList<>();
        Family family=new Family();
        family.setType("father");
        family.setUsername("杨康");

        Family family2=new Family();
        family2.setType("mother");
        family2.setUsername("穆念慈");

        familyList.add(family);
        familyList.add(family2);

        person.setFamilyList(familyList);
        person.setLastName("杨");
        person.setFirstName("过");

        Person person1 = personRepository.save(person);
        System.out.println("person1=====>"+person1);
    }
    
    
    
    
    }

```

