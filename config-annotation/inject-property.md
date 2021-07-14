# @ConfigurationProperties注入属性



第1种方法 properties

```properties
#给类属性赋值
person.id=1
person.name=wukongnotnull
person.family=baba,mama,gege,jiejie
person.hobbies=dance,game,110
person.map.level=3
person.map.salary=3000
person.pet.petName=wangcai
person.pet.petAge=3
```



第二种方式yml

```yaml
#给类属性赋值
person:
  id: 2
  name: wukongnotnull
  family: [baba,mama,gege,jiejie]
  hobbies: [dance,game,sing]
  map: {level: 3,salary: 3000}
  pet: {petName: wangcai, petAge: 3}

```



编写实体类

```java
package com.wukongnotnull.domain;
  /* 
  author: 悟空非空也（B站/知乎/公众号） 
  */

public class Pet {

    private String  petName;
    private  int  petAge;

    public String getPetName() {
        return petName;
    }

    public void setPetName(String petName) {
        this.petName = petName;
    }

    public int getPetAge() {
        return petAge;
    }

    public void setPetAge(int petAge) {
        this.petAge = petAge;
    }

    @Override
    public String toString() {
        return "Pet{" +
                "petName='" + petName + '\'' +
                ", petAge=" + petAge +
                '}';
    }
}


```



使用@Component,@ConfigurationProperties注解

```java
package com.wukongnotnull.domain;
  /* 
  author: 悟空非空也（B站/知乎/公众号） 
  */

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Arrays;
import java.util.List;
import java.util.Map;

@Component
@ConfigurationProperties(prefix = "person")
public class Person {

    private  int id;
    private  String name;
    private String[] family;
    private List hobbies;
    private Map map;
    private Pet pet;


    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public List getHobbies() {
        return hobbies;
    }

    public void setHobbies(List hobbies) {
        this.hobbies = hobbies;
    }

    public String[] getFamily() {
        return family;
    }

    public void setFamily(String[] family) {
        this.family = family;
    }

    public Map getMap() {
        return map;
    }

    public void setMap(Map map) {
        this.map = map;
    }

    public Pet getPet() {
        return pet;
    }

    public void setPet(Pet pet) {
        this.pet = pet;
    }

    @Override
    public String toString() {
        return "Person{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", family=" + Arrays.toString(family) +
                ", hobbies=" + hobbies +
                ", map=" + map +
                ", pet=" + pet +
                '}';
    }
}

```



测试类

```java
@SpringBootTest
class MySpringbootApplicationTests {

    @Autowired
    private  Person person;


    @Test
    public void personTest(){
        System.out.println(person);
    }
 
}
```





# @Value注入属性



@Value直接注入属性

```prope
#给基本数据数据类型注入
flag=wukongnotnull
```



测试

```java
@SpringBootTest
class MySpringbootApplicationTests {

  
    @Value(value = "${flag}") //直接注入，不需要setter方法
    private String flag;

    @Test
    public void showTest(){
        System.out.println("姓名为--->"+ flag);
    }
  
  
     @Value(value = "${person.family[0]}")
    private int familyName;


    @Test
    void valueTest(){
        System.out.println("family--->" + familyName);

    }

  

}
```



### @ConfigurationProperties注解支持JSR303数据校验

```
person:
  id: 2
  name: wukongnotnull
  family: [baba,mama,gege,jiejie]
  hobbies: [dance,game,110]
  map: {level: 3,salary: 3000}
  pet: {petName: wangcai, petAge: 3}
```



application.properties

```properties
#@ConfigurationProperties注解支持JSR303数据校验
user.email=1390128154@qq.com
```



pom

```pom
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
```





实体类

```java
package com.wukongnotnullg.myspringboot.domain;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;
import org.springframework.validation.annotation.Validated;

import javax.validation.constraints.Email;

@Component
@ConfigurationProperties(prefix = "user")
@Validated
public class User {

    @Email
    private  String email;

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```





test

```java
    @Test
    public void validateTest(){
        System.out.println(user.getEmail());
    }
```



# 两种注解对比分析

![1593089590122](../img/1593089590122.png)

