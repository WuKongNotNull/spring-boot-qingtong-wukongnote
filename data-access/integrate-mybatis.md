## 使用注解的方式整合MyBatis



mapper接口

```java
package com.wukongnotnull.mapper;


import com.wukongnotnull.domain.Comment;
import org.apache.ibatis.annotations.*;


//表示该类是mybatis接口文件，需要被springboot扫描
@Mapper
public interface CommentMapper {

   //查询
   @Select("select * from t_comment where id=#{id}")
   public Comment getCommentById(@Param("id") Integer id);

   //增加
   @Insert("insert into t_comment(content,author,a_id)values(#{content},#{author},#{aId})")
    public int addComment(Comment comment);

   //修改
    @Update("update t_comment set content=#{content},author=#{author},a_id=#{aId} where id=#{id}")
    public int modifyComment(Comment comment);

   //删除
    @Delete("delete from t_comment where id=#{id}")
    public int deleteComment(@Param("id") Integer id);



}

```



测试

```java
package com.siliang;

import com.siliang.domain.Comment;
import com.siliang.mapper.CommentMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import javax.annotation.Resource;


@SpringBootTest
class MyspringbootMybatisApplicationTests {

    //此处使用@Autowired也可以，但是会标红
    @Resource
    private CommentMapper commentMapper;

   private Comment comment=new Comment();


    @Test
    void selectTest() {

        Comment comment = commentMapper.getCommentById(1);
        System.out.println(comment);

    }

    @Test
     void addTest(){

        comment.setContent("this article is very good");
        comment.setAuthor("libai");
        comment.setaId(1);
        int i = this.commentMapper.addComment(comment);
        System.out.println("add====>"+i);

    }

    @Test
     void updateTest(){
        comment.setId(1);
        comment.setContent("这部书太恐怖");
        comment.setAuthor("libai2");
        comment.setaId(1);
        int i = this.commentMapper.modifyComment(comment);
        System.out.println("update====>"+i);
    }

    @Test
    void deleteTest(){
        int i = this.commentMapper.deleteComment(6);
        System.out.println("delete===>"+i);

    }

}

```



## 使用配置文件的方式整合MyBatis



在全局配置文件配置

```properties
#配置mybatis的xml配置文件路径
mybatis.mapper-locations=classpath:mapper/**
#配置xml配置文件中实体类的别名
mybatis.type-aliases-package=com.siliang.domain
```



编写ArticleMapper接口

```java
package com.siliang.mapper;


import com.siliang.domain.Article;
import org.apache.ibatis.annotations.Mapper;

@Mapper
public interface ArticleMapper {

    //查询
    public Article getArticleById(Integer id);
}
```



编写接口对应的xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.siliang.mapper.ArticleMapper">
    <resultMap id="collectComment" type="com.siliang.domain.Article">
        <id column="id" property="id"/>
        <result column="title" property="title"/>
        <result column="content" property="content"/>
        <collection property="commentList" ofType="com.siliang.domain.Comment">
            <id column="c_id" property="id"/>
            <result column="c_content" property="content"/>
            <result column="author" property="author"/>
            <result column="a_id" property="aId"/>
        </collection>

    </resultMap>
    <select id="getArticleById" resultMap="collectComment" parameterType="int">
    SELECT a.*,c.`id` AS c_id,c.`content` as c_content,c.`author`,c.`a_id`
    FROM t_article a,t_comment c
    WHERE a.`id`=c.`a_id`
    AND a.`id`=#{id}
  </select>
</mapper>
```



测试

```java
package com.siliang;

import com.siliang.domain.Article;
import com.siliang.mapper.ArticleMapper;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

import javax.annotation.Resource;

@SpringBootTest
public class ArticleTest {
    @Resource
    private ArticleMapper articleMapper;

    @Test
    void selectTest(){
        Article article = articleMapper.getArticleById(1);
        System.out.println(article);
    }
}

```

