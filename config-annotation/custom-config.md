# 使用 @PropertySource 加载自定义配置文件

## @PropertySource 源码
```java
public @interface PropertySource {

	/**
	 * Indicate the resource location(s) of the properties file to be loaded.
	 * <p>Both traditional and XML-based properties file formats are supported
	 * &mdash; for example, {@code "classpath:/com/myco/app.properties"}
	 * or {@code "file:/path/to/file.xml"}.
	 * <p>Resource location wildcards (e.g. *&#42;/*.properties) are not permitted;
	 * each location must evaluate to exactly one {@code .properties} or {@code .xml}
	 * resource.
	 * <p>${...} placeholders will be resolved against any/all property sources already
	 * registered with the {@code Environment}. See {@linkplain PropertySource above}
	 * for examples.
	 * <p>Each location will be added to the enclosing {@code Environment} as its own
	 * property source, and in the order declared.
	 */
	String[] value();

```

## 案例


自定义配置文件<br>
test.properties

```properties

#自定义配置文件
test.id=1
test.name=wukongnotnull

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

@Configuration//指定当前类为配置类
@EnableConfigurationProperties(MyProperties.class)//开启配置文件的属性注入功能
@PropertySource(value="classpath:test.properties")//指定自定义配置文件的来源
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





# 使用@ImportResource加载自定义的 spring xml 配置文件

## @ImportResource 源码
```java

public @interface ImportResource {

	/**
	 * Resource locations from which to import.
	 * <p>Supports resource-loading prefixes such as {@code classpath:},
	 * {@code file:}, etc.
	 * <p>Consult the Javadoc for {@link #reader} for details on how resources
	 * will be processed.
	 * @since 4.2
	 * @see #value
	 * @see #reader
	 */
	@AliasFor("value")
	String[] locations() default {};
```
## 案例
创建 spring xml 配置文件  
myBeans.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="myPet" class="com.wukongnotnull.domain.Pet"/>

</beans>
```  

<br>

启动类上添加 @ImportResource 注解

```java

package com.wukongnotnull;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ImportResource;

@SpringBootApplication
@ImportResource(value = "classpath:myBeans.xml")
public class InitializrProjectApplication {

    public static void main(String[] args) {

        SpringApplication.run(InitializrProjectApplication.class, args);

    }

}

```



test

```java

@SpringBootTest
class InitializrProjectApplicationTests {

    @Autowired
    private ApplicationContext applicationContext;

    @Test
    void beansTest(){
        Pet myPet =(Pet) applicationContext.getBean("myPet");
        System.out.println("myPet----->" + myPet);
    }

}


```



# 使用 @Configuration 编写自定义配置类

## @Configuration 源码

```java

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {

	/**
	 * Explicitly specify the name of the Spring bean definition associated with the
	 * {@code @Configuration} class. If left unspecified (the common case), a bean
	 * name will be automatically generated.
	 * <p>The custom name applies only if the {@code @Configuration} class is picked
	 * up via component scanning or supplied directly to an
	 * {@link AnnotationConfigApplicationContext}. If the {@code @Configuration} class
	 * is registered as a traditional XML bean definition, the name/id of the bean
	 * element will take precedence.
	 * @return the explicit component name, if any (or empty String otherwise)
	 * @see AnnotationBeanNameGenerator
	 */
	@AliasFor(annotation = Component.class)
	String value() default "";

	/**
	 * Specify whether {@code @Bean} methods should get proxied in order to enforce
	 * bean lifecycle behavior, e.g. to return shared singleton bean instances even
	 * in case of direct {@code @Bean} method calls in user code. This feature
	 * requires method interception, implemented through a runtime-generated CGLIB
	 * subclass which comes with limitations such as the configuration class and
	 * its methods not being allowed to declare {@code final}.
	 * <p>The default is {@code true}, allowing for 'inter-bean references' via direct
	 * method calls within the configuration class as well as for external calls to
	 * this configuration's {@code @Bean} methods, e.g. from another configuration class.
	 * If this is not needed since each of this particular configuration's {@code @Bean}
	 * methods is self-contained and designed as a plain factory method for container use,
	 * switch this flag to {@code false} in order to avoid CGLIB subclass processing.
	 * <p>Turning off bean method interception effectively processes {@code @Bean}
	 * methods individually like when declared on non-{@code @Configuration} classes,
	 * a.k.a. "@Bean Lite Mode" (see {@link Bean @Bean's javadoc}). It is therefore
	 * behaviorally equivalent to removing the {@code @Configuration} stereotype.
	 * @since 5.2
	 */
	boolean proxyBeanMethods() default true;

}


```

## 案例

编写配置类

```java

package com.wukongnotnull.config;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
        /**
         * @Author: 悟空非空也（公众号/B站/知乎）
         */
@Configuration  //定义该类为配置类
public class MyConfig {

    @Bean(name = "aPet")
    public MyService getMyService(){
        return new Pet();
    }
}

```
<br>

```java

public class Pet {
    // properties 
}

```

<br>

测试

```java

    @Autowired
    private ApplicationContext applicationContext;

    @Test
    public void myServiceTest(){
        Pet aPet = (Pet)applicationContext.getBean("aPet");
        System.out.println(aPet);
    }

```

<br>
<br>
<br>
<br>



