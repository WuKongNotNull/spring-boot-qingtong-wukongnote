# Spring Boot 整合 Freemarker

## Freemarker 介绍

> 官方网站  https://freemarker.apache.org/
>
> 中文网站  http://freemarker.foofun.cn/toc.html

<br>

## 集成 Freemarker 视图

`Spring Boot`内部支持 `Freemarker` 视图技术的集成，并提供了自动化配置类 `FreeMarkerAutoConﬁguration` ，借助自动化配置可以很方便的集成Freemarker 视图到SpringBoot环境中。

### **pom 依赖**

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-freemarker</artifactId>
    </dependency>
```

<br>

### **修改 Freemarker 配置信息** 

> Freemarker 默认视图路径为 resources/templates 目录(由自动化配置类FreemarkerProperties 类决定)， 该目录可以在application.yml 中进行修改。

```yaml
spring:
  freemarker:
    charset: utf-8
    content-type: text/html
    # 默认是  resources/templates ,改成 resources/views
    template-loader-path: classpath:/views/
    suffix: .ftl
```

<br>

### **controller**

```java
    @RequestMapping("/hello")
    public String showHello(){
            return "hello";
        }
```

<br>

### **resources/views/hello.ftl**

```html
<h1>
  hello ,悟空非空也，我是freemarker模板
</h1>
```



## Freemarker 常用语法

`FreeMarker` 三种语法：

1. 插值：`${...} `，`Freemarker` 会将里面的变量替换为实际值。

   

2. `FTL` 标签(`tags`)：结构上类似HTML的标签，都是用`<>`包裹起来，普通标签以`<#`开头，用户自定义标签以`<@`开头，如`<#if true>true thing</#if>`，`<@myWukong></@myWukong>`

> 标签（tags）和 指令（directive）的区分
>
> 举个例子：`<#if></#if>` 叫标签； 标签里面的 `if` 是指令。



3. 注释(`Comments`)：`FTL` 中的注释是：`<#-- 被注释掉的内容 -->`，对于注释，`FTL`会自动跳过，所以不会显示在生成的文本中。

> 注意：除以上三种语法之外的所有内容，皆被 FreeMarker 视为普通文本，普通文本会被原样输出



### if 指令

if 可以根据条件跳过模板中的某块代码，当 `userName` 值为 "悟空非空也" 或`wukongnotnull`时，用特殊样式展示，相关模板代码如下：

```xml
<p>你好，
    <#if userName == "悟空非空也">
        <strong>${userName}</strong>
    <#elseif userName == "wukongnotnull">
        <h1>${userName}</h1>
    <#else>
        ${userName}
    </#if>
</p>
```

<br>

### list 指令

list 用来遍历序列，其语法为：

```xml
<#list sequence as loopVariable>
    repeatThis
</#list>
```

后端 ` model` 里放入一个 `userList` 的集合

```java
model.addAttribute("userList",userService.getUserList());
```

可以直接使用下标访问集合中的某个元素：`${userList[0].name}`

也可以在模板中直接遍历展示：

```xml
<ol>
<#list userList as user>
    <li>
        姓名：${user.name}，年龄：${user.age}
    </li>
</#list>
</ol>
```

渲染出 HTML：

```xml
<ol>
  <li>
    姓名：wukong，年龄：100
  </li>
  <li>
    姓名：zhubajie，年龄：200
  </li>
  <li>
    姓名：shaseng，年龄：300
  </li>
</ol>
```

> 注意：假设 userList 是空的，渲染出页面为 `<ol></ol>`，如何规避，使用 items 标签

```xml
<#list userList>
    <ol>
        <#items as user>
            <li>
                姓名：${user.name}，年龄：${user.age}
            </li>
        </#items>
    </ol>
</#list>
```



<br>

### include 指令

`include`  指令把一个模板的内容插入到另一个模板中。（官方建议使用 import 代替）。
若每个页面都添加 ` footer`，可以编写一个公共的 `footer.ftl` 模板，每个页面直接引入即可。

```xml
<#include "footer.ftlh">
```



<br>

### import 指令

`import` 将模板中定义的变量引入当前模板并使用。

它和  `include` 的主要区别就是 ` import`  可以将变量封装到新的命名空间中。

例如：模板 ` /libs/commons.ftl` 里面写了很多公共方法，想在其他模板里引用，只需要在其他模板的开头写上：

```clean
<#import "/libs/commons.ftl" as com>
```

后续想使用/libs/commons.ftl 中的 copyright 方法，可以直接使用：

```xml
<@com.copyright date="3030-3032"/>
```

<br>

### assign 指令

assign 可以用来创建新的变量并为其赋值，语法如下：

```xml
<#assign name1=value1 name2=value2 ... nameN=valueN>
or
<#assign name1=value1 name2=value2 ... nameN=valueN in namespacehash>
or
<#assign name>
  capture this
</#assign>
or
<#assign name in namespacehash>
  capture this
</#assign>
```

举例：

```xml
<#--创建字符串-->
<#assign username = "悟空非空也">
<#--使用插值语法显示字符串-->
myStr:${myStr}
```



<br><br><br>