<extoc></extoc>

# Api文档框架 Swagger

## Swagger2 文档构建工具

由于Spring Boot能够快速开发、便捷部署等特性，通常在使用Spring Boot构建Restful 接口应用时考虑到多终端的 原因，这些终端会共用很多底层业务逻辑，因此我们会抽象出这样一层来同时服务于多个移动端或者Web前端。对于 不同的终端公用一套接口API时对于联调测试时就需要知道后端提供的接口Api 列表文档，对于服务端开发人员来说 就需要编写接口文档，描述接口调用地址参数结果等，这里借助第三方构建工具Swagger2来实现Api文档生成功能 。



**引入依赖**

```xml
    <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger2</artifactId>
      <version>2.9.2</version>
    </dependency>

    <dependency>
      <groupId>io.springfox</groupId>
      <artifactId>springfox-swagger-ui</artifactId>
      <version>2.9.2</version>
    </dependency>
```



**配置类**

```java
package com.wukongnotnull.config;
/* author: 悟空非空也（B站/知乎/公众号） */

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

import java.util.ArrayList;

@Configuration
@EnableSwagger2
public class Swagger2Config {

    @Bean
    public Docket createDocket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(this.apiInfo())
                .select()
          				//根据基包路径选择接口
                .apis(RequestHandlerSelectors.basePackage("com.wukongnotnull.controller"))
                .build();
    }

    //配置文档信息
    private ApiInfo apiInfo() {
        Contact contact = new Contact("悟空非空也", "http://www.huaguoshan.tech", "1390128154@qq.com");
        return new ApiInfo(
                "悟空的项目api文档", // 标题
                "实现对用户模块的增删改查", // 描述
                "v1.0", // 版本
                "http://terms.service.url/组织链接", // 组织链接
                contact, // 联系人信息
                "Apach 2.0 许可", // 许可
                "许可链接", // 许可连接
                new ArrayList<>()// 扩展
        );
    }

}

```

> // 根据包路径选择接口
>
>basePackage(final String basePackage) 
>
>// 选择所有接口
>
>any()
>
>//  不选择接口
>
>none() 
>
>// 根据 method 的注解选择，如 withMethodAnnotation(GetMapping.class) 只扫描 GET 请求
>withMethodAnnotation(final Class<? extends Annotation> annotation)
>
>// 根据类上的注解选择，如.withClassAnnotation(Controller.class)只选择 controller注解类中的接口
>withClassAnnotation(final Class<? extends Annotation> annotation)
>
>



**访问路径**

>  wukong 为项目名

http://localhost:8081/wukong/swagger-ui.html





**controller**

```java
package com.wukongnotnull.controller;

import com.github.pagehelper.PageInfo;
import com.wukong.bo.UserParams;
import com.wukong.pojo.User;
import com.wukong.service.UserService;
import com.wukong.utils.ParamsException;
import com.wukong.vo.ResultInfo;
import io.swagger.annotations.*;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;



@RestController
@Api(tags = "我是用户控制器")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/user/{username}")
    @ApiOperation(value ="getUserByUsername接口",notes = "根据用户名查询用户")
    @ApiImplicitParam(
            name = "username", value = "用户名",
            required = true, dataType = "String",
            defaultValue = "默认值是。。。",
            paramType = "path"
    )
    public User getUserByUsername(@PathVariable  String username){
        return this.userService.getUserByUsername(username);
    }

    @GetMapping("/user/id/{id}")
    @ApiOperation(value = "getUserById接口", notes = "根据id查询用户")
    @ApiImplicitParam(name = "id", value = "用户id字段",
            required = true,dataType = "Integer",
            paramType = "path",defaultValue = "默认值为..."
    )
    public Object getUserById(@PathVariable Integer id){
        ResultInfo resultInfo = new ResultInfo();
        User userById =null;
      		。。。。
            。。。。
        return resultInfo;
    }

   @PostMapping("/user")
   @ApiOperation(value = "addUser接口", notes = "实现添加用户")
   @ApiImplicitParam(
           name = "user", value = "User对象",
           dataType = "User", paramType = "body",
           defaultValue = "默认值..."
   )
    public Object addUser(@RequestBody User user){
       ResultInfo resultInfo = new ResultInfo();
       try{
            this.userService.addUser(user);

        }catch (ParamsException paramsException){

           resultInfo.setCode(paramsException.getCode());
           resultInfo.setMsg(paramsException.getMsg());

        }catch (Exception e){
            e.printStackTrace();
        }

      return resultInfo;
   }


   @PutMapping("/user")
   @ApiOperation(value = "modifyUser接口", notes = "实现用户信息的修改")
   @ApiImplicitParam(
          name = "user",value = "User对象",required = true,
           dataType = "User",paramType = "body",
           defaultValue = "默认值为..."
   )
    public Object modifyUser(@RequestBody User user){
       ResultInfo resultInfo = new ResultInfo();
       try {
           this.userService.modifyUser(user);
       } catch (ParamsException e){
          resultInfo.setCode(e.getCode());
          resultInfo.setMsg(e.getMsg());
       }
       catch (Exception e) {
           e.printStackTrace();
       }
       return  resultInfo;
   }


   @DeleteMapping("/user/{id}")
   @ApiOperation(value = "deleteUser接口", notes = "删除用户的功能")
   @ApiImplicitParam(
           name = "id",value = "用户id",
           defaultValue = "默认值...", required = true,
           dataType = "Integer",paramType = "path"
   )
    public ResultInfo deleteUser(@PathVariable Integer id) {
       ResultInfo resultInfo = new ResultInfo();
       try {
           this.userService.deleteUser(id);
       } catch (ParamsException e){
           resultInfo.setCode(e.getCode());
           resultInfo.setMsg(e.getMsg());
       }
       catch (Exception e) {
           e.printStackTrace();
       }
       return resultInfo;
   }

   @GetMapping("/user/list")
   @ApiOperation(value = "getUserPageList接口", notes = "分页查询用户列表")
   /*@ApiImplicitParam(name = "userParams", value = "用户查询条件") 不要写 */
   public PageInfo<User> getUserPageList( UserParams userParams){
       return this.userService.getUserPageList(userParams);
   }



}
```



**model**

```java
package com.wukongnotnull.pojo;

import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;

@ApiModel(description = "User实体类")
public class User {
    // 当类型为Integer类型时候, example 必须要写
    @ApiModelProperty(name = "id",value = "用户id",example = "1")
    private Integer id;
    @ApiModelProperty(name = "username",value = "用户名")
    private String username;
    @ApiModelProperty(name = "password",value = "用户密码")
    private String password;

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



```java
package com.wukongnotnull.vo;
/* author: 悟空非空也（B站/知乎/公众号） */

import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;

@ApiModel(description = "响应对象")
public class ResultInfo {
    @ApiModelProperty(name = "code",value = "状态码",example = "200")
    private Integer code = 200;
    @ApiModelProperty(name = "msg",value = "响应信息")
    private  String msg = "操作成功";
    @ApiModelProperty(name = "data", value = "响应数据")
    private  Object data;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMsg() {
        return msg;
    }

    public void setMsg(String msg) {
        this.msg = msg;
    }

    public Object getData() {
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }
}
```





## 分环境显示文档

> 动态配置当项目处于test、dev环境时显示swagger，处于prod时不显示

```java
@Bean
public Docket docket(Environment environment) {
   // 设置要显示swagger的环境
   Profiles of = Profiles.of("dev", "test");

   // 通过 enable() 接收此参数判断是否要显示
   boolean flag = environment.acceptsProfiles(of);

   return new Docket(DocumentationType.SWAGGER_2)
      .apiInfo(apiInfo())
      .enable(flag) //是否启用 Swagger ，false 为浏览器无法访问
      .select()
      .apis(RequestHandlerSelectors.basePackage("com.wukongnotnull.controller"))
       // 通过请求路径再过滤
      .paths(PathSelectors.ant("/wukong/**"))
      .build();
}
```





## 文档分组展示

> 接口文档根据不同开发者进行分组展示，开发者分别是 wukong , zhubajie , shaseng 



默认组名为 default  ,通过 groupName() 可配置分组

```
@Bean
public Docket docket() {
   return new Docket(DocumentationType.SWAGGER_2).apiInfo(apiInfo())
      .groupName("wukong") // 配置分组
       // 省略配置....
}
```



配置多个分组

```
@Bean
public Docket docket1(){
   return new Docket(DocumentationType.SWAGGER_2).groupName("zhubajie");
}
@Bean
public Docket docket2(){
   return new Docket(DocumentationType.SWAGGER_2).groupName("shaseng");
}
```



## UI 界面拓展

 **默认的UI**

>  **访问 http://localhost:8080/swagger-ui.html**

```xml
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger-ui</artifactId>
   <version>2.9.2</version>
</dependency>
```



**bootstrap-ui** 

> 访问 http://localhost:8080/doc.html

```xml
<dependency>
   <groupId>com.github.xiaoymin</groupId>
   <artifactId>swagger-bootstrap-ui</artifactId>
   <version>1.9.1</version>
</dependency>
```



**Layui-ui**  

> 访问 http://localhost:8080/docs.html

```
<dependency>
   <groupId>com.github.caspar-chen</groupId>
   <artifactId>swagger-ui-layer</artifactId>
   <version>1.1.3</version>
</dependency>
```



**mg-ui**

>  **访问 http://localhost:8080/document.html**

```
<dependency>
   <groupId>com.zyplayer</groupId>
   <artifactId>swagger-mg-ui</artifactId>
   <version>1.0.6</version>
</dependency>
```

