# Spring Boot jar包方式打包部署

## **maven打包插件**

```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
```

<br>

## **打包**

> 步骤 ： +    --->    command line: package 

![image-20210831151820294](https://tva1.sinaimg.cn/large/008i3skNgy1gu007gduy7j61ok0u043002.jpg)







<br>



## **找到保存路径**

<img src="../img/image-20200629185556075.png" alt="image-20200629185556075" style="zoom:50%;" />

<br>



## **运行jar包后直接访问**



`java -jar target\myspringboot-mybatis-0.0.1-SNAPSHOT.jar`



<br>

> **注意**
> 使用 java 指令，需要配置java的环境变量





<br><br><br>