# 定时任务（quartz）

#### 引入

```xml
<dependency>
    <groupId>org.quartz-scheduler</groupId>
    <artifactId>quartz</artifactId>
    <version>2.2.3</version>
</dependency>
```

#### 使用

分别创建调度器，任务，触发器（定时器），将任务和触发器丢给调度器，实现定时任务

```java
~ TestQuartz.class

// import static 为静态导入，直接导入静态方法
import static org.quartz.JobBuilder.newJob;
import static org.quartz.SimpleScheduleBuilder.simpleSchedule;
import static org.quartz.TriggerBuilder.newTrigger;
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.Trigger;
import org.quartz.impl.StdSchedulerFactory;


//创建调度器
Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();

//定义一个触发器
Trigger trigger = newTrigger()
    .withIdentity("trigger1", "group1") //分组，定义名称和所属的组，将该触发器分在某个组里
    .startNow() 
    .withSchedule(simpleSchedule() // 设置调度间隔 
                  .withIntervalInSeconds(2) //每隔2秒执行一次
                  .withRepeatCount(10)) //总共执行11次，默认会执行一次
    .build();

//定义一个任务
JobDetail job = newJob(MailJob.class) //指定干活的类MailJob
    .withIdentity("mailjob1", "mailgroup") //定义任务名称和分组
    .usingJobData("email", "admin@10086.com") //定义属性
    .build();

// 修改已定义的属性
// jobDetail.getJobDataMap().put("email", "10010@163.com");

//调度加入这个job
scheduler.scheduleJob(job, trigger);

//启动
scheduler.start();

//等待20秒，让前面的任务都执行完了之后，再关闭调度器
Thread.sleep(20000);
scheduler.shutdown(true);
```

```java
~ MailJob.class
import org.quartz.Job;
import org.quartz.JobDetail;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;

// 实现job接口， 重写execute方法
public class MailJob implements Job {
    public void execute(JobExecutionContext context) throws JobExecutionException {
        // 创建任务上下文
        JobDetail detail = context.getJobDetail();
        // 获取任务属性，在创建任务时传递
        String email = detail.getJobDataMap().getString("email");
        
        SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
        String now = sdf.format(new  Date());
        
        System.out.printf("给邮件地址 %s 发出了一封定时邮件, 当前时间是: %s%n" ,email, now);
    }
}
```

#### 阻止job并发

定时任务触发的每个任务在没执行完时，下一个任务会照样执行，所以在执行某些不需要并发执行的任务时，需阻止并发

```java
// 在job类中上添加注解
@DisallowConcurrentExecution 
public class MailJob implements Job{}
```

#### job异常处理

job在运行中发生异常，有两种解决方案

- 重新执行
- 停止执行

```java
// 在catch中处理
// 重新执行
JobExecutionException je =new JobExecutionException(e);
je.setRefireImmediately(true);
// 停止执行
JobExecutionException je =new JobExecutionException(e);
je.setUnscheduleAllTriggers(true);
```

#### 中断job

主动中断任务

```java
// 需要将实现的job接口，改为InterruptableJob接口
// 实现InterruptableJob需要重写其两个接口excute和interrupt
import org.quartz.InterruptableJob;
import org.quartz.Job;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.quartz.UnableToInterruptJobException;

public class StoppableJob implements InterruptableJob {
    // 该属性控制定时任务是否关闭
	private boolean stop = false;
    public void execute(JobExecutionContext context) throws JobExecutionException {
    	while(true){
            // 中止任务
    		if(stop)
    			break;
    		try {
        		System.out.println("每隔1秒，进行一次检测，看看是否停止");
				Thread.sleep(1000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
    		System.out.println("持续工作中。。。");
    	}
    
    }
    // 该方法被调度器调用，停止任务
    public void interrupt() throws UnableToInterruptJobException {
    	System.out.println("被调度叫停");
        stop = true;
    }
}
```

```java
import static org.quartz.JobBuilder.newJob;
import static org.quartz.SimpleScheduleBuilder.simpleSchedule;
import static org.quartz.TriggerBuilder.newTrigger;
 
import org.quartz.InterruptableJob;
import org.quartz.JobDetail;
import org.quartz.Scheduler;
import org.quartz.SchedulerException;
import org.quartz.Trigger;
import org.quartz.impl.StdSchedulerFactory;

private static void stop() throws Exception {
    // 创建调度器
    Scheduler scheduler = StdSchedulerFactory.getDefaultScheduler();
	// 创建定时器
    Trigger trigger = newTrigger().withIdentity("trigger1", "group1")
        .startNow()
        .build();

    //定义一个JobDetail
    JobDetail job = newJob(StoppableJob.class)
        .withIdentity("exceptionJob1", "someJobGroup")
        .build();

    //调度加入这个job
    scheduler.scheduleJob(job, trigger);

    //启动
    scheduler.start();

    Thread.sleep(5000);
    System.out.println("过5秒，调度停止 job");

    //key 就相当于这个Job的主键，通过job的key来停止该job
    scheduler.interrupt(job.getKey());

    //等待20秒，让前面的任务都执行完了之后，再关闭调度器
    Thread.sleep(20000);
    scheduler.shutdown(true); 
}
```

