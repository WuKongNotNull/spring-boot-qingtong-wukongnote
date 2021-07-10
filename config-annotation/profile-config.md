## 使用profile文件进行多环境配置



多环境配置文件格式

<img src="../img/1593095923921.png" alt="1593095923921" style="zoom:50%;" />





方法一：使用命令行方式激活指定环境的配置文件

<img src="../img/1593098529499.png" alt="1593098529499" style="zoom:50%;" />



ctrl+c  服务器运行退出

<img src="../img/1593099959623.png" alt="1593099959623" style="zoom:50%;" />

ctrl+c  停止



方法二：在全局配置文件application.properties设置 spring.profiles.active属性激活

```properties
spring.profiles.active=test
```



2.9 使用@Profile注解进行多环境配置

编写不同环境下的配置类

<img src="../img/1593102190282.png" alt="1593102190282" style="zoom:33%;" />

```java
package com.siliang.config;

/**
 * @Author: 悟空非空也（公众号/B站/知乎）
 */
public interface DBConnector {

    public void configuration();

}

```

```java
package com.siliang.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

/**
 * @Author: 悟空非空也（公众号/B站/知乎）
 */
@Configuration
@Profile(value ="developer" )
public class DeveloperDBConnector implements DBConnector {

    @Override
    public void configuration() {
        System.out.println("开发环境===>连接数据库");
    }
}

```

```java
package com.siliang.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

/**
 * @Author: 悟空非空也（公众号/B站/知乎）
 */
@Configuration
@Profile(value = "product")
public class ProductDBConnector implements DBConnector {
    @Override
    public void configuration() {
        System.out.println("生产环境====》连接数据库");
    }
}

```

```java
package com.siliang.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;

/**
 * @Author: 悟空非空也（公众号/B站/知乎）
 */

@Configuration
@Profile(value = "test")
public class TestDBConnector implements  DBConnector {
    @Override
    public void configuration() {
        System.out.println("测试环境下===》连接数据库");
    }
}

```



在全局配置文件application.properties中指定使用哪个环境

```properties
spring.profiles.active=test
#或者
#spring.profiles.active=product
#或者
#spring .profiles.active=develop
```



测试

```java
  @Autowired
    private DBConnector dbConnector;

    @Test
    public void  dbConnectorTest(){
        dbConnector.configuration();
    }
```

