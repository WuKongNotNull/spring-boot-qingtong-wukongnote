# Spring Boot  全局异常处理

 `Spring Boot` 提供了对异常的全局性处理，相关注解如下: 

**@ControllerAdvice** 
最常用的就是作为全局异常处理的切面类,同时通过该注解可以指定包扫描围。`@ControllerAdvice` 约定了几种可行的返回值，如果是直接返回 `model` 类的话，需要使用 `@ResponseBody` 进行 ` json` 转换。



 **@ExceptionHandler** 
 该注解在 `Spring 3.X`  版本引入，在处理异常时标注在方法级别，代表当前方法处理的异常类型有哪些。



 以 Restful 接口为例,测试保存用户接口

```java
package com.wukongnotnull.exception;
/* author: 悟空非空也（B站/知乎/公众号） */

import com.wukongnotnull.utils.ParamsException;
import com.wukongnotnull.vo.ResultInfo;
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

<br><br><br>