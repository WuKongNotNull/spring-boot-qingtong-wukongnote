# Spring Boot 整合数据校验
## Spring MVC 数据校验 

前端提交表单数据，被后端接口接收到，为了程序的严谨性，后端会进行数据校验。

`Spring Boot`  通过  `spring-boot-starter-validation `  模块进行数据校验的工作。 

**JSR303/JSR-349**

` JSR303` 是一项标准,只提供规范不提供实现，规定一些校验规范即校验注解，如 `@Null` ， ` @NotNull` ， `@Pattern` ，位于`javax.validation.constraints` 包下。

JSR-349是其升级版本，添加了一些新特性。

**Hibernate Validation：**

`Hibernate Validation`  是对这个规范的实现，并增加了一些其他校验注解，如`@Email`， `@Length`，`@Range` 等等 。

`Spring Validation` 对 `Hibernate Validation` 进行了二次封装，在 `Spring MVC` 模块中添加自动校验，并将校验信息封装进特定的类中。



## 使用案例

### **pom 依赖**

```xml
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

```

<br>



### 前端页面

> 创建 `registerUser.html` 
>
> > 记得引入 `spring-boot-starter-thymeleaf` 依赖  

```html
  <form action="/addUser" method="post">
    <p>用户名：<input type="text" name="username"></p>
    <p>密码：<input type="text" name="password"></p>
    <p><input type="submit" value="注册"></p>
  </form>
```



<br>

### **User 实体类**

> 属性上添加对应校验注解，比如 `@NotBlank` ，`@Length` ，`@NotBlank`  。
>
> 补充：
>
> > @Data  //记得引入 lombok 依赖

```java
package com.wukongnotnull;
//author: 悟空非空也（B站/知乎/公众号）

import org.hibernate.validator.constraints.Length;


import javax.validation.constraints.NotBlank;
import java.io.Serializable;
@Data  //记得引入 lombok 依赖
public class User implements Serializable {

    private  Integer id;
    @NotBlank(message = "用户名不能为空")
    private  String username;

    @Length(min=3,max=10,message = "密码长度为3-10之间")
    @NotBlank(message = "密码不能为空")
    private  String password;

   
}

```

<br>

### **controller**

> 形参前添加 `@Validated` 或者 `@Valid`

```java
package com.wukongnotnull.controller;

import com.wukongnotnull.User;
import org.springframework.stereotype.Controller;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

import java.util.ArrayList;
import java.util.List;

//author: 悟空非空也（B站/知乎/公众号）
@Controller
public class UserController {

    @GetMapping("/register")
    public String showRegisterPage(){
        return "registerUser";
    }


    // 添加注解 @Valid 或者 @Validated 均可以
    // BindingResult 用于接收数据校验失败的提示信息
    @PostMapping(value = "/addUser")
    @ResponseBody
    public Object addUser(@Validated User user , BindingResult bindingResult){
        List<Object> list = new ArrayList<>();
        if (bindingResult.hasErrors()) {
            bindingResult.getAllErrors().forEach(e ->{
                list.add(e.getDefaultMessage());
            });
        }else {
            list.add("后端数据校验成功");
        }
        return list;
    }


}
```





<br><br><br>