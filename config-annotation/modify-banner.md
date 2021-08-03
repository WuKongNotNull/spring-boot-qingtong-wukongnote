## 修改/关闭 启动图标

**创建 /src/main/resources/banner.txt 文件**

http://patorjk.com/software/taag/#p=display&f=Graffiti&t=Type%20Something%20  

通过该网站设置banner图标样式，如下，复制粘贴到banner.txt文件中

```
                 __                                         __                 .__  .__   
__  _  ____ __  |  | ______   ____    ____     ____   _____/  |_    ____  __ __|  | |  |  
\ \/ \/ /  |  \ |  |/ /  _ \ /    \  / ___\   /    \ /  _ \   __\  /    \|  |  \  | |  |  
 \     /|  |  / |    <  <_> )   |  \/ /_/  > |   |  (  <_> )  |   |   |  \  |  /  |_|  |__
  \/\_/ |____/  |__|_ \____/|___|  /\___  /  |___|  /\____/|__|   |___|  /____/|____/____/
                     \/          \//_____/        \/                   \/                 
```



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

