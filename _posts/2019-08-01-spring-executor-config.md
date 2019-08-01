---
layout: post
title:  "spring-executor-config"
date:   2019-08-01 +0800
categories: JAVA
tags: java
author: Jimmy Lee
---

* content
{:toc}


#### spring-executor-config  

### 技术版本
jdk: 1.8  
springboot: 2.0.5.RELEASE

   
#### 纯配置代码,以后直接复制粘贴     
```java
@Slf4j
@Configuration
public class AsyncExecutorConfig implements AsyncConfigurer {

    @Value("${worker.core}")
    private Integer workerCore;
    @Value("${worker.max}")
    private Integer workerMax;
    @Value("${worker.queueCapacity}")
    private Integer workerQueueCapacity;

//    @Value("${executor.cost.enable}")
    private boolean costEnable;

    @Nullable
    @Override
    public Executor getAsyncExecutor() {
        return null;
    }
    @Bean(value = "asyncThreadPool", destroyMethod = "shutdown")
    public ThreadPoolTaskExecutor getTaskExecutor() {
        return create(workerCore,workerMax, workerQueueCapacity, 60, "async-sms-thread-");
    }

    private ThreadPoolTaskExecutor create(int core, int max, int queueCapacity, int waitSec, String threadPrefix) {
        ThreadPoolTaskExecutor delayThreadPool = new ThreadPoolTaskExecutor();
        delayThreadPool.setCorePoolSize(core);//当前线程数
        delayThreadPool.setMaxPoolSize(max);// 最大线程数
        delayThreadPool.setQueueCapacity(queueCapacity);//线程池所使用的缓冲队列
        delayThreadPool.setWaitForTasksToCompleteOnShutdown(true);//等待任务在关机时完成--表明等待所有线程执行完
        delayThreadPool.setAwaitTerminationSeconds(waitSec);// 等待时间 （默认为0，此时立即停止），并没等待xx秒后强制停止
        delayThreadPool.setThreadNamePrefix(threadPrefix);//  线程名称前缀
        delayThreadPool.setTaskDecorator(new MDCTaskDecorator());
        if (costEnable) {
//            delayThreadPool.setTaskDecorator(new ExecCostTaskDecorator());
        }
//        threadPool.setRejectedExecutionHandler(new BlockingRejectHandler());
//        delayThreadPool.setThreadFactory(new GuardThreadFactory(threadPrefix));
        delayThreadPool.initialize(); // 初始化
        return delayThreadPool;
    }
    @Nullable
    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new AsyncExecutorConfig.SpringAsyncExceptionHandler();
    }

    class SpringAsyncExceptionHandler implements AsyncUncaughtExceptionHandler {
        @Override
        public void handleUncaughtException(Throwable throwable, Method method, Object... obj) {
            log.error("Exception occurs in async method:{}", method.getName(),throwable);
        }
    }
}
```

