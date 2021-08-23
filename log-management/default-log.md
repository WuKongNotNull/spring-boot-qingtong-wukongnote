# 默认日志管理
## 默认日志框架 logback
java web 下有好几种日志框架，比如：logback，log4j，log4j2（slj4f 并不是一种日志框架，它相当于定义了规范，实现了这个规范的日志框架就能够用 slj4f 调用）。其中性能最高的应该使 logback 了，而且 springboot 默认使用的也是 logback 日志。默认情况下 Spring Boot 将 info 级别的日志输出到控制台中，不会写到日志文件，且不能进行复杂配置。



```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

  Logger logger = LoggerFactory.getLogger(App.class);
        logger.info("我是 springboot的默认日志系统.....");
```



**日志的复杂配置**

写到日志文件中。如果要编写除控制台输出之外的日志文件，则需要application.yml中设置logging.file 或者logging.path属性。

```yaml
logging:
  file:
    # 项目路径下的log目录下的spring.log 文件（默认名为 spring.log）
    path: ./log
    # 自定义日志名，存放在项目路径下 （name 优先级高于 path）
    name: ./resources.log

```

<br><br><br>

