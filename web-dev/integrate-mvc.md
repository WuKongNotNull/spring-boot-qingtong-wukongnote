# Spring Boot 整合 MVC 和拦截器
## Spring Boot 整合 Spring MVC

### **pom 依赖**

```xml
        <!-- thymeleaf -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

        <!-- web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        
```



<br>

## **自定义配置类**

> 编写自定义配置类并实现WebMvcConfigurer

````java
package com.wukongnotnull.config;

import com.wukongnotnull.interceptor.MyInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
                registry.addViewController("/login.html").setViewName("login");

    }

}

````



<br>



### **login 页面展示**

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>登录页面</title>
</head>
<body>
<div>
  <form action="">
    <p>用户名：<input type="text"></p>
    <p>密码：<input type="password"></p>
    <p><input type="submit" value="登录"></p>
  </form>
</div>
</body>
</html>
```

<br>

## Spring Boot 整合拦截器

## 自定义拦截器组件

> 准备主页（home.html）和 登录页（login.html）
>
> 拦截器实现未登录状态下，不能访问主页（home页面文件）

```java
package com.wukongnotnull.interceptor;
//author: 悟空非空也（B站/知乎/公众号）

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component
public class MyInterceptor  implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, 
                             HttpServletResponse response, 
                             Object handler) throws Exception {
        // 拦截下来的请求，什么请求被阻止，什么请求被放行 
        Object username = request.getSession().getAttribute("userSession");
        if (username == null) {
            response.sendRedirect(request.getContextPath()+"/login");
            // 拦截
            return false;
        }
        // 放行
        return  true;
    }


}

```

<br>

**在 MyMvcConfig 配置类中添加拦截器的相关配置**

````java
package com.wukongnotnull.config;
//author: 悟空非空也（B站/知乎/公众号）

import com.wukongnotnull.interceptor.MyInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyMvcConfig  implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
      registry.addViewController("/login").setViewName("login");
       registry.addViewController("/home").setViewName("home");
  
    }

    @Autowired
    private MyInterceptor myInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
      	// 指定拦截哪些请求
        // 拦截所有请求，除了 /login 和 /doLogin
       registry.addInterceptor(myInterceptor).addPathPatterns("/**")
         .excludePathPatterns("/login");
    }
}


````

<br>

**测试**

localhost:8080/home  

localhost:8080/login



<br><br><br>