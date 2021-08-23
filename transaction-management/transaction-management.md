# 事务管理



Spring Boot 集成了Mybatis框架，Mybatis底层数据访问层实现基于jdbc 来实现，所以在Spring Boot 环境下对 事物进行控制，事务实现由Spring Boot实现并自动配置，在使用时通过注解方式标注相关方法加入事务控制即可



```java
@Service
public class UserServiceImpl implements UserService {

    @Resource
    private UserMapper userMapper;

		//增删改上添加 该注解即可 （查询不需要）
    @Transactional(propagation = Propagation.REQUIRED)
    public void addUser(User user) {
				......
        ......  

    }
```


<br><br><br>
