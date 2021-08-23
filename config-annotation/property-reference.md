# yml 高级玩法
## 参数间引用

application.properties

```properties

# 参数间的引用
app.name=wukongnotnull
app.description=${app.name} is a technology blog website, welcome your comming.

wukong.age=${random.int[10,20]}
wukong.description=悟空非空也的年龄可能是 ${wukong.age} 

```

<br>

test

```java
@SpringBootTest
class MySpringbootApplicationTests {

    @Value("${wukong.description}")
    private String authorDes;

    @Test
    void contextLoads() {
        System.out.println(authorDes);
    }
}
```


<br>
<br>
<br>
