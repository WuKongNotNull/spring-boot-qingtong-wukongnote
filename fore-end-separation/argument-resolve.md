<extoc></extoc>

# 自定义参数解析

如果我们想实现像获取请求体中的数据那样，在Handler方法的参数上增加一个@RepuestBody注解就可以获取到对应的数据的话。
可以使用HandlerMethodArgumentResolver来实现自定义的参数解析。
## 案例 
> 实现当前用户token 值和形参的绑定
### 定义用来标识的注解

~~~~java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
public @interface CurrentUserId {

}
~~~~

### 创建类实现HandlerMethodArgumentResolver接口并重写其中的方法

**注意加上@Component注解注入Spring容器**

~~~~java
public class UserIdArgumentResolver implements HandlerMethodArgumentResolver {

    //判断方法参数使用能使用当前的参数解析器进行解析
    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        //如果方法参数有加上CurrentUserId注解，就能把被我们的解析器解析
        return parameter.hasParameterAnnotation(CurrentUserId.class);
    }
    //进行参数解析的方法，可以在方法中获取对应的数据，然后把数据作为返回值返回。方法的返回值就会赋值给对应的方法参数
    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        //获取请求头中的token
        String token = webRequest.getHeader("token");
        if(StringUtils.hasText(token)){
            //解析token，获取userId
            Claims claims = JwtUtil.parseJWT(token);
            String userId = claims.getSubject();
            //返回结果
            return userId;
        }
        return null;
    }
}
~~~~

### 配置参数解析器

~~~~java
@Configuration
public class ArgumentResolverConfig implements WebMvcConfigurer {

    @Autowired
    private UserIdArgumentResolver userIdArgumentResolver;

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(userIdArgumentResolver);
    }
}
~~~~



### 测试

在需要获取UserId的方法中增加对应的方法参数然后使用@CurrentUserId进行标识即可获取到数据

~~~~java
@RestController
@RequestMapping("/user")
//@CrossOrigin
public class UserController {

    @Autowired
    private UserServcie userServcie;

    @RequestMapping("/findAll")
    public ResponseResult findAll(@CurrentUserId String userId) throws Exception {
        System.out.println(userId);
        //调用service查询数据 ，进行返回s
        List<User> users = userServcie.findAll();

        return new ResponseResult(200,users);
    }
}
~~~~

