## 集成 ehcache缓存框架

EhCache 是一个纯Java的进程内缓存框架，具有快速、精干等特点，是Hibernate中默认CacheProvider。Ehcache是一种广泛使用的开源Java分布式缓存。主要面向通用缓存,Java EE和轻量级容器。它具有内存和磁盘存储，缓存加载器,缓存扩展,缓存异常处理程序,一个gzip缓存servlet过滤器,支持REST和SOAP api等特点。

Spring 提供了对缓存功能的抽象：即允许绑定不同的缓存解决方案（如Ehcache），但本身不直接提供缓存功能的实现。它支持注解方式使用缓存，非常方便。



**ehcache 和 redis 比较**

　　ehcache直接在jvm虚拟机中缓存，速度快，效率高；但是缓存共享麻烦，集群分布式应用不方便。

　　redis是通过socket访问到缓存服务，效率比Ehcache低，比数据库要快很多，处理集群和分布式缓存方便，有成熟的方案。如果是单个应用或者对缓存访问要求很高的应用，用ehcache。如果是大型系统，存在缓存共享、分布式部署、缓存内容很大的，建议用redis。

　　ehcache也有缓存共享方案，不过是通过RMI或者Jgroup多播方式进行广播缓存通知更新，缓存共享复杂，维护不方便；简单的共享可以，但是涉及到缓存恢复，大数据缓存，则不合适。



**pom**

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-cache</artifactId>
</dependency>

<dependency>
  <groupId>net.sf.ehcache</groupId>
  <artifactId>ehcache</artifactId>
</dependency>
```



**src/main/resources/ehcache.xml**

```xml
<ehcache name="mycache">
    <!--
       diskStore：为缓存路径，ehcache分为内存和磁盘两级，此属性定义磁盘的缓存位置。参数解释如下：
       user.home – 用户主目录
       user.dir  – 用户当前工作目录
       java.io.tmpdir – 默认临时文件路径
     -->
    <diskStore path="java.io.tmpdir/Tmp_EhCache"/>
    <!--
       defaultCache：默认缓存策略，当ehcache找不到定义的缓存时，则使用这个缓存策略。只能定义一个。
     -->
    <!--
      name:缓存名称。
      maxElementsInMemory:缓存最大数目
      maxElementsOnDisk：硬盘最大缓存个数。
      eternal:对象是否永久有效，一但设置了，timeout将不起作用。
      overflowToDisk:是否保存到磁盘，当系统宕机时
      timeToIdleSeconds:设置对象在失效前的允许闲置时间（单位：秒）。
      仅当eternal=false对象不是永久有效时使用，可选属性，默认值是0，也就是可闲置时间无穷大。
      timeToLiveSeconds:设置对象在失效前允许存活时间（单位：秒）。
      最大时间介于创建时间和失效时间之间。仅当eternal=false对象不是永久有效时使用，默认是0.，也就是对象存活时间无穷大。
      diskPersistent：是否缓存虚拟机重启期数据 Whether the disk store persists between restarts of the Virtual Machine. The default value is false.
      diskSpoolBufferSizeMB：这个参数设置DiskStore（磁盘缓存）的缓存区大小。默认是30MB。每个Cache都应该有自己的一个缓冲区。
      diskExpiryThreadIntervalSeconds：磁盘失效线程运行时间间隔，默认是120秒。
      memoryStoreEvictionPolicy：当达到maxElementsInMemory限制时，Ehcache将会根据指定的策略去清理内存。默认策略是LRU（最近最少使用）。
      你可以设置为FIFO（先进先出）或是LFU（较少使用）。
      clearOnFlush：内存数量最大时是否清除。
      memoryStoreEvictionPolicy:可选策略有：LRU（最近最少使用，默认策略）、FIFO（先进先出）、LFU（最少访问次数）。
      FIFO，first in first out，这个是大家最熟的，先进先出。
      LFU， Less Frequently Used，就是上面例子中使用的策略，直白一点就是讲一直以来最少被使用的。如上面所讲，缓存的元素有一个hit属性，hit值最小的将会被清出缓存。
      LRU，Least Recently Used，最近最少使用的，缓存的元素有一个时间戳，当缓存容量满了，而又需要腾出地方来缓存新的元素的时候，
      那么现有缓存元素中时间戳离当前时间最远的元素将被清出缓存。
   -->
    <defaultCache
            eternal="false"
            maxElementsInMemory="10000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="259200"
            memoryStoreEvictionPolicy="LRU"/>

    <cache
            name="users"
            eternal="false"
            maxElementsInMemory="5000"
            overflowToDisk="false"
            diskPersistent="false"
            timeToIdleSeconds="1800"
            timeToLiveSeconds="1800"
            memoryStoreEvictionPolicy="LRU"/>

</ehcache>
```



**application.xml**

```yaml
# 缓存的配置
spring:
  cache:
    ehcache:
      config: classpath:ehcache.xml
      
# 开启dao层的dubug日志，打印出sql语句，便于缓存测试分析
logging:
	level:
		com.wukong.dao: debug
		
```



**实体类序列化**

```java
public class User implements Serializable {
}
```



**启动类**

```java
@SpringBootApplication

@EnableCaching
public class App
{
    public static void main( String[] args )
    {
        SpringApplication.run(App.class);
        System.out.println( "Hello World!" );
    }
}
```



**service层设置缓存**

```java
@Service
public class UserServiceImpl implements UserService {

    @Cacheable(value = "users", key = "#username")
    public User getUserByUsername(String username) {
       
    }
  

    @Cacheable(value = "users",key = "#id")
    public User getUserById(Integer id)  {
        
    }

		// 添加用户，不需要添加到缓存容器中
    public void addUser(User user) {

    }

  
  	// 修改用户，返回类型不能是void，
  	// 参数是对象是，使用 #user.id 作为关键词
  	// @CachePut 作用：修改数据库的同时，同步修改缓存容器
  	// 当然也可以直接使用@CacheEvict ,修改数据库同时删除缓存容器中该数据
    @CachePut(value = "users", key = "#user.id")
    public User modifyUser(User user) {

        return user;
    }
	
  	// @CacheEvict 作用： 删除数据库的同时，删除缓存容器
    @CacheEvict(value = "users", allEntries = true)
    public void deleteUser(Integer id) {
       

    }
  
		//参数是封装对象时，可以使用多个key ，写法如下
    @Cacheable(value = "users" ,key = "#userParams.pageNum+'-'+#userParams.pageSize+'-'+#userParams.username")
    public PageInfo<User> getUserPageList(UserParams userParams) {

    }
```



