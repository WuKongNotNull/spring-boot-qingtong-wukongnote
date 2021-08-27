# Spring Boot 整合 AOP
## AOP

在SpringBoot中默认是开启AOP功能的

~~~~yml
spring:
  aop:
    auto: true
~~~~

<br>

## 使用步骤

### 添加依赖

~~~~xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
~~~~

<br>

### 自定义注解

~~~~java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface InvokeLog {
}

~~~~

<br>

### 定义切面类

~~~~java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@Aspect  //标识这是一个切面类
@Component
public class InvokeLogAspect {
  
  	public static final Logger logger = LoggerFactory.getLogger(InvokeLogAspect.class);

    //指定哪些连接点为切点
    @Pointcut("@annotation(com.wukongnotnull.aop.InvokeLog)")
    public void pc(){
    }
		
  	// 在切点出添加什么增强，增强方式是什么？
    @Around("cp()")
    public Object printInvokeLog(ProceedingJoinPoint joinPoint){
         //目标方法调用前
        Object proceed = null;
        MethodSignature signature = (MethodSignature) joinPoint.getSignature();
        String methodName = signature.getMethod().getName();
       logger.info("enter this method--->"+methodName);
        try {
            proceed = joinPoint.proceed();
            //目标方法调用后
            logger.info("leave this method--->"+methodName);
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        return proceed;
    }
}

~~~~

<br>

### 增加自定义注解

~~~~Java
@Service
public class UserServiceImpl implements UserServcie {

    @Autowired
    private UserMapper userMapper;

    @Override
    @InvokeLog  //添加自定义注解
    public List<User> findAll() {
        return userMapper.findAll();
    }
}
~~~~

<br>

### 单元测试

```java
    @Test
    void findAll() {
        userService.findAll();
    }
```





## 切换动态代理

有的时候我们需要修改AOP的代理方式。

我们可以使用以下方式修改：

在配置文件中配置spring.aop.proxy-target-class为false这为使用jdk动态代理。该配置默认值为true，代表使用cglib动态代理。

~~~~java
@SpringBootApplication
@EnableAspectJAutoProxy(proxyTargetClass = false)//修改代理方式
public class WebApplication {
    public static void main(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(WebApplication.class, args);
    }
}
~~~~

如果想生效还需要在配置文件中做如下配置

~~~~yml
spring:
  aop:
    proxy-target-class: false #切换动态代理的方式
~~~~



<br><br><br>

