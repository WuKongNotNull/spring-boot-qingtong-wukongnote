## 悟空编号





<br>

## 学前素材准备

> 前端资源（html css js image 等等）
>
> 链接：https://share.weiyun.com/QD9u6jjX 密码：wukong



<br>



## 自定义用户访问控制

> - 访问首页授权  index.html
> - 访问静态资源（图片和样式）授权
>

<br>

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
        // 自定义用户授权管理
        http.authorizeRequests()
                 // 所有人都可以访问
                .antMatchers("/").permitAll()
                // 需要对static文件夹下静态资源（图片和样式）进行统一放行
                .antMatchers("/login/**").permitAll()
          			// 访问如下请求需要 角色（权限）是 common 或者 vip
                .antMatchers("/detail/common/**").hasAnyRole("common","vip")
                .antMatchers("/detail/vip/**").hasRole("vip")
                // 任何请求访问都需要先进行身份认证
                .anyRequest().authenticated();
}
```

<br>



> Spring Security 中的 hasRole 和 hasAuthority 有区别吗？
>
> https://cloud.tencent.com/developer/article/1703187



**hasRole()**

```java
public ExpressionInterceptUrlRegistry hasRole(String role) {
 return access(ExpressionUrlAuthorizationConfigurer.hasRole(role));
}
private static String hasRole(String role) {
 Assert.notNull(role, "role cannot be null");
 if (role.startsWith("ROLE_")) {
  throw new IllegalArgumentException(
    "role should not start with 'ROLE_' since it is automatically inserted. Got '"
      + role + "'");
 }
 return "hasRole('ROLE_" + role + "')";
}
```



<br>


## 自定义用户登录页面

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org" lang="en">
<head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>用户登录界面</title>
    <link th:href="@{/login/css/bootstrap.min.css}" rel="stylesheet">
    <link th:href="@{/login/css/signin.css}" rel="stylesheet">
    <link th:href="@{/login/css/mycss.css}" rel="stylesheet"/>
</head>
<body class="text-center">
<form class="form-signin" th:action="@{/userLogin}" th:method="post">
    <img class="mb-4" th:src="@{/login/img/login.jpg}" width="72px" height="72px" alt="">
    <h1 class="h3 mb-3 font-weight-normal">请登录</h1>
    <!-- 用户登录错误信息提示框 -->
    <div th:if="${param.error}" style="color: red;height: 40px;text-align: left;font-size: 1.1em">
        <img th:src="@{/login/img/loginError.jpg}" width="20px" alt="">用户名或密码错误，请重新登录！
    </div>
    <label>
        <input type="text" name="name" class="form-control" placeholder="用户名" required="" autofocus="">
    </label>
    <label>
        <input type="password" name="pwd" class="form-control" placeholder="密码" required="">
    </label>
    <div class="checkbox mb-3">
        <label>
            <input type="checkbox" name="rememberMe"> 记住我
        </label>
    </div>
    <button class="btn btn-lg btn-primary btn-block" type="submit">登录</button>
    <p class="mt-5 mb-3 text-muted">Copyright© 2050-3030</p>
</form>
</body>
</html>

```

<br>

```java
package com.wukongnotnull.controller;

import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContext;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;


//author: 悟空非空也（B站/知乎/公众号）
@Controller
public class LoginController {

    @GetMapping("/userLogin")  //路径要和 提交登录表单的url 一致
    public String index(){
        return "login/login";
    }

}
```



```java
package com.wukongnotnull.config;


import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import javax.sql.DataSource;


//author: 悟空非空也（B站/知乎/公众号）
@EnableWebSecurity
public class SecurityConfig  extends WebSecurityConfigurerAdapter {


    @Override
    protected void configure(HttpSecurity http) throws Exception {

        // 自定义用户登录控制
        // 技巧： 获得登录页和处理登录信息的 url 都是 /userLogin , post 请求进入如下方法
        http.authorizeRequests().and()
          			// 开启登录功能
          			.formLogin()
          			// 访问登录页面（自定义登录路径）
                .loginPage("/userLogin").permitAll()
          			//  指定登录处理的 url ，对应action的值
          			.loginProcessingUrl("/userLogin")
                .usernameParameter("name")
                .passwordParameter("pwd")
          			// 	登录成功后，访问首页 /
                .defaultSuccessUrl("/")
          			// 登录失败后，访问 /userLogin?error
                .failureUrl("/userLogin?error");

    }

}

```



<br>

## 自定义用户退出

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
 				// 退出
        http
          // 开启退出功能
          .logout()
          // 访问 /logout 执行退出功能
          .logoutUrl("/logout")
          // 退出成功，访问首页  / 
          .logoutSuccessUrl("/");

    }
```



<br>

## 登录用户信息获取

```
    /**
     *  通过 Security 提供的 SecurityContextHolder 获取登录用户信息
     * @return String
     */
    @GetMapping("/userInfo")
    @ResponseBody
    public String getUser(){
        // 获取应用上下文
        SecurityContext context = SecurityContextHolder.getContext();
        // 获取用户相关信息
        Authentication authentication = context.getAuthentication();
        UserDetails userDetails = (UserDetails)authentication.getPrincipal();
        System.out.println("username: "+userDetails.getUsername());
        return "登录的用户名为： " + userDetails.getUsername();
    }
```



<br>

## 记得我功能

```
        <div class="checkbox mb-3">
            <label>
                <input type="checkbox" name="rememberMe"> 记住我
            </label>
        </div>
```



```mysql
USE springbootdata;
# 来源于 JdbcTokenRepositoryImpl 
create table persistent_logins (username varchar(64) not null, series varchar(64) primary key,
			token varchar(64) not null, last_used timestamp not null);

```



```
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
```



```java
    // 使用jdbc 技术访问数据库 
    @Autowired
    private DataSource dataSource;
   
   
   /**
     * 持久化Token存储
     * @return 
     */
    @Bean
    public JdbcTokenRepositoryImpl tokenRepository(){
        JdbcTokenRepositoryImpl jr=new JdbcTokenRepositoryImpl();
        jr.setDataSource(dataSource);
        return jr;
    }

		@Override
    protected void configure(HttpSecurity http) throws Exception {
       // 记住我
        http.rememberMe()
                .rememberMeParameter("rememberMe")
                .tokenValiditySeconds(60*60*24*7)
                .tokenRepository(this.tokenRepository());
    }

```



<br>

## CSRF防护功能

> 跨站伪造用户，获得真实用户的 session 信息，伪造成真实用户，进行非法操作。

<br>

**form 表单使用 csrf 防护**

>  			 <!--csrf 防护： 添加如下代码-->
>      	   <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}"/>

```html
    <!DOCTYPE html>
    <html xmlns:th="http://www.thymeleaf.org" lang="en">
    <head>
        <meta charset="UTF-8">
        <title>修改用户</title>
        <link th:href="@{/login/css/mycss.css}" rel="stylesheet"/>
    </head>
    <body>
    <div align="center">
        <form method="post" th:action="@{/updateUser}">
            <!--csrf 防护： 添加如下代码-->
            <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}"/>
            <label>用户名:
                <input type="text" name="username"/>
            </label><br/>
            <button type="submit">修改用户名</button>
        </form>
    </div>
    </body>
</html>
```

<br>

```java
package com.wukongnotnull.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.http.HttpServletRequest;

//author: 悟空非空也（B站/知乎/公众号）
@Controller
public class CSRFController {
    @GetMapping("/updateUser")
    public String updateUser(){
        return "csrf/csrfTest";
    }


    @ResponseBody
    @PostMapping("/updateUser")
    public String updateUser(@RequestParam("username") String username,
                             HttpServletRequest request
    ){
        System.out.println("request = " + request);

        String csrf_token=request.getParameter("_csrf");
        System.out.println("csrf_token = " + csrf_token);

        System.out.println("username = " + username);
        return "success";
    }
}

```

<br>



**ajax 使用 csrf 防护**

```html
<head> //针对ajax数据修改的CSRF Token配置
    <meta name="_csrf" th:content="${_csrf.token}"/>
    <meta name="_csrf_header" th:content="${_csrf.headerName}"/>
</head>

$(function () {
    var token = $("meta[name='_csrf']").attr("content");
    var headerName = $("meta[name='_csrf_header']").attr("content");
    $(document).ajaxSend(function(e, xhr, options) {
        xhr.setRequestHeader(headerName, token);
    });
});
```





## Security 管理前端页面内容呈现

> 不同权限，页面内容针对性呈现不同的内容
>
> 应用场景： 动态菜单的实现



```
       <!--使 thymeLeaf 上的权限属性生效-->
        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-springsecurity5</artifactId>
        </dependency>
```



> **(1)sec:authorize="isAuthenticated()"**
>
> **判断用户是否已经登陆认证，引号内的参数必须是isAuthenticated()。**
>
> **(2)sec:authentication=“name”**
>
> **获得当前用户的用户名，引号内的参数必须是name。**
>
> **(3)sec:authorize=“hasRole(‘role’)”**
>
> **判断当前用户是否拥有指定的权限。引号内的参数为权限的名称。**
>
> **(4)sec:authentication="principal.authorities"**
>
> **获得当前用户的全部角色，引号内的参数必须是principal.authorities。**



<br>

**index.html**

```html
<!DOCTYPE html>
<html
		xmlns:th="http://www.thymeleaf.org"
		xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity5" lang="en">
<head>
	<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
	<title>新浪新闻平台</title>
	<link th:href="@{/login/css/mycss.css}" rel="stylesheet"/>
</head>

<body>
<h1>欢迎新浪新闻首页</h1>

<div sec:authorize="isAnonymous()">
	<h2>游客您好，如果想查看<b>会员新闻</b><a th:href="@{/userLogin}">请登录</a></h2>
</div>


<div sec:authorize="isAuthenticated()">
	<h2>
		用户: <span sec:authentication="name" style="color: #0e69cb"></span> <br>
		您的权限：<span sec:authentication="principal.authorities" style="color:#66c256"></span>，您有权观看以下内容
	</h2>
	<form th:action="@{/logout}" method="post">
		<label>
			<input th:type="submit" th:value="注销"/>
		</label>
	</form>
</div>

<hr>

<div>
	<h3>游客 - 新闻专栏</h3>
	<ul>
		<li><a th:href="@{/detail/visitor/1}">新闻1：悟空非空也带你学习 Java </a></li>
		<li><a th:href="@{/detail/visitor/2}">新闻2：悟空非空也带你学习 Python </a></li>
		<li><a th:href="@{/detail/visitor/3}">新闻3：悟空非空也带你学习 C++ </a></li>
	</ul>
</div>

<!--<div sec:authorize="hasRole('common')">-->
<div sec:authorize="hasRole('common') || hasRole('vip')">
	<h3>普通会员 - 新闻专栏</h3>
	<ul>
		<li><a th:href="@{/detail/common/1}">普通会员新闻：1 </a></li>
		<li><a th:href="@{/detail/common/2}">普通会员新闻：2 </a></li>
	</ul>
</div>

<!--<div sec:authorize="hasAuthority('ROLE_vip')">-->
<div sec:authorize="hasRole('vip')">
	<h3>VIP会员 - 专享专栏</h3>
	<ul>
		<li><a th:href="@{/detail/vip/1}">vip会员新闻：1</a></li>
		<li><a th:href="@{/detail/vip/2}">vip会员新闻：2</a></li>
	</ul>
</div>

</body>
</html>


```



<br>



## 源码归档

> 归档链接



<br>

<br>

<br>

