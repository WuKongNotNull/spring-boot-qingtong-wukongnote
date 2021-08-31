<extoc></extoc>

# 响应结果统一设计

## 背景

在分布式、微服务盛行的时代，项目都采用的微服务框架和前后端分离方式。

前端和后端进行交互，前端按照约定请求 `URL` 路径，并携带相关请求参数；后端服务器接收请求，进行业务处理，返回数据给前端。

统一接口的返回数据，变得非常重要。

<br>

## 统一格式设计

### 一般形式

```xml
{
	# 是否响应成功
	success: true,
	# 响应状态码
	code: 200,		
	# 响应数据
	data: Object
	# 返回错误信息
	message: "",
}
```

<br>

###  结果类枚举

```java
public enum ResultCodeEnum {
    /*** 通用部分 100 - 599***/
    // 成功请求
    SUCCESS(200, "successful"),
    // 重定向
    REDIRECT(301, "redirect"),
    // 资源未找到
    NOT_FOUND(404, "not found"),
    // 服务器错误
    SERVER_ERROR(500,"server error");

		//可以根据不同模块用不同的区级分开错误码
    // 1000～1999 区间表示用户模块错误
    // 2000～2999 区间表示订单模块错误
    // 3000～3999 区间表示商品模块错误

    /**
     * 响应状态码
     */
    private Integer code;
    /**
     * 响应信息
     */
    private String message;

    ResultCodeEnum(Integer code, String msg) {
        this.code = code;
        this.message = msg;
    }

    public Integer getCode() {
        return code;
    }

    public String getMessage() {
        return message;
    }
}
```

<br>



### 统一结果类

```java
public class HttpResult <T> implements Serializable {

    /**
     * 是否响应成功
     */
    private Boolean success;
    /**
     * 响应状态码
     */
    private Integer code;
    /**
     * 响应数据
     */
    private T data;
    /**
     * 错误信息
     */
    private String message;

    // 构造器开始
    /**
     * 无参构造器(构造器私有，外部不可以直接创建)
     */
    private HttpResult() {
        this.code = 200;
        this.success = true;
    }
    /**
     * 有参构造器
     * @param obj
     */
    private HttpResult(T obj) {
        this.code = 200;
        this.data = obj;
        this.success = true;
    }

    /**
     * 有参构造器
     * @param resultCode
     */
    private HttpResult(ResultCodeEnum resultCode) {
        this.success = false;
        this.code = resultCode.getCode();
        this.message = resultCode.getMessage();
    }
    // 构造器结束

    /**
     * 通用返回成功（没有返回结果）
     * @param <T>
     * @return
     */
    public static<T> HttpResult<T> success(){
        return new HttpResult();
    }

    /**
     * 返回成功（有返回结果）
     * @param data
     * @param <T>
     * @return
     */
    public static<T> HttpResult<T> success(T data){
        return new HttpResult<T>(data);
    }

    /**
     * 通用返回失败
     * @param resultCode
     * @param <T>
     * @return
     */
    public static<T> HttpResult<T> failure(ResultCodeEnum resultCode){
        return  new HttpResult<T>(resultCode);
    }

    public Boolean getSuccess() {
        return success;
    }

    public void setSuccess(Boolean success) {
        this.success = success;
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public T getData() {
        return data;
    }

    public void setData(T data) {
        this.data = data;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

    @Override
    public String toString() {
        return "HttpResult{" +
                "success=" + success +
                ", code=" + code +
                ", data=" + data +
                ", message='" + message + '\'' +
                '}';
    }
}
```

**说明：**

1. 构造器私有，外部不可以直接创建；
2. 只可以调用统一返回类的静态方法返回对象；
3. `success` 是一个`Boolean` 值，通过这个值，可以直接观察到该次请求是否成功；
4. `data` 表示响应数据，用于请求成功后，返回客户端需要的数据。

<br>

### 测试

```java
@RestController
public class HttpRestController {

    @GetMapping("/success")
    public HttpResult success(){
        return HttpResult.success();
    }


    @GetMapping("/successWithData")
    public HttpResult successWithData(){
        return HttpResult.success("我是悟空非空也");
    }

 
    @GetMapping("/failure")
    public HttpResult failure(){
        return HttpResult.failure(ResultCodeEnum.NOT_FOUND);
    }

}
```







<br><br>

<br>