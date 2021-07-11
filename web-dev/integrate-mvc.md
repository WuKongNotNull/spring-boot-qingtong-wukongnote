## Spring MVC的整合支持



自定义拦截器组件

```java
package com.wukongnotnull.interceptor;

import com.wukongnotnull.tools.Constants;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Calendar;

@Component
public class MyInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        String requestURI = request.getRequestURI();
        Object loginUser = request.getSession().getAttribute(Constants.USER_SESSION);
        //请求路径是/admin的时候（同时session为null），拦截器放行通过
        if(requestURI.startsWith("/admin") && loginUser==null){
            response.sendRedirect(request.getContextPath()+"/toLoginPage");
            return false;
        }
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        int currentYear = Calendar.getInstance().get(Calendar.YEAR);
        request.setAttribute("currentYear",currentYear);

    }


}

```



编写自定义配置类，实现WebMvcConfigurer

````java
package com.siliang.config;

import com.siliang.interceptor.MyInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class MyMvcConfig implements WebMvcConfigurer {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
                registry.addViewController("/toLoginPage").setViewName("login");
                registry.addViewController("/login.html").setViewName("login");

    }

    @Autowired
    private MyInterceptor myInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        //对/** 格式的请求路径进行拦截，排除/login.html
      registry.addInterceptor(myInterceptor).addPathPatterns("/**").excludePathPatterns("/login.html");
    }
}

````



测试

localhost:8080/admin  可以正常显示 年份（经过拦截器并放行）

localhost:8080/login.html  不能显示年份（因没有经过拦截器）