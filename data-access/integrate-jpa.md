# 整合 JPA
## Spring Date JPA介绍

Spring Data 是Spring的一个子项目，旨在统一和简化各类型数据的持久化存储方式，而不拘泥于是关系型数据库还是NoSQL数据库。无论是哪种持久化存储方式，数据访问对象（Data Access Objects，DAO）都会提供对对象的增加、删除、修改和查询的方法，以及排序和分页方法等。
Spring Data 提供了基于这些层面的统一接口（如：CrudRepository、 PagingAndSortingRepository），以实现持久化的存储。



JPA（Java Persistence API）是Java的持久化API，用于对象的持久化。它是一个非常强大的ORM持久化的解决方案，免去了使用JDBCTemplate开发的编写脚本工作。JPA通过简单约定好接口方法的规则自动生成相应的JPQL语句，然后映射成POJO对象。

JPA是一个规范化接口，封装了Hibernate的操作作为默认实现，让用户不通过任何配置即可完成数据库的操作。

<br>

## 使用Spring Boot整合JPA

<br>

**pom**

```xml
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
```

<br>

**实体类和表进行映射**

```java
package com.wukongnotnull.domain;/* 
author: 悟空非空也（B站/知乎/公众号） 
*/

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.persistence.*;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Entity(name = "b_comment")
public class Comment {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;

    @Column(name = "content" )
    private String content;
    @Column(name = "author")
    private String author;
    //此属性名和表中的字段article_id不一致，不能自动映射，需要在application.yml中进行配置
    //mybatis.configuration.map-underscore-to-camel-case=true
    @Column(name = "article_id")
    private Integer articleId;


}

```

<br>

**自定义dao层的操作数据库的接口**

>  默认继承父接口，父接口提供了简单的增删改查方法，但是还不能满足业务需求，那就需要自定义方法吧

```java
package com.wukongnotnull.repository;
    /*
    author: 悟空非空也（B站/知乎/公众号）
    */
import com.wukongnotnull.domain.Comment;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;

import javax.transaction.Transactional;
import java.util.List;
import java.util.Optional;

public interface CommentRepository extends JpaRepository<Comment, Integer> {

    @Override
    List<Comment> findAll();

    @Override
    Optional<Comment> findById(Integer id);

    // 查询条件：作者不为空
    List<Comment> findByAuthorNotNull();
    

    @Override
    long count();

    @Override
    <S extends Comment> S saveAndFlush(S s);

    @Query("select c from b_comment c where c.articleId=?1")
    List<Comment> getCommentList(Integer articleId);

    @Query(value = "select * from b_comment c where c.article_id=?1",nativeQuery = true)
    List<Comment> getCommentList2(Integer articleId);

    @Transactional
    @Modifying
    @Query(value = "update b_comment set author =?1  where id =?2 ",nativeQuery = true)
    int updateComment(String author,Integer id);

    @Transactional
    @Modifying
    @Query(value = "delete from b_comment where id = ?1",nativeQuery = true)
    int deleteComment(Integer id);

    @Override
    <S extends Comment> S save(S entity);

}

```

<br>

**测试一下吧**

```java
package com.wukongnotnull.repository;

import com.wukongnotnull.domain.Comment;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

    /*
    author: 悟空非空也（B站/知乎/公众号） 
    */
@SpringBootTest
class CommentRepositoryTest {

    @Autowired
    private  CommentRepository commentRepository;

    @Test
    void findAll() {
        System.out.println(commentRepository.findAll());
    }

    @Test
    void findById() {
        System.out.println(commentRepository.findById(1));
    }

    @Test
    void findByAuthorNotNull(){
        System.out.println(commentRepository.findByAuthorNotNull());
    }

    @Test
    void count(){
        System.out.println(commentRepository.count());
    }

    @Test
    void getCommentList(){
        System.out.println(commentRepository.getCommentList(1));
    }

    @Test
    void getCommentList2(){
        System.out.println(commentRepository.getCommentList2(1));
    }

    @Test
    void updateComment(){
        System.out.println(commentRepository.updateComment("6666", 6));
    }

    @Test
    void deleteComment(){
        System.out.println(commentRepository.deleteComment(6));
    }

    @Test
    void save(){
        Comment comment = new Comment();
        comment.setAuthor("wukongnotnull0716");
        comment.setContent("content .... wukong ...");
        System.out.println(commentRepository.save(comment));
    }
}
```




<br>
<br>
<br>