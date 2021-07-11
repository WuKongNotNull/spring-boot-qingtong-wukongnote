

## 内存身份认证

```java
package com.wukongnotnull.config;

import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.authentication.configurers.provisioning.InMemoryUserDetailsManagerConfigurer;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    //重写 configure方法

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //设置密码编码器
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();

      // 内存身份认证，模拟测试用户  InMemoryUserDetailsManagerConfigurer<AuthenticationManagerBuilder> builder = auth.inMemoryAuthentication().passwordEncoder(bCryptPasswordEncoder);
        builder.withUser("lisi").password(bCryptPasswordEncoder.encode("123")).roles("common");
        builder.withUser("李四").password(bCryptPasswordEncoder.encode("123")).roles("vip");
    }
}

```



## 7.5JDBC身份认证

sql脚本

```sql
#use database
USE springbootdata;
#create t_customer 并插入相关数据
DROP TABLE IF EXISTS `t_customer`;
CREATE TABLE t_customer (
	`id` INT(20) NOT NULL AUTO_INCREMENT PRIMARY KEY,
	username VARCHAR(200),
	`password` VARCHAR(200),
	`valid` TINYINT(1) NOT NULL DEFAULT '1'
);
INSERT INTO t_customer VALUES(
	'1','lisi','123','1'
),(
	'2','李四','123','1'
);
#创建表 t_authority 并插入相干数据
DROP TABLE IF EXISTS `t_authority`;
CREATE TABLE `t_authority`(
	id INT(20) NOT NULL AUTO_INCREMENT PRIMARY KEY,
	authority VARCHAR(20)
	

);
INSERT INTO `t_authority` VALUES(
	1,'ROLE_common'
),(2,'ROLE_vip');

#创建 表 t_customer_authority，并插入数据
DROP TABLE IF EXISTS `t_customer_authority`;
CREATE TABLE `t_customer_authority`(
	id INT(20) NOT NULL AUTO_INCREMENT PRIMARY KEY,
	customer_id INT(20),
	authority_id INT(20)
);
INSERT INTO `t_customer_authority` VALUES(
	'1','1','1'
),(
	'2','2','2'
);

```



配置mysql连接和redis连接

```xml
# MySQL\u6570\u636E\u5E93\u8FDE\u63A5\u914D\u7F6E
spring.datasource.url=jdbc:mysql://localhost:3306/springbootdata?serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root

# Redis\u670D\u52A1\u5668\u5730\u5740
spring.redis.host=127.0.0.1
# Redis\u670D\u52A1\u5668\u8FDE\u63A5\u7AEF\u53E3
spring.redis.port=6379
# Redis\u670D\u52A1\u5668\u8FDE\u63A5\u5BC6\u7801\uFF08\u9ED8\u8BA4\u4E3A\u7A7A\uFF09
spring.redis.password=

# thymeleaf\u9875\u9762\u7F13\u5B58\u8BBE\u7F6E\uFF08\u9ED8\u8BA4\u4E3Atrue\uFF09\uFF0C\u5F00\u53D1\u4E2D\u65B9\u4FBF\u8C03\u8BD5\u5E94\u8BBE\u7F6E\u4E3Afalse\uFF0C\u4E0A\u7EBF\u7A33\u5B9A\u540E\u5E94\u4FDD\u6301\u9ED8\u8BA4true
spring.thymeleaf.cache=false
```



添加依赖

```xml
 <!--jdbc数据库连接启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!--mysql 数据库连接驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
```

编写安全配置类

```java
package com.wukong.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.authentication.configurers.provisioning.InMemoryUserDetailsManagerConfigurer;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

import javax.sql.DataSource;

@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private DataSource dataSource;

    //重写 configure方法

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        //设置密码编码器
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();

        //使用jdbc进行身份认证
        String userSQL ="select username,password,valid from t_customer " +
                   "where username = ?";
           String authoritySQL="select c.username,a.authority from t_customer c,t_authority a,"+
                   "t_customer_authority ca where ca.customer_id=c.id " +
                   "and ca.authority_id=a.id and c.username =?";
           auth.jdbcAuthentication().passwordEncoder(bCryptPasswordEncoder)
                   .dataSource(dataSource)
                   .usersByUsernameQuery(userSQL)
                   .authoritiesByUsernameQuery(authoritySQL);

    }
}

```

测试

用户名：lisi 密码：123

密码可以通过 Bcrypt生成器生成

<img src="../../../springboot/springboot笔记.assets/image-20200711203938490.png" alt="image-20200711203938490" style="zoom:25%;" />

<img src="../../../springboot/springboot笔记.assets/image-20200711203828048.png" alt="image-20200711203828048" style="zoom:25%;" />



## 7.6UserDetailService身份认证

pom

```java
<!-- Security与Thymeleaf整合实现前端页面安全访问控制 -->
        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-springsecurity5</artifactId>
        </dependency>


        <!--redis-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <!-- Spring Data JPA操作数据库  -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <!--jdbc数据库连接启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <!--mysql 数据库连接驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <!--启动安全管理-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
```



domain: Authority

```java
package com.wukong.domain;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import java.io.Serializable;

@Entity(name = "t_authority")
public class Authority implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private  Integer id;
    private  String authority;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getAuthority() {
        return authority;
    }

    public void setAuthority(String authority) {
        this.authority = authority;
    }

    @Override
    public String toString() {
        return "Authority{" +
                "id=" + id +
                ", authority='" + authority + '\'' +
                '}';
    }
}

```

Customer

```java
package com.wukong.domain;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import java.io.Serializable;
@Entity(name = "t_customer")
public class Customer implements  Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private  Integer id;
    private  String username;
    private  String password;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }
}

```

repository: AuthoritiyRepository

```java
package com.wukong.repository;

import com.wukong.domain.Authority;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;

import java.util.List;

public interface AuthorityRepository extends JpaRepository<Authority,Integer> {
    @Query(value = "select a.* from t_customer c,t_authority a,t_customer_authority ca where ca.customer_id=c.id and ca.authority_id=a.id and c.username =?1",nativeQuery = true)
    public List<Authority> findAuthoritiesByUsername(String username);
}

```

CustomerRepository

```java
package com.wukong.repository;

import com.wukong.domain.Customer;
import org.springframework.data.jpa.repository.JpaRepository;

public interface CustomerRepository extends JpaRepository<Customer,Integer> {

    Customer findByUsername(String username);


}

```

service:

CustomerService

```java
package com.wukong.service;

import com.wukong.domain.Authority;
import com.wukong.domain.Customer;
import com.wukong.repository.AuthorityRepository;
import com.wukong.repository.CustomerRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * 对用户数据结合Redis缓存进行业务处理
 */
@Service
public class CustomerService {
    @Autowired
    private CustomerRepository customerRepository;
    @Autowired
    private AuthorityRepository authorityRepository;
    @Autowired
    private RedisTemplate redisTemplate;

    //业务控制：使用唯一用户名称查询用户信息
    public Customer getCustomer(String username){
        Customer customer=null;
        Object o = redisTemplate.opsForValue().get("customer_"+username);
        if(o!=null){
            customer=(Customer)o;
        }else {
            customer = customerRepository.findByUsername(username);
            if(customer!=null){
                redisTemplate.opsForValue().set("customer_"+username,customer);
            }
        }
        return customer;
    }

    //业务控制：使用唯一用户名查询用户权限
    public List<Authority> getCustomerAuthority(String username){
        List<Authority> authorities=null;
        Object o = redisTemplate.opsForValue().get("authorities_" + username);
        if(o!=null){
            authorities=(List<Authority>) o;
        }else {
            authorities=authorityRepository.findAuthoritiesByUsername(username);
            if(authorities.size()>0){
                redisTemplate.opsForValue().set("authorities_"+username,authorities);
            }
        }
        return authorities;
    }



}

```

UserDetailsServiceImpl

```java
package com.wukongnotnull.service;

import com.wukongnotnull.domain.Authority;
import com.wukongnotnull.domain.Customer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

/**
 * 自定义一个UserDetailService接口实现类进行用户认证信息封装
 */
@Service
public class UserDetailsServiceImpl implements UserDetailsService {

    @Autowired
    private  CustomerService customerService;


    @Override
    public UserDetails loadUserByUsername(String s) throws UsernameNotFoundException {
       //通过业务方法获取用户及权限信息
        Customer customer = customerService.getCustomer(s);
        List<Authority> authorities = customerService.getCustomerAuthority(s);
        // 对用户权限进行封装
        List<SimpleGrantedAuthority> list = authorities.stream().
                map(authority -> new SimpleGrantedAuthority(authority.getAuthority())).collect(Collectors.toList());
        // 返回封装的UserDetails用户详情类
        if(customer!=null){
            UserDetails userDetails= new User(customer.getUsername(),customer.getPassword(),list);
            return userDetails;
        } else {
            // 如果查询的用户不存在（用户名不存在），必须抛出此异常
            throw new UsernameNotFoundException("当前用户不存在！");
        }


    }
}

```

测试

登录