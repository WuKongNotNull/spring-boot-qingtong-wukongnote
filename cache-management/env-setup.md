## 基础环境搭建



创建项目

![image-20200630201135910](../img/image-20200630201135910-5969368.png)



配置文件配置

```properties
#mysql数据库连接配置
#spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/springbootdata?serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root

#显示使用JPA进行数据库查询
spring.jpa.show-sql=true
```



实体类

```java
package com.wukongnotnull.domain;



import javax.persistence.*;

@Entity(name = "t_comment")
public class Comment {
    @Id//注意导包 javax.persistence.Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    private String content;
    private String author;
    @Column(name = "a_id")//实现映射
    private Integer aId;//此属性名和表中的字段a_id不一致，不能自动映射，需要在application.properties中进行配置


    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getContent() {
        return content;
    }

    public void setContent(String content) {
        this.content = content;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }

    public Integer getaId() {
        return aId;
    }

    public void setaId(Integer aId) {
        this.aId = aId;
    }
}

```

编写自定义仓库类，继承JpaRepository

```java
package com.wukongnotnull.repository;

import com.wukongnotnull.domain.Comment;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.transaction.annotation.Transactional;

//接口遵循jpa的规则
public interface CommentRepository extends JpaRepository<Comment,Integer> {

    //modify comment according id and author
    @Transactional//进行事务管理
    @Modifying
    @Query("update t_comment c set c.author=?1 where c.id=?2")
    public int updateComment(String author,Integer id);

}

```



service层

```java
package com.wukongnotnull.service;

import com.wukongnotnull.domain.Comment;

import java.util.Optional;

public interface CommentService {
    //add
    public Comment addComment(Comment comment);
    //delete
    public void deleteComment(Integer id);

    //modify
    public int modifyComment(Integer id,String author);
    //query
    public Comment findById(Integer id);
}

```



```java
package com.wukongnotnull.service;

import com.wukongnotnull.domain.Comment;
import com.wukongnotnull.repository.CommentRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.Optional;

@Service
public class CommentServiceImpl implements  CommentService {

    @Autowired
    public CommentRepository commentRepository;

    //add
    public Comment addComment(Comment comment){
        return this.commentRepository.save(comment);
    }
    //delete
    public void deleteComment(Integer id){
        this.commentRepository.deleteById(id);
    }

    //modify
    public int modifyComment(Integer id,String author){
        return this.commentRepository.updateComment(author,id);
    }
    //query
    public Comment findById(Integer id){
        Optional<Comment> optional = this.commentRepository.findById(id);
        if(optional.isPresent()){
           return optional.get();

        }
        return null;
    };
}

```



controller层

```java
package com.wukongnotnull.controller;

import com.wukongnotnull.domain.Comment;
import com.wukongnotnull.service.CommentService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class CommentController {

    @Autowired
    private CommentService commentService;

    //query
    @GetMapping("/query/{id}")
    public Comment findById(@PathVariable("id") Integer id){
         return this.commentService.findById(id);
    }

    //modify
    @GetMapping("/modify/{id}/{author}")
    public int modifyBy(@PathVariable("id") Integer id,@PathVariable("author") String author){
        int i = this.commentService.modifyComment(id, author);
        return i;
    }

    //delete
    @GetMapping("/delete/{id}")
    public void deleteById(@PathVariable("id") Integer id){
        this.commentService.deleteComment(id);
    }

    //add

}

```

