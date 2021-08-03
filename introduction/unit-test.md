## 单元测试

**pom**

```xml
   <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
    </dependency>
```





## **service 层单元测试**



```java
@SpringBootTest
class MySpringbootApplicationTests {

    @Autowired
    private HelloController helloController;

    @Test
    public void contextLoads() {
        String hello = helloController.hello();
        System.out.println(hello);

    }

}

```



## **controller层单元测试**

视图层代码使用MockMvc 进行测试，这里以UserCntroller 为例,src/test/java 目录下添加测试包

 com.wukong.controller 定义测试类代码如下:

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = {Starter.class})
@AutoConfigureMockMvc
public class TestUserController {
private Logger log = LoggerFactory.getLog ler.class);
@Autowired
private MockMvc mockMvc;
//用户列表查询
@Test
public void apiTest01()t
MvcResult mvcResu ders.get("/user/list")).
andExp Ok()).andReturn();
log.info("响 Status());
log.info(" getContentAsString());;
}
// 用户名记录查询
@Test
public void apiTest02()t
MvcResult
mvcResult=mockMvc.perform(MockMvcR tBuilders.get("/user/uname/admin")).
andExpect(MockMvcResultMatchers.status().isOk()).andReturn();
log.info("响应状态:{}",mvcResult.getResponse().getStatus());
log.info("响应内容:{}",mvcResult.getResponse().getContentAsString());;
}
}

         @RunWith(SpringRunner.class)
@SpringBootTest(classes = {Starter.class})
@AutoConfigureMockMvc
public class TestUserController {
private Logger log = LoggerFactory.getLog ler.class);
@Autowired
private MockMvc mockMvc;
//用户列表查询
@Test
public void apiTest01()t
MvcResult mvcResu ders.get("/user/list")).
andExp Ok()).andReturn();
log.info("响 Status());
log.info(" getContentAsString());;
}
// 用户名记录查询
@Test
public void apiTest02()t
MvcResult
mvcResult=mockMvc.perform(MockMvcR tBuilders.get("/user/uname/admin")).
andExpect(MockMvcResultMatchers.status().isOk()).andReturn();
log.info("响应状态:{}",mvcResult.getResponse().getStatus());
log.info("响应内容:{}",mvcResult.getResponse().getContentAsString());;
}
}

```

