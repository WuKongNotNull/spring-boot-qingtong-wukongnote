<extoc></extoc>
# RestFull 接口设计

## RESTfull  API 设计思想

最近，流行的Web都分为前端和后端两个部分。前端设备各种各样（手机、平板、桌面电脑等等.）。因此，需要一种统一的机制，方便不同的前端设备与后端进行通信。

需要一个良好的API构架来解决这样问题。`RESTful API` 是目前比较成熟的一套API设计理论。

<br>

### 协议

API与前端设备的通信协议，使用 `HTTPs` 协议。

<br>

### 域名

应该尽量将`API`部署在专用域名之下。

> ```javascript
> https://api.example.com
> ```

如果确定API很简单，不会有进一步扩展，可以考虑放在主域名下。

> ```javascript
> https://example.org/api/
> ```

<br>

### 版本

可以将 API 的版本号放入 URL 中，如 Github 。

> ```javascript
> https://api.example.com/v1/
> ```

<br>



### 路径

路径表示 `API` 的具体地址。

在 RESTful 架构中，每个网址代表一种资源（resource），规定网址中不能有动词，只能有名词。

名词一般与数据库的表格名对应。

名词需要使用复数。

比如，有一个电商系统，它的路径应该如下设计。

> - https://api.example.com/v1/users
> - https://api.example.com/v1/bills
> - https://api.example.com/v1/products

<br>

### HTTP动词

对于资源的具体操作类型，由HTTP动词表示。

常用的 HTTP 动词有如下五个种（括号里是对应的SQL命令）。

> - GET（SELECT）：       从服务器取出资源（一项或多项）。
> - POST（CREATE）：    在服务器新建一个资源。
> - PUT（UPDATE）：     在服务器更新资源（客户端提供改变后的完整资源）。
> - PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）。
> - DELETE（DELETE）：从服务器删除资源。



还有两个不常用的HTTP动词。

> - HEAD：获取资源的元数据。
> - OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。



举例：

> - GET    	   /users：查询用户列表
> - POST        /users： 新建一个用户信息
> - GET           /users/ID：获取某个指定id 的用户信息
> - PUT           /users/ID：更新某个指定id 的用户的信息（提供该用户的全部信息）
> - PATCH      /users/ID：更新某个指定id 的用户的信息（提供该用户的部分信息）
> - DELETE     /users/ID：删除指定用户
> - GET             /users/ID/bills：列出某个用户的所有订单
> - DELETE      /users/ID/bills/ID：删除某个指定用户的指定订单

<br>

### 过滤信息

若查询记录数量很多，服务器不可能都将它们返回给客户端。API应该提供参数，过滤返回结果。

下面是一些常见的参数。

> - ?limit=10：指定返回记录的数量
> - ?offset=10：指定返回记录的开始位置。
> - ?page=2&per_page=100：指定第几页，以及每页的记录数。
> - ?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
> - ?users_type_id=1：指定筛选条件

<br>



### 状态码

服务器向浏览器返回的状态码和提示信息，常见如下

> - 200 OK - [GET]：                                      服务器成功返回用户请求的数据，该操作是每次都成功。
> - 201 CREATED - [POST/PUT/PATCH]：  用户新建或修改数据成功。
> - 202 Accepted - [*]：                                表示一个请求已经进入后台排队（异步任务）
> - 204 NO CONTENT - [DELETE]：             用户删除数据成功。
> - 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，每次都是一样
> - 401 Unauthorized - [*]：                     表示用户没有权限（令牌、用户名、密码错误）。
> - 403 Forbidden - [*]                              表示用户得到授权（与401错误相对），但是访问是被禁止的。
> - 404 NOT FOUND - [*]：                     用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
> - 406 Not Acceptable - [GET]：           用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
> - 410 Gone -[GET]：                            用户请求的资源被永久删除，且不会再得到的。
> - 422 Unprocesable entity - [POST/PUT/PATCH]       当创建一个对象时，发生一个验证错误。
> - 500 INTERNAL SERVER ERROR - [*]：                      服务器发生错误，用户将无法判断发出的请求是否成功。

<br>



### 返回结果

针对不同操作，服务器向用户返回的结果应该符合以下规范

> - GET /collection：                        返回资源对象的列表（数组）
> - GET /collection/resource：       返回单个资源对象
> - POST /collection：                       返回新生成的资源对象
> - PUT /collection/resource：        返回完整的资源对象
> - PATCH /collection/resource：   返回完整的资源对象
> - DELETE /collection/resource：  返回一个空文档

<br>

## RESTfull  API 设计实操

>  测试前后端分离的项目，浏览器是不能胜任的，这里选择常用的测试工具，PostMan 完成接口测试。

### @RequestMapping

> 该注解可以加到方法上或者是类上，设置请求映射规则。



#### 指定请求路径

>  path或者value属性都可以用来指定请求路径。
>
> 

~~~~java
@RestController
@RequestMapping("/test")
public class HelloController {
    @RequestMapping("/testPath")
    public String testPath(){
        return "testPath";
    }
}
~~~~

~~~~java
@RestController
public class HelloController {

    @RequestMapping("/test/testPath")
    public String testPath(){
        return "testPath";
    }
}
~~~~

<br>

#### 指定请求方式

>  method属性可以用来指定可处理的请求方式。
>
> 注意：可以运用如下注解来进行替换
>
> - ​    @PostMapping       等价于   @RequestMapping(method = RequestMethod.POST) 
>
> - ​	@GetMapping         等价于   @RequestMapping(method = RequestMethod.GET) 
> - ​	@PutMapping         等价于   @RequestMapping(method = RequestMethod.PUT) 
> - ​	@DeleteMapping    等价于   @RequestMapping(method = RequestMethod.DELETE) 
>
> 

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {

    @RequestMapping(value = "/testMethod",method = RequestMethod.POST)
    public String testMethod(){
        System.out.println("testMethod处理请求");
        return "testMethod";
    }
}

~~~~

<br>



####  指定请求参数

> 我们可以使用**params**属性来对请求参数进行一些限制。可以要求必须具有某些参数，或者是某些参数必须是某个值，或者是某些参数必须不是某个值。

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {
    @RequestMapping(value = "/testParams",method = RequestMethod.GET,params = "code")
    public String testParams(){
        System.out.println("testParams处理了请求");
        return "testParams";
    }
}
~~~~

​	

如果是要求**不能有code**这个参数可以把改成如下形式

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {
    @RequestMapping(value = "/testParams",method = RequestMethod.GET,params = "!code")
    public String testParams(){
        System.out.println("testParams处理请求");
        return "testParams";
    }
}
~~~~

​	

如果要求有code这参数，并且这参数值必须**是某个值**可以改成如下形式

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {
    @RequestMapping(value = "/testParams",method = RequestMethod.GET,params = "code=wukong")
    public String testParams(){
        System.out.println("testParams处理请求");
        return "testParams";
    }
}
~~~~



如果要求有code这参数，并且这参数值必须**不是某个值**可以改成如下形式	

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {
    @RequestMapping(value = "/testParams",method = RequestMethod.GET,params = "code!=wukong")
    public String testParams(){
        System.out.println("testParams处理了请求");
        return "testParams";
    }
}
~~~~



#### 指定请求头

> 使用 **`headers`** 属性来对请求头进行一些限制。
>
> 例如，请求头中有  **`deviceType`** 能够被方法处理。

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/testHeaders",method = RequestMethod.GET,headers = "deviceType")
    public String testHeaders(){
        System.out.println("testHeaders处理了请求");
        return "testHeaders";
    }
}
~~~~



要求不能有**deviceType**这个请求头，可以写成如下代码

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/testHeaders",method = RequestMethod.GET,headers = "!deviceType")
    public String testHeaders(){
        System.out.println("testHeaders处理了请求");
        return "testHeaders";
    }
}
~~~~



如果要求有deviceType这个请求头，并且其值必须**是某个值**可以改成如下形式

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/testHeaders",method = RequestMethod.GET,headers = "deviceType=ios")
    public String testHeaders(){
        System.out.println("testHeaders处理请求");
        return "testHeaders";
    }
}
~~~~



如果要求有deviceType这个请求头，并且其值必须**不是某个值**可以改成如下形式

~~~~java
@RestController
@RequestMapping("/test")
public class TestController {
    
    @RequestMapping(value = "/testHeaders",method = RequestMethod.GET,headers = "deviceType!=ios")
    public String testHeaders(){
        System.out.println("testHeaders处理了请求");
        return "testHeaders";
    }
}
~~~~



#### 指定请求头Content-Type

>  使用 **`consumes`** 属性来对 **`Content-Type`** 这个请求头进行一些限制。



指定请求头中的Content-Type必须为 **multipart/from-data** ，代码如下

~~~~java
    @RequestMapping(value = "/testConsumes",method = RequestMethod.POST,consumes = "multipart/from-data")
    public String testConsumes(){
        System.out.println("testConsumes处理了请求");
        return "testConsumes";
    }
~~~~

要求请求头Content-Type的值必须**不能为某个multipart/from-data**则可以改成如下形式：

~~~~java
    @RequestMapping(value = "/testConsumes",method = RequestMethod.POST,consumes = "!multipart/from-data")
    public String testConsumes(){
        System.out.println("testConsumes处理了请求");
        return "testConsumes";
    }
~~~~

<br>

### @PathVariable  获取请求参数

> 该接口可以用来根据id查询用户。请求路径要求为  /user  ，请求方式要求为GET。

~~~~java
@RestController
public class UserController {

    @RequestMapping(value = "/user/{id}",method = RequestMethod.GET)
    public String findUserById( @PathVariable("id")Integer id){
        System.out.println(id);
        return "findUserById";
    }
}
~~~~

##### 

>  根据id和username查询用户。请求路径要求为  /user  ，请求方式要求为GET。

~~~~java
@RestController
public class UserController {
  
    @RequestMapping(value = "/user/{id}/{name}",method = RequestMethod.GET)
    public String findUser(@PathVariable("id") Integer id,@PathVariable("name") String name){
        System.out.println(id);
        System.out.println(name);
        return "findUser";
    }
  
}

~~~~



<br>

### @RequestBody 获得请求体中的 json 参数

> 使用**@RequestBody**注解获取请求体中的数据。
>
> SpringBoot的web启动器已经默认导入了jackson的依赖，不需要再额外导入。
>
> **注意事项**
>
> 使用**@RequestBody**来获取请求体中 `Json` 数据，并且进行转换，要求请求头 `Content-Type` 的值必须为  `application/json` 。

#### 请求体中数据

~~~~json
{"name":"wukongnotnull","age":18}
~~~~



#### User 实体

~~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Integer id;
    private String name;
    private Integer age;
}

~~~~

​		

#### 获取参数封装成实体对象

> 如果我们想把Json数据获取出来封装User对象,我们可以这样定义方法：

~~~~~java
@RestController
public class UserController {
  
    @RequestMapping(value = "/user",method = RequestMethod.POST)
    public String insertUser(@RequestBody User user){
        System.out.println(user);
        return "insertUser";
    }
}
~~~~~



> 或者， 获取参数封装成 `Map` 集合
>
> ~~~~java
>     @RequestMapping(value = "/user",method = RequestMethod.POST)
>     public String insertUser(@RequestBody Map map){
>         System.out.println(map);
>         return "insertUser";
>     }
> ~~~~
>
> 



#### 请求体中参数为集合

请求体中数据

~~~~java
[{"name":"悟空","age":100},{"name":"猪八戒","age":200},{"name":"唐僧","age":30}]
~~~~

方法定义

~~~~java
    @RequestMapping(value = "/users",method = RequestMethod.POST)
    public String insertUsers(@RequestBody List<User> users){
        System.out.println("insertUsers");
        System.out.println(users);
        return "insertUser";
    }
~~~~

<br>



### @RequestParam 获取 QueryString 格式参数 

> `QueryString` 格式参数为
>
> http://localhost:8080/testRequestParam?id=1&name=wukongnotnull&likes=eat&likes=drink&likes=play



#### 各个参数单独获取

> 方法中参数名和请求参数名一致时，可以省略 `**@RequestParam**` 注解。

~~~~java
    @RequestMapping("/testRequestParam")
    public String testRquestParam(Integer id, String name, String[] likes){
        System.out.println("testRquestParam");
        System.out.println(id);
        System.out.println(name);
        System.out.println(Arrays.toString(likes));
        return "testRquestParam";
    }

~~~~



> 方法中参数名和请求参数名一致时，需要添加  `**@RequestParam**` 注解。

~~~~java
    @RequestMapping("/testRequestParam")
    public String testRquestParam(@RequestParam("id") Integer uid,@RequestParam("name") String uname, @RequestParam("likes")String[] ulikes){
        System.out.println(uid);
        System.out.println(name);
        System.out.println(Arrays.toString(likes));
        return "testRquestParam";
    }
~~~~



#### 获取参数封装成实体对象

> 把这些参数封装到 `User` 对象中。

User 实体

> 实体类中的成员变量名要和请求参数名一致，并且要提供对应的set/get方法。

~~~~java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Integer id;
    private String name;
    private Integer age;
    private String[] likes;
}
~~~~

​	

Controller

~~~~java
    @RequestMapping("/testRequestParam")
    public String testRquestParam(User user){
        System.out.println("testRquestParam");
        System.out.println(user);
        return "testRquestParam";
    }
~~~~



测试 url

~~~~java
http://localhost:8080/testRequestParam?id=1&name=wukongnotnull&likes=eat&likes=drink&likes=play
~~~~

<br>

#### 相关注解其他属性

##### required

​	代表是否必须，默认值为true也就是必须要有对应的参数。如果没有就会报错。

​	如果对应的参数可传可不传则可以把其设置为fasle

例如：

~~~~java
    @RequestMapping("/testRquestParam")
    public String testRquestParam(@RequestParam(value = "id",required = false) Integer uid,@RequestParam("name") String name, @RequestParam("likes")String[] likes){
        System.out.println("testRquestParam");
        System.out.println(uid);
        System.out.println(name);
        System.out.println(Arrays.toString(likes));
        return "testRquestParam";
    }
~~~~



##### defaultValue

​	如果对应的参数没有，我们可以用defaultValue属性设置默认值。

例如：

~~~~java
    @RequestMapping("/testRquestParam")
    public String testRquestParam(@RequestParam(value = "id",required = false,defaultValue = "777") Integer uid,@RequestParam("name") String name, @RequestParam("likes")String[] likes){
        System.out.println("testRquestParam");
        System.out.println(uid);
        System.out.println(name);
        System.out.println(Arrays.toString(likes));
        return "testRquestParam";
    }
~~~~





### @ResponseBody

> `RestFul` 风格请求，把返回数据转换成 `Json` ，放入响应体中。

~~~~java
@Controller
@RequestMapping("/response")
public class ResponseController {

    @RequestMapping("/user/{id}")
    @ResponseBody
    public User findById(@PathVariable("id") Integer id){
        User user = new User();
        return user;
    }
}
~~~~



