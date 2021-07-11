## 定时任务介绍

https://www.jianshu.com/p/1defb0f22ed1



## 9.4 定时任务实现

**新建一个定时任务管理的业务处理类ScheduledTaskService**，并在该类中编写对应的定时任务处理方法。使用@Scheduled注解声明了三个定时任务方法，这三个方法定制的执行规则基本相同，都是每隔1分钟重复执行一次定时任务，在使用fixedDelay属性的方法scheduledTaskAfterSleep()中，使用Thread.sleep(10000)模拟该定时任务处理耗时为10秒钟。

service

```java
@Service
public class ScheduledTaskService {

    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    private Integer count1 = 1;
    private Integer count2 = 1;
    private Integer count3 = 1;


    //每隔1分钟，执行一次
    @Scheduled(fixedRate = 60000)
    public void scheduledTaskImmediately() {
        System.out.println(String.format("fixedRate第%s次执行，当前时间为：%s",
                count1++, dateFormat.format(new Date())));
    }
    //每隔1分10秒（1分钟定时+业务处理时间10秒），执行一次
    @Scheduled(fixedDelay = 60000)
    public void scheduledTaskAfterSleep() throws InterruptedException {
        System.out.println(String.format("fixedDelay第%s次执行，当前时间为：%s",
                count2++, dateFormat.format(new Date())));
        Thread.sleep(10000);//模拟业务处理时间
    }
    //整秒输出
    @Scheduled(cron = "0 * * * * *")
    public void scheduledTaskCron(){
        System.out.println(String.format("cron第%s次执行，当前时间为：%s",
                count3++, dateFormat.format(new Date())));
    }



}
```

![image-20200724172612847](../img/image-20200724172612847-5995377.png)

