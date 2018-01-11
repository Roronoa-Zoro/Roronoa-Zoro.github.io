---
layout: post
title:  "spring aop笔记"
date:   2016-11-03 +0800
categories: spring
tags: spring
author: Jimmy Lee
---

* content
{:toc}

### 前言

这个用的少, 总是忘, 还是记录一下吧.

### 拦截自定义注解, 执行方法  
自定义注解
```java
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface MethodCostTag {

    String methodName() default "";
} 
```  

拦截器, 注解了上面注解的方法会被拦截, 打印一下方法执行时间
```java
@Slf4j
@Aspect
@Component
public class MethodCostAop {
    @Around(value = "@annotation(methodCostTag)")
    public Object aroundMethod(ProceedingJoinPoint proceedingJoinPoint, MethodCostTag methodCostTag)  throws Throwable{
        String methodName = methodCostTag.methodName();
        long t1 = System.currentTimeMillis();
        Object obj = proceedingJoinPoint.proceed(proceedingJoinPoint.getArgs());
        log.info("method execution cost:method is :{}, cost is :{}", methodName, (System.currentTimeMillis() - t1));
        return obj;
    }
}
```
 

### 拦截自定义注解, 拦截类里的方法
定义一个注解
```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface ClassMethodTag {
}
```

定义拦截方法, 需要使用@within, 则标注了注解的类里的方法都会被拦截
```java
@Before(value = "@within(classMethod)")
public void beforeMethod(JoinPoint joinPoint, ClassMethodTag classMethod)  throws Throwable{
    System.out.println("afasfadasdfsdadfadfdsfaf====" + System.currentTimeMillis());
}
```


### 直接使用controllerAdvice
controller方法签名, 标注一下@Valid注解, 对象的熟悉需要标注校验注解(@NotNull和其他)   
```java
@PostMapping("/routeAgreement")
public RouterAgreementRes routeAgreement(@Valid @RequestBody RouterAgreement agreement) throws BusinessException {
	
}
```

拦截逻辑, 参数校验不通过直接返回特定的错误码
```java
/**
 * 对controller的参数对象进行统一校验，出现异常后的统一返回错误信息
 */
@Slf4j
@RestControllerAdvice
public class ParamValidationControllerAdvice {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public BaseRes validExceptionHandler(MethodArgumentNotValidException e) {
        log.info("validate controller param fail:{}", e.getMessage());
        BaseRes res = new BaseRes();
        res.setRespCode(ErrorCodeEnum.AgreementParameterNotMatch.getCode());
        res.setRespMsg(ErrorCodeEnum.AgreementParameterNotMatch.getDesc());
        return res;
    }
}
```
