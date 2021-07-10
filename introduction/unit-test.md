# 单元测试









# 案例

**pom**

```xml
   <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
    </dependency>
```



**test**

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

## 