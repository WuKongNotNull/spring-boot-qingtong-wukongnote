# 多文件上传

**controller**

```java
package com.wukongnotnull.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;
import java.util.UUID;

//author: 悟空非空也（B站/知乎/公众号）
@Controller
public class FileController {

    @GetMapping("/upload")
    public String upload(){
        return "upload";
    }

    @PostMapping("/uploadFile")
    public String uploadFile(
            @RequestParam("fileUpload") MultipartFile[] uploadFiles,
            Model model,
            HttpServletRequest request){
        model.addAttribute("uploadStatus","文件上传成功");
        for (MultipartFile file : uploadFiles) {
            String originalFilename = file.getOriginalFilename();// 获得文件名及后缀，例如  wukong.jpg
            //重新命名
            String newFilename = UUID.randomUUID() +"-"+ originalFilename;
            // 创建上传文件的存放目录 :设置为tomcat服务器中的路径
            String newPath = request.getServletContext().getRealPath("/upload");
            System.out.println(newPath);

            // String newPath ="D:\\upload";
            File file1 = new File(newPath);
            if (!file1.exists()){
                file1.mkdir();
            }

            //创建新文件，接受传递过来的文件流
            try {
                file.transferTo(new File(newPath,newFilename));
            } catch (IOException e) {
                e.printStackTrace();
                model.addAttribute("uploadStatus","上传失败");
            }
        }
        return "upload";
    }

}

```

<br>



**upload.html**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>动态添加文件上传列表</title>
<!--  <link th:href="@{/login/css/bootstrap.min.css}" rel="stylesheet">-->
  <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
 <!-- <script th:src="@{/login/js/jquery.min.js}"></script>-->
  <script src="https://cdn.staticfile.org/jquery/1.10.2/jquery.min.js"></script>
</head>
<body>
<div th:if="${uploadStatus}" style="color: red" th:text="${uploadStatus}">上传成功</div>
<form th:action="@{/uploadFile}" method="post" enctype="multipart/form-data">
    上传文件:&nbsp;&nbsp;<input type="button" value="添加文件" onclick="add()"/>
    <div id="file" style="margin-top: 10px;" th:value="文件上传区域">  </div>
    <input id="submit" type="submit" value="上传"
           style="display: none;margin-top: 10px;"/>
</form>
<script type="text/javascript">
    // 动态添加上传按钮
    function add(){
        var innerdiv = "<div>";
        innerdiv += "<input type='file' name='fileUpload' required='required'>" +
            "<input type='button' value='删除' onclick='remove(this)'>";
        innerdiv +="</div>";
        $("#file").append(innerdiv);
        // 打开上传按钮
        $("#submit").css("display","block");
    }
    // 删除当前行<div>
    function remove(obj) {
        $(obj).parent().remove();
        if($("#file div").length ==0){
            $("#submit").css("display","none");
        }
    }
</script>
</body>
</html>
```



<br><br><br>