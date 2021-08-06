<extoc></extoc>
# CORS 跨域处理
##  跨域

浏览器出于安全的考虑，使用 XMLHttpRequest对象发起 HTTP请求时必须遵守同源策略，否则就是跨域的HTTP请求，默认情况下是被禁止的。 同源策略要求源相同才能正常进行通信，即协议、域名、端口号都完全一致。 

<br>

## CORS 解决跨域

CORS是一个W3C标准，全称是”跨域资源共享” `Cross-origin resource sharing` ，允许浏览器向跨源服务器，发出 `XMLHttpRequest`请求，从而克服了AJAX只能同源使用的限制。



它通过服务器增加一个特殊的 `Header[Access-Control-Allow-Origin]` 来告诉客户端跨域的限制，如果浏览器支持 `CORS`  ,并且判断Origin通过的话，就会允许XMLHttpRequest发起跨域请求。

<br>

## SpringBoot 使用 CORS 解决跨域

### 方法一 @CrossOrigin

> 方法或者类上添加 `@CrossOrigin` 注解

~~~~java
@RestController
@RequestMapping("/user")
@CrossOrigin
public class UserController {

    @Autowired
    private UserServcie userServcie;

    @RequestMapping("/getUserList")
    public ResponseResult getUserList(){
      
        List<User> users = userServcie.getUserList();

        return new ResponseResult(200,users);
    }
}

~~~~

<br>

### 方法二  重写 addCorsMappings 方法

~~~~java
@Configuration
public class MyWebMvcConfigurer implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
      	// 设置允许跨域的路径
        registry.addMapping("/**")
                // 设置允许跨域请求的域名
                .allowedOriginPatterns("*")
                // 是否允许 cookie
                .allowCredentials(true)
                // 设置允许的请求方式
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                // 设置允许的 header 属性
                .allowedHeaders("*")
                // 允许跨域最大时长
                .maxAge(3600);
    }
}
~~~~





<br>
<br>
<br>