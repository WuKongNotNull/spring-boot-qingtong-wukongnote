## 全局异常处理

在Spring Boot 提供了对异常的全局性处理，相关注解如下: 

**@ControllerAdvice** 
该注解组合了@Component注解功能,最常用的就是作为全局异常处理的切面类,同时通过该注解可以指定包扫描的范 围。@ControllerAdvice约定了几种可行的返回值，如果是直接返回model类的话，需要使用@ResponseBody进行 json转换



 **@ExceptionHandler** 
  该注解在Spring 3.X 版本引入，在处理异常时标注在方法级别，代表当前方法处理的异常类型有哪些



 以 Restful 接口为例,测试保存用户接口

```java
package com.wukong.exception;
/* author: 悟空非空也（B站/知乎/公众号） */

import com.wukong.utils.ParamsException;
import com.wukong.vo.ResultInfo;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;

@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(value = {Exception.class})
    @ResponseBody
    public ResultInfo handleException(Exception e){

        ResultInfo resultInfo = new ResultInfo();
        resultInfo.setCode(300);
        resultInfo.setMsg("系统异常");

        if(e instanceof ParamsException){
            ParamsException paramsException = (ParamsException) e;
            resultInfo.setCode(paramsException.getCode());
            resultInfo.setMsg(paramsException.getMsg());
        }
        return  resultInfo;
    }

}
```

**或者**

```java
@ExceptionHandler(value = {ParamsException.class})
@ResponseBody
public ResultInfo handleParamsException(ParamsException paramsException){
    ResultInfo resultInfo = new ResultInfo();
    resultInfo.setCode(paramsException.getCode());
    resultInfo.setMsg(paramsException.getMsg());
    return  resultInfo;
}
```

