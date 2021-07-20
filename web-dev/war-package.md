## war包方式打包部署

**pom.xml**

```xml
 <packaging>war</packaging>
```

<br>



**修改启动类**

```java
@SpringBootApplication
public class MyspringbootMybatisApplication extends SpringBootServletInitializer {

    //继承SpringbootServletInitializer ,重写configure方法
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(MyspringbootMybatisApplication.class);
    }

    public static void main(String[] args) {
        SpringApplication.run(MyspringbootMybatisApplication.class, args);
    }

}

```

<br>

**打包**

<img src="../img/image-20200629195527399.png" alt="image-20200629195527399" style="zoom:50%;" />



<br>



**部署到外部的tomcat的webapp内**

<img src="../img/image-20200629195656565.png" alt="image-20200629195656565" style="zoom:50%;" />



<br>

**启动tomcat部署成功**

bin\startup.bat指令

<img src="../img/image-20200629195757542.png" alt="image-20200629195757542" style="zoom:33%;" />



<br>

**注意**

Jar 包内嵌服务器（Tomcat），war包不含服务器，需要外部服务器才能运行



<br><br>

<br>