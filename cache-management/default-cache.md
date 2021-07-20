## Spring Boot默认缓存体验

**spring boot 自带缓存机制，可以使用默认缓存，避免每次查询都去数据库访问一边，避免资源消耗**

<br>

**在启动类上添加注解 @EnableCaching**

```java
package com.wukongnotnull;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;

@SpringBootApplication
@EnableCaching //开启springboot默认缓存
public class RedisSpringbootApplication {

    public static void main(String[] args) {
        SpringApplication.run(RedisSpringbootApplication.class, args);
    }

}

```

<br>

**在service层的实现类的方法上添加注解 @Cacheable，开启缓存空间**

```java
package com.wukongnotnull.service;

import com.wukongnotnull.domain.Comment;
import com.wukongnotnull.repository.CommentRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cache.annotation.Cacheable;
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
    @Cacheable(cacheNames = "comment")//创建缓存空间，并命名为comment
    public Comment findById(Integer id){
        Optional<Comment> optional = this.commentRepository.findById(id);
        if(optional.isPresent()){
           return optional.get();

        }
        return null;
    };
}

```

<br>

**测试，多次查询，在控制台只打印第一次查询日志**



<br>

<br>

<br>