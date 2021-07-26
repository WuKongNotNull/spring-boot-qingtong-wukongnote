# 发送纯文本邮件



**pom.xml**

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-mail</artifactId>
        </dependency>
```

<br>



**application.yml**

```yaml
spring.mail.host=smtp.qq.com
spring.mail.port=587

spring.mail.username=1390128154@qq.com
spring.mail.password=wvthphmybrqmhidf
spring.mail.default-encoding=UTF-8

spring.mail.properties.mail.smtp.connectiontimeout=5000
spring.mail.properties.mail.smtp.timeout=3000
spring.mail.properties.mail.smtp.writetimeout=5000
```



<br>



**service**

```java
package com.wukongnotnull.service;
//author: 悟空非空也（B站/知乎/公众号）
import org.springframework.stereotype.Service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.MailException;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSenderImpl;
import org.springframework.mail.javamail.MimeMessageHelper;
import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.io.File;
@Service
public class SendMailService {
        @Autowired
        private JavaMailSenderImpl mailSender;

        @Value("${spring.mail.username}")
        private String from;


        /**
         * 发送纯文本邮件
         * @param to       收件人地址
         * @param subject  邮件标题
         * @param text     邮件内容
         */
        public void sendSimpleEmail(String to,String subject,String text){
            // 定制纯文本邮件信息SimpleMailMessage
            SimpleMailMessage message = new SimpleMailMessage();
            message.setFrom(from);
            message.setTo(to);
            message.setSubject(subject);
            message.setText(text);
            try {
                // 发送邮件
                mailSender.send(message);
                System.out.println("纯文本邮件发送成功");
            } catch (MailException e) {
                System.out.println("纯文本邮件发送失败 "+e.getMessage());
                e.printStackTrace();
            }
        }

    }

```



<br>



**test**

```java
package com.wukongnotnull.service;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;

//author: 悟空非空也（B站/知乎/公众号）
@SpringBootTest
class SendMailServiceTest {

    @Autowired
   private SendMailService sendMailService ;

    @Autowired
    private  TemplateEngine templateEngine;

    FastStringWriter fastStringWriter;

    @Test
    void sendSimpleEmail() {
        sendMailService.sendSimpleEmail("1390128154@qq.com","标题123","内容123");
    }

  
}
```

<br>



# 发送带附件和图片的文件

**service**

```java
package com.wukongnotnull.service;
//author: 悟空非空也（B站/知乎/公众号）

import org.springframework.stereotype.Service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.MailException;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSenderImpl;
import org.springframework.mail.javamail.MimeMessageHelper;
import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.io.File;

@Service
public class SendMailService {



        @Autowired
        private JavaMailSenderImpl mailSender;

        @Value("${spring.mail.username}")
        private String from;



        /**
         * 发送复杂邮件（包括静态资源和附件）
         *
         * @param to           收件人地址
         * @param subject      邮件标题
         * @param text         邮件内容
         * @param filePath     附件地址
         * @param rscId        静态资源唯一标识
         * @param rscPath      静态资源地址
         */
        public void sendComplexEmail(
                String to,
                String subject,
                String text,
                String filePath,
                String rscId,
                String rscPath){
            // 定制复杂邮件信息 MimeMessage
            MimeMessage message = mailSender.createMimeMessage();
            try {
                // 使用 MimeMessageHelper 帮助类，并设置 multipart 多部件使用为 true
                MimeMessageHelper helper = new MimeMessageHelper(message, true);
                helper.setFrom(from);
                helper.setTo(to);
                helper.setSubject(subject);
                helper.setText(text, true);
                // 设置邮件静态资源
                FileSystemResource res = new FileSystemResource(new File(rscPath));
                helper.addInline(rscId, res);
                // 设置邮件附件
                FileSystemResource file = new FileSystemResource(new File(filePath));
                String fileName = filePath.substring(filePath.lastIndexOf(File.separator));
                helper.addAttachment(fileName, file);
                // 发送邮件
                mailSender.send(message);
                System.out.println("复杂邮件发送成功");
            } catch (MessagingException e) {
                System.out.println("复杂邮件发送失败 "+e.getMessage());
                e.printStackTrace();
            }
        }

    }

```



<br>



**test**

```java
package com.wukongnotnull.service;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;

//author: 悟空非空也（B站/知乎/公众号）
@SpringBootTest
class SendMailServiceTest {

    @Autowired
   private SendMailService sendMailService ;

    @Autowired
    private  TemplateEngine templateEngine;

    FastStringWriter fastStringWriter;



    @Test
    void sendComplexEmail() {
        StringBuffer text = new StringBuffer();
        String rscId = "img_2616";
        text.append("<html><head></head><body>");
        text.append("<div>这是邮件正文");
        text.append("<img src=cid:"+rscId+"/>");
        text.append("</body></html>");
        sendMailService.sendComplexEmail("1390128154@qq.com",
                "带附件的邮件标题",
                text.toString(),
                "/Users/mac/Downloads/信1901班级人员名单.xlsx",
                rscId,
                "/Users/mac/Downloads/IMG_2616.JPG");
    }

}
```



<br>



# 发送模板邮件

**pom.xml**

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```



<br>

**emailTemplate.html**

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org" >
<head>
    <meta charset="UTF-8">
    <title>email</title>
</head>
<body>
<div>
    <p><span th:text="${username}"></span>用户您好，你的验证码是 <span th:text="code"></span></p>
    <img src="../static/img/avcon.png" alt="加载失败">
</div>
</body>
</html>
```



<br>



**service**

```java
package com.wukongnotnull.service;
//author: 悟空非空也（B站/知乎/公众号）

import org.springframework.stereotype.Service;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.FileSystemResource;
import org.springframework.mail.MailException;
import org.springframework.mail.SimpleMailMessage;
import org.springframework.mail.javamail.JavaMailSenderImpl;
import org.springframework.mail.javamail.MimeMessageHelper;
import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.io.File;

@Service
public class SendMailService {

        @Autowired
        private JavaMailSenderImpl mailSender;

        @Value("${spring.mail.username}")
        private String from;


        /**
         * 发送模板邮件
         * @param to       收件人地址
         * @param subject  邮件标题
         * @param content  邮件内容
         */
        public void sendTemplateEmail(String to, String subject, String content) {
            MimeMessage message = mailSender.createMimeMessage();
            try {
                // 使用MimeMessageHelper帮助类，并设置multipart多部件使用为true
                MimeMessageHelper helper = new MimeMessageHelper(message, true);
                helper.setFrom(from);
                helper.setTo(to);
                helper.setSubject(subject);
                helper.setText(content, true);
                // 发送邮件
                mailSender.send(message);
                System.out.println("模板邮件发送成功");
            } catch (MessagingException e) {
                System.out.println("模板邮件发送失败 "+e.getMessage());
                e.printStackTrace();
            }
        }

    }

```



<br>



**test**

> import org.thymeleaf.TemplateEngine;
> import org.thymeleaf.context.Context;
>
> 两个类无法识别 ，后续解决



```java
package com.wukongnotnull.service;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;

//author: 悟空非空也（B站/知乎/公众号）
@SpringBootTest
class SendMailServiceTest {

    @Autowired
   private SendMailService sendMailService ;

    @Autowired
    private  TemplateEngine templateEngine;



    @Test
    void sendTemplateEmail() {
        String to="1390128154@qq.com";
        String subject="【模板邮件】标题";
        // 使用模板邮件定制邮件正文内容
        Context context = new Context();
        context.setVariable("username", "悟空非空也");
        context.setVariable("code", "456123");
        // 使用TemplateEngine设置要处理的模板页面
        String emailContent = templateEngine.process("emailTemplate", context);
        // 发送模板邮件
        sendMailService.sendTemplateEmail(to,subject,emailContent);
    }
}
```

