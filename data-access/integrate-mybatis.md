## 使用注解的方式整合MyBatis



**mapper接口**

```java
package com.wukongnotnull.mapper;
    /* 
    author: 悟空非空也（B站/知乎/公众号） 
    */

import com.wukongnotnull.domain.Comment;
import org.apache.ibatis.annotations.*;


@Mapper
public interface CommentMapper {

    // 查询
    @Select("select * from b_comment where id = #{id}")
    public Comment queryComment(Integer id);

    //删除
    @Delete("delete from b_comment where id=#{id}")
    public int deleteComment(@Param("id") Integer id);

    // 修改
    @Update("update b_comment set content = #{content}, author = #{author}  where id = #{id}")
    public int updateComment(Comment comment);

    // 添加，注意是 #{articleId} ，不是#{article_id}
    @Insert("insert into b_comment(content,author,article_id) values(#{content},#{author},#{articleId})")
    public int insertComment(Comment comment);

}

```

<br>

**测试**

```java
package com.wukongnotnull.mapper;

import com.wukongnotnull.domain.Comment;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

    /*
    author: 悟空非空也（B站/知乎/公众号） 
    */
@SpringBootTest
class CommentMapperTest {

    @Autowired
    private CommentMapper commentMapper;

    @Test
    void queryComment() {
        System.out.println(commentMapper.queryComment(2));
    }

    @Test
    void deleteComment() {
        System.out.println(commentMapper);
        System.out.println(commentMapper.deleteComment(5));
    }

    @Test
    void updateComment(){
        Comment comment = new Comment();
        comment.setId(2);
        comment.setContent("11111");
        comment.setAuthor("111");
        System.out.println(commentMapper.updateComment(comment));
    }

    @Test
    void insertComment(){
        Comment comment = new Comment();
        comment.setContent("add11111");
        comment.setAuthor("add11111");
        comment.setArticleId(1);
        System.out.println(commentMapper.insertComment(comment));
    }

}

```



## 使用配置文件的方式整合MyBatis

<br>

**在全局配置文件配置**



> mapper-locations: classpath:mapper/** 
>
> 该位置是在 resources 文件夹下新建 mapper 目录



```properties
mybatis:
  configuration:
    map-underscore-to-camel-case: true
  mapper-locations: classpath:mapper/**
  type-aliases-package: com.wukongnotnull.domain
```

<br>

**编写ArticleMapper接口**

```java
package com.wukongnotnull.mapper;
   /*
   author: 悟空非空也（B站/知乎/公众号）
   */

import com.wukongnotnull.domain.Article;
import org.apache.ibatis.annotations.Mapper;
import org.springframework.stereotype.Component;

import java.util.List;

// 不要忘记 @Mapper
@Mapper
public interface ArticleMapper {

   // 查询单文章
   Article getArticleById(int id);

   // 查询文章列表
   List<Article> getArticleList();

   // 修改文章
   int updateArticle(Article article);

   // 添加文章
    int addArticle(Article article);

    // 删除文章
    int delArticle(int id);

}

```

<br>

**编写接口对应的xml配置文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.wukongnotnull.mapper.ArticleMapper">

    <select id="getArticleById" resultType="Article" parameterType="int">
        select * from b_article where id = #{id}
    </select>

    <select id="getArticleList" resultType="Article">
        select  * from b_article
    </select>

    <update id="updateArticle" parameterType="Article">
        update b_article set title = #{title}, content = #{content} where id = #{id}
    </update>

    <insert id="addArticle" parameterType="Article">
        insert into b_article(title,content) values(#{title},#{content})
    </insert>

    <delete id="delArticle" parameterType="int" >
        delete from b_article where id = #{id}
    </delete>

</mapper>
```

<br>

**测试**

```java
package com.wukongnotnull.mapper;

import com.wukongnotnull.domain.Article;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.junit.jupiter.api.Assertions.*;

  /*
  author: 悟空非空也（B站/知乎/公众号）
  */
@SpringBootTest
class ArticleMapperTest {

    @Autowired
    private  ArticleMapper articleMapper;

    @Test
    void delArticle(){
        System.out.println(articleMapper.delArticle(3));
    }

    @Test
    void addArticle(){
        Article article = new Article();
        article.setTitle("add 1029");
        article.setContent("add content 1030");
        System.out.println(articleMapper.addArticle(article));
    }

    @Test
    void updateArticle(){
        Article article = new Article();
        article.setId(2);
        article.setTitle("update 1026...");
        article.setContent("content 1026");
        System.out.println(articleMapper.updateArticle(article));
    }

    @Test
    void getArticleList(){
        System.out.println(articleMapper.getArticleList());
    }

    @Test
    void getArticleById() {
        System.out.println(articleMapper.getArticleById(1));
    }
}

```







<br>

<br>

<br>