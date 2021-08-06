## 悟空编号

`sbqt002`

<br>



## 配套视频

> B 站链接：

<br>

```
introduction
```

## 修改启动图标



- **创建 /src/main/resources/banner.txt 文件**

- 通过如下网站设置banner图标样式

  > http://patorjk.com/software/taag/#p=display&f=Graffiti&t=Type%20Something%20  
  >
  > or
  >
  > http://www.degraeve.com/img2txt.php 

- 例如，如下图标复制到 banner.txt 文件中

  ```xml
                   __                                         __                 .__  .__   
  __  _  ____ __  |  | ______   ____    ____     ____   _____/  |_    ____  __ __|  | |  |  
  \ \/ \/ /  |  \ |  |/ /  _ \ /    \  / ___\   /    \ /  _ \   __\  /    \|  |  \  | |  |  
   \     /|  |  / |    <  <_> )   |  \/ /_/  > |   |  (  <_> )  |   |   |  \  |  /  |_|  |__
    \/\_/ |____/  |__|_ \____/|___|  /\___  /  |___|  /\____/|__|   |___|  /____/|____/____/
                       \/          \//_____/        \/                   \/                 
  ```





<br>



## 关闭启动图标

**banner图标如何关闭**

```java
@SpringBootApplication
public class App
{
    public static void main( String[] args )
    {
        SpringApplication springApplication = new SpringApplication(App.class);
        // 关闭 banner 图标
       springApplication.setBannerMode(Banner.Mode.OFF);
        springApplication.run();

    }
}

```

