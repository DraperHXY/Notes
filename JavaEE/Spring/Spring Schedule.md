> 那天遇见了一个问题，我设置了一个定时器，每 5s 执行一次，但是第一个任务执行了 8s，第二个会怎样？



主要内容：

* 定时器的模式
* 定时器的实现



## 一、定时器的模式

* ``fixedDelay`` - 任务见固定间隔
* ``cron`` - 只在设置的时间运行，错过则在下一个预定的时间运行
* ``fixedRate`` - 计划周期，但错过后，下个任务提前执行，把时间追回来

### 1. fixedDelay

这种方式属于最简单的方式

```java
@Scheduled(fixedDelay = 3000)
public void task1() throws InterruptedException {
    long time = (long) (Math.random() * 1000);
    log.error("start  sleep {}", System.currentTimeMillis());
    log.error("sleep time = {}", time);
    Thread.sleep(time);
    log.error("finish sleep {}", System.currentTimeMillis());
}
```

其运行结果如下

```java
c.d.w.t.ScheduleTask                     : start  sleep 1555488894443
c.d.w.t.ScheduleTask                     : sleep time = 614
c.d.w.t.ScheduleTask                     : finish sleep 1555488895059
c.d.w.t.ScheduleTask                     : start  sleep 1555488898062
c.d.w.t.ScheduleTask                     : sleep time = 259
c.d.w.t.ScheduleTask                     : finish sleep 1555488898327
c.d.w.t.ScheduleTask                     : start  sleep 1555488901332
c.d.w.t.ScheduleTask                     : sleep time = 383
c.d.w.t.ScheduleTask                     : finish sleep 1555488901719
c.d.w.t.ScheduleTask                     : start  sleep 1555488904723
c.d.w.t.ScheduleTask                     : sleep time = 97
c.d.w.t.ScheduleTask                     : finish sleep 1555488904820
c.d.w.t.ScheduleTask                     : start  sleep 1555488907821
c.d.w.t.ScheduleTask                     : sleep time = 26
c.d.w.t.ScheduleTask                     : finish sleep 1555488907850
```

总结：每个任务之间间隔为 fixedDelay



###2. cron

cron 表达式很多，但不复杂。[Cron 表达式](<https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/support/CronSequenceGenerator.html>)

```java
@Scheduled(cron = "0/5 * * * * ?")
public void task2() throws InterruptedException {
    long time = (long) (Math.random() * 100000);
    log.error("start  sleep {}", System.currentTimeMillis());
    log.error("sleep time = {}", time);
    Thread.sleep(time);
    log.error("finish sleep {}", System.currentTimeMillis());
}
```



```java
c.d.w.t.ScheduleTask                     : start  sleep 1555489230001
c.d.w.t.ScheduleTask                     : sleep time = 83142
c.d.w.t.ScheduleTask                     : finish sleep 1555489313144
c.d.w.t.ScheduleTask                     : start  sleep 1555489315000
c.d.w.t.ScheduleTask                     : sleep time = 8426
c.d.w.t.ScheduleTask                     : finish sleep 1555489323428
c.d.w.t.ScheduleTask                     : start  sleep 1555489325005
c.d.w.t.ScheduleTask                     : sleep time = 58675
c.d.w.t.ScheduleTask                     : finish sleep 1555489383682
c.d.w.t.ScheduleTask                     : start  sleep 1555489385005
c.d.w.t.ScheduleTask                     : sleep time = 87765
c.d.w.t.ScheduleTask                     : finish sleep 1555489472773
c.d.w.t.ScheduleTask                     : start  sleep 1555489475003
c.d.w.t.ScheduleTask                     : sleep time = 96440
```

总结： 只在设置的时间运行，错过则在下一个预定的时间运行

### 3. fixedRate

```java
@Scheduled(fixedRate = 5 * 1000)
public void task3() throws InterruptedException {
    long time = (long) (Math.random() * 10000);
    log.error("start  sleep {}", System.currentTimeMillis());
    log.error("sleep time = {}", time);
    Thread.sleep(time);
    log.error("finish sleep {}", System.currentTimeMillis());
}v
```

总结：与第一个 fixedDelay 是比较相似，但不同的地方，在于如果错过了时间，后一个任务就会立即执行，得把时间追回来