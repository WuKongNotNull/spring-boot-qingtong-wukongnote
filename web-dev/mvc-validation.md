# Spring Boot 整合数据校验
## springMVC 数据校验 validation



日常项目开发中，对于前端提交的表单，后台接口接收到表单数据后，为了程序的严谨性，通常后端会加入业务参数 的合法校验操作来避免程序的非技术性bug，这里对于客户端提交的数据校验，SpringBoot通过spring-boot-starter-validation 模块包含了数据校验的工作。 

相关概念如下

JSR303/JSR-349: JSR303是一项标准,只提供规范不提供实现，规定一些校验规范即校验注解，如@Null， @NotNull，@Pattern，位于javax.validation.constraints包下。JSR-349是其升级版本，添加了一些新特性。 

Hibernate Validation：

Hibernate Validation是对这个规范的实现，并增加了一些其他校验注解，如@Email， @Length，@Range等等 。

Spring Validation：Spring Validation对Hibernate Validation进行了二次封装，在Spring Mvc模块中添加了自 动校验，并将校验信息封装进了特定的类





**pom**

> 实现参数校验，程序必须引入spring-boot-starter-validation 依赖.
>
> 在版本2.2.2中，引入spring-boot-starter-web依赖时，自动引入依赖spring-boot-starter-validation。
>
> 因为 spring-boot-starter-web 依赖中**包含 **spring-boot-starter-validation。
>
> 其他版本不一定噢 ，要自己检查一下哈。
>
> 

```xml
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

```



**user 实体类**

```java
import org.hibernate.validator.constraints.Length;
import javax.validation.constraints.NotBlank;


public class User implements Serializable {
    
    private Integer id;

    
    @NotBlank(message ="用户名不能为空哈")
    private String username;

   
    @Length(min =1 ,max = 10,message = "密码长度为1~10之间哈")
    @NotBlank(message = "用户密码不能为空!")
    private String password;
  
```

**controller**

```java
import org.springframework.validation.annotation.Validated;

public Object addUser02(@Validated User user){
    ResultInfo resultInfo = new ResultInfo();
    this.userService.addUser(user);
    
    return resultInfo;
}
```



<br><br>