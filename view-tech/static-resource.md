# Spring Boot 访问静态资源
## 访问静态资源

由于SpringBoot的项目一般打成jar包，项目结构中没有 webapps 目录。   

那么，静态资源要放到哪里呢？

> Spring Boot 源码告诉我们，将静态资源放到 `resources/static`   (或者 `resources/public` 或者`resources/resources` 或者 `resources/META-INF/resources`) 中即可。



<br>



## 修改存放目录

> 根据 `spring.web.resources.static-locations`  自定义静态资源存放位置。   
>
> 可以存放在 `src/main/resources/static/`	     
>
> 或者  `src/main/resources/public/ `    
>
> 或者  `src/main/resources/wukong/`    

 

```yaml
spring:
  web:
    resources:
      static-locations: classpath:/static/,classpath:/public/,classpath:/wukong/
```



`css`  和  `js` , 同理    

http://localhost:8080/wukong/mycss.css  

http://localhost:8080/wukong/myjs.js  

也可以分别被引入到html页面中  



![image-20210212221525314](../img/image-20210212221525314.png)





<br>



## 修改访问路径

> `Spring Boot` 默认静态资源的路径匹配为/** 。
>
> 想要修改请设置  `spring.mvc.static-path-pattern`  。

在application.yml中

~~~~yml
spring:
  mvc:
    static-path-pattern: /wk/**  #修改静态资源访问路径
~~~~







<br><br><br>

