## 使用 @PropertySource 加载自定义配置文件



自定义配置文件test.properties

```properties
#自定义配置文件
test.id=1
test.name=testzhangsan
```



实体类

```java
package com.wukongnotnull.myspringboot.domain;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.boot.context.properties.EnableConfigurationProperties;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

/**
 * @Author: 悟空非空也（公众号/B站/知乎）
 */
//注意：可以使用@Component代替
//@Configuration+@EnableConfigurationProperties(MyProperties.class)

@Configuration//指定当前类为配置类，
@PropertySource("classpath:test.properties")//指定自定义配置文件的来源
@EnableConfigurationProperties(MyProperties.class)//开启配置文件的属性注入功能
@ConfigurationProperties(prefix = "test")
public class MyProperties {

    private  int id;
    private  String name;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "MyProperties{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}

```



测试

```java
@SpringBootTest
class MySpringbootApplicationTests {

    @Autowired
    private MyProperties myProperties;

    @Test
    public void myPropertiesTest(){
        System.out.println("myProperties===>"+myProperties);
    }

}
```





## 使用@ImportResource加载自定义的xml配置文件(不推荐使用)

创建xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myService" class="com.siliang.myspringboot.config.MyService"/>

</beans>
```



启动类上添加@ImportResource注解

```java
package com.siliang.myspringboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ImportResource;

@SpringBootApplication
@ImportResource("classpath:bean.xml")
public class MySpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(MySpringbootApplication.class, args);
    }
}
```



test

```java
@SpringBootTest
class MySpringbootApplicationTests {

   //此处注入的是这个类
    @Autowired
    private ApplicationContext applicationContext;

    @Test
    public void myServiceTest(){
        MyService myService = (MyService)applicationContext.getBean("myService");
        System.out.println(myService);
    }

```



## 使用@Configuration编写自定义配置类



编写配置类

```java
package com.siliang.myspringboot.config;


import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Author: 悟空非空也（公众号/B站/知乎）
 */
@Configuration  //定义该类为 配置类
public class MyConfig {

    @Bean(name = "myService2")
    public MyService getMyService(){
        return new MyService();
    }
}

```

```java
public class MyService {
}

```



测试

```java
 @Autowired
    private ApplicationContext applicationContext;

    @Test
    public void myServiceTest(){
        MyService myService = (MyService)applicationContext.getBean("myService2");
        System.out.println(myService);
    }

```



