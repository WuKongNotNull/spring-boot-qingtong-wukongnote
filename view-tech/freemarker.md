# Spring Boot 整合 Freemarker
## 集成freemarker视图


SpringBoot内部支持Freemarker 视图技术的集成，并提供了自动化配置类FreeMarkerAutoConﬁguration，借助自 动化配置可以很方便的集成Freemarker 视图到SpringBoot环境中。

**pom**

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-freemarker</artifactId>
    </dependency>
```



**修改 Freemarker 配置信息** 

> Freemarker 默认视图路径为 resources/templates 目录(由自动化配置类FreemarkerProperties 类决定)， 该目录可以在application.yml 中进行修改。

```yaml
spring:
  freemarker:
    charset: utf-8
    content-type: text/html
    # 默认是  resources/templates ,改成 resources/views
    template-loader-path: classpath:/views/
    suffix: .ftl
```



**controller**

```java
    @RequestMapping("/hello")
    public String showHello(){
            return "hello";
        }
```



**resources/views/hello.ftl**

```html
<h1>
  hello ,悟空非空也，我是freemarker模板
</h1>
```

<br><br><br>