<extoc></extoc>

# Token 登录
## 拦截器

​		如果我们想在多个Handler方法执行之前或者之后都进行一些处理，甚至某些情况下需要拦截掉，不让Handler方法执行。那么可以使用SpringMVC为我们提供的拦截器。



##  使用步骤

### 创建类实现HandlerInterceptor接口

~~~~java
public class LoginInterceptor implements HandlerInterceptor {
}
~~~~

### 实现方法

~~~~java
@Component
public class LoginInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //获取请求头中的token
        String token = request.getHeader("token");
        //判断token是否为空，如果为空也代表未登录 提醒重新登录（401）
        if(!StringUtils.hasText(token)){
            response.sendError(HttpServletResponse.SC_UNAUTHORIZED);
            return false;
        }
        //解析token看看是否成功
        try {
            Claims claims = JwtUtil.parseJWT(token);
            String subject = claims.getSubject();
            System.out.println(subject);
        } catch (Exception e) {
            e.printStackTrace();
            //如果解析过程中没有出现异常说明是登录状态
            //如果出现了异常，说明未登录，提醒重新登录（401）
            response.sendError(HttpServletResponse.SC_UNAUTHORIZED);
            return false;
        }
        return true;
    }
}
~~~~

### 配置拦截器


~~~~java
@Configuration
public class LoginConfig implements WebMvcConfigurer {

    @Autowired
    private LoginInterceptor loginInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loginInterceptor)//添加拦截器
            .addPathPatterns("/**")  //配置拦截路径
            .excludePathPatterns("/sys_user/login");//配置排除路径
    }
}
~~~~





