# 整合 Servlet Filter Listener

## 组件注册方式整合Servlet三大组件


> 3 大组件分别是 servlet  filter  和 listener 

<br>

### **整合 servlet 组件**

> servlet  负责和浏览器进行交互 ，接受请求，找到相对应的响应信息，返回给客户端（浏览器）

<br>



**myServlet**

```java
package com.wukongnotnull.servlet;
/* author: 悟空非空也（B站/知乎/公众号） */

import org.springframework.stereotype.Component;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component
public class MyServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        this.doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        resp.getWriter().write("hello MyServlet ,hello wukong");
    }

}

```

<br>



**servletConfig**

```java
package com.wukongnotnull.config;
/* author: 悟空非空也（B站/知乎/公众号） */

import com.wukongnotnull.servlet.MyServlet;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;


@Configuration
public class ServletConfig {
    
    // 注册Servlet组件
    @Bean
    public ServletRegistrationBean<MyServlet> getServlet(MyServlet myServlet){

        return new ServletRegistrationBean<>(myServlet,"/myServlet");
    }


}
```

<br>

**测试**  <br>

 通过路径找到对应的资源文件，返回给浏览器。

<br>

### **整合 filter**



**自定义 MyFilter** 

```java
package com.wukongnotnull.servlet;
/* author: 悟空非空也（B站/知乎/公众号） */


import org.springframework.stereotype.Component;
import javax.servlet.*;
import java.io.IOException;

@Component
public class MyFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("悟空非空也在执行过滤操作。。。。");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {

    }
}

```

<br>

**FilterConfig**

```java
package com.wukongnotnull.config;
/* author: 悟空非空也（B站/知乎/公众号） */

import com.wukongnotnull.servlet.MyFilter;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;


import java.util.Arrays;
import java.util.Collections;

@Configuration
public class FilterConfig {

    // 注册filter组件
    @Bean
    public FilterRegistrationBean<MyFilter> getFilter(MyFilter myFilter){
        FilterRegistrationBean<MyFilter> myFilterFilterRegistrationBean = new FilterRegistrationBean<>(myFilter);
        myFilterFilterRegistrationBean.setUrlPatterns(Arrays.asList("/myServlet"));
        return myFilterFilterRegistrationBean;

    }

}

```

<br>

### **整合 listener**



**MyListener**

```java
package com.wukongnotnull.servlet;
/* author: 悟空非空也（B站/知乎/公众号） */


import org.springframework.stereotype.Component;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;

@Component
public class MyListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ServletContextListener  开始初始化.....");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContextListener  销毁掉.....");
    }
}
```

<br>

**listenerConfig**

```java
package com.wukongnotnull.config;
/* author: 悟空非空也（B站/知乎/公众号） */

import com.wukongnotnull.servlet.MyListener;
import org.springframework.boot.web.servlet.ServletListenerRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class ListenerConfig {

    @Bean
    public ServletListenerRegistrationBean<MyListener> getListener(MyListener myListener){
        return new ServletListenerRegistrationBean<>(myListener);
    }

}
```

<br>



## 路径扫描方式整合Servlet三大组件

**启动类**

> 添加注解 @ServletComponentScan

```java
@SpringBootApplication
@ServletComponentScan
public class WebappThymeleafApplication {

    public static void main(String[] args) {
        SpringApplication.run(WebappThymeleafApplication.class, args);
    }

}
```

<br>

### **myServlet**

>  添加注解   @WebServlet(value = {"/myServlet"})

```java
package com.wukongnotnull.servlet;
/* author: 悟空非空也（B站/知乎/公众号） */

import org.springframework.stereotype.Component;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(value = {"/myServlet"})
public class MyServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        this.doPost(req, resp);
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp)
            throws ServletException, IOException {
        resp.getWriter().write("hello MyServlet ,hello wukong");
    }

}

```

<br>

### **myFilter**

> 添加注解 @WebFilter(value = {"/myServlet"})

```java
package com.wukongnotnull.servlet;
/* author: 悟空非空也（B站/知乎/公众号） */

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

@WebFilter(value = {"/myServlet"})
public class MyFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {

    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("悟空非空也在执行过滤操作。。。。");
        filterChain.doFilter(servletRequest,servletResponse);
    }

    @Override
    public void destroy() {

    }
}

```



### **myListener.java**

> 添加注解 @WebListener

```java
package com.wukongnotnull.servlet;
/* author: 悟空非空也（B站/知乎/公众号） */

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

@WebListener
public class MyListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ServletContextListener  开始初始化.....");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContextListener  销毁掉.....");
    }
}
```

<br><br><br>

