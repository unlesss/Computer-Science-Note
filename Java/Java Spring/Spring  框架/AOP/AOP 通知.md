# 后置通知
	aop:after 
	后置返回通知，后置异常通知，最终后置通知
	
![[截屏2023-07-21 15.28.59.png]]

## 实例

### 异常抛出
```java
@Override
    public String echo(String msg) {
        if (!msg.contains("why")) {
            throw new RuntimeException("没找到why");
        }
        return "[Echo] :" + msg;
    }
```
#### 修改配置类
```java
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd
                            http://www.springframework.org/schema/aop
                            https://www.springframework.org/schema/aop/spring-aop.xsd">
    <context:annotation-config>

    </context:annotation-config>
    <context:component-scan base-package="com.why.service"/>
    <bean id="serviceAdvice" class="com.why.service.advice.ServiceAdvice"/>
    <aop:config proxy-target-class="true">
        <aop:pointcut id="messagePointcut" expression="execution(public * com.why..service..*.*(..))"/>
        <aop:aspect ref="serviceAdvice">
            <aop:before method="beforeHandle" pointcut="execution(public * com.why..service..*.*(..)) and args(msg)"/>
            <aop:after method="afterHandle" pointcut="execution(public * com.why..service..*.*(..)) and args(msg)"/>
            <aop:after-returning method="afterReturningHandle" pointcut-ref="messagePointcut" returning="value"
                                 arg-names="value"/>
            <aop:after-throwing method="afterThrowHandle" pointcut-ref="messagePointcut" throwing="e" arg-names="e"/>
        </aop:aspect>
    </aop:config>
</beans>
```
	前置通知和后置通知配置即可出现，后置返回和异常抛出则需要条件

## 环绕通知
	可以直接完成之前的四类通知处理，自成体系，常见的代理结构设计实现方式
	切面处理程序设计，逻辑过于繁琐，又提供环绕通知处理模型，类似传统动态代理结构，开发者可自定义
	 
![[截屏2023-07-21 16.07.04.png]]
![[截屏2023-07-21 16.12.14.png]]
### 修改通知配置类
```java
{
    private static final Logger LOGGER = LoggerFactory.getLogger(ServiceAdvice.class);

    public Object handleRound(ProceedingJoinPoint point) throws Exception {
        LOGGER.debug("[环绕通知处理] 目标对象：{}", point.getTarget());
        LOGGER.debug("[环绕通知处理] 对象类型：{}", point.getKind());
        LOGGER.debug("[环绕通知处理] 切面表达式：{}", point.getStaticPart());
        LOGGER.debug("[环绕通知处理] 方法签名：{}", point.getSignature());
        LOGGER.debug("[环绕通知处理] 源代码定位：{}", point.getSourceLocation());
        LOGGER.info("[通知前置处理] 业务方法调用前，传递的参数内容: {}", Arrays.toString(point.getArgs()));
        Object returnValue = null;
        try {
            returnValue = point.proceed(point.getArgs());
        } catch (Throwable e) {
            LOGGER.info("[Exception] 业务方法产生异常，异常为{}", e.getMessage());
        }
        LOGGER.info("[返回通知] 方法执行完毕 {}", returnValue);
        return returnValue;
    }
}
```
### 修改xml
```xml

```