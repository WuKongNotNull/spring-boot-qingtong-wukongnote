### 参数间引用

application.properties

```properties
#参数间的引用
app.name=shopping
app.description=${app.name} is a buying spring boot application

jack.age=${random.int[10,20]}
jack.description=jack 的年龄 可能是 ${jack.age} 
```



test

```java
@SpringBootTest
class MySpringboot2ApplicationTests {

    @Value("${jack.description}")
    private String str;

    @Test
    void contextLoads() {
        System.out.println(str);
    }
}
```


