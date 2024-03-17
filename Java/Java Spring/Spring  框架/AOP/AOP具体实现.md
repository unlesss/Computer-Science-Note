	通过XML文件配置

# 实例

## 新建接口
```java
package com.why.service;

public interface IMessageService {
    public String echo(String message);
}
```
## 实现子类
```java
package com.why.service.Impl;

import com.why.service.IMessageService;
import org.springframework.stereotype.Service;

@Service
public class MessageServiceImpl implements IMessageService {

    @Override
    public String echo(String msg) {
        return "[Echo] :" + msg;
    }
}
```
## AOP代理类
```java
package com.why.service.advice;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class ServiceAdvice {
    private static final Logger LOGGER = LoggerFactory.getLogger(ServiceAdvice.class);

    public void beforeHandle() {
        LOGGER.info("启用业务功能前置调用处理机制");
    }

    public void afterHandle() {
        LOGGER.info("启动业务功能后置调用处理机制");
    }
}
```
## 配置文件
```xml
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
    <aop:config>
        <aop:pointcut id="messagePointcut" expression="execution(public * com.why..service..*.*(..))"/>
        <aop:aspect ref="serviceAdvice">
            <aop:before method="beforeHandle" pointcut-ref="messagePointcut"/>
            <aop:after method="afterHandle" pointcut-ref="messagePointcut"/>
        </aop:aspect>
    </aop:config>
</beans>
```
## 测试类
```java
package com.why.test;

import com.why.service.IMessageService;
import org.junit.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit.jupiter.SpringExtension;
import org.springframework.test.context.junit.jupiter.SpringJUnitConfig;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring/spring-aop.xml"})
public class TestMessageService {
    private static final Logger LOGGER = LoggerFactory.getLogger(TestMessageService.class);

    @Autowired
    private IMessageService messageService;

    @Test
    public void testEcho() {
        LOGGER.info("[Echo调用] " + this.messageService.echo("www.why.com"));
    }
}

```
## 运行结果
![[截屏2023-07-21 14.34.55.png|975]]

# 配置深入
	源码深入研究AOP代理机制

![[截屏2023-07-21 14.45.02.png]]
## aop配置文件

### proxy-target-class 配置定义

![[截屏2023-07-21 14.46.07.png]]
### 观察代理类型
	proxy-target-class="false" 为默认值
#### 通过注入ApplicationContext 查看代理 
```java
package com.why.test;

import com.why.service.IMessageService;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:spring/spring-aop.xml"})
public class TestApplicationContext {
    private static final Logger LOGGER = LoggerFactory.getLogger(TestApplicationContext.class);

    @Autowired
    private ApplicationContext applicationContext;

    @Test
    public void testEcho() {
        LOGGER.info("[ApplicationContext 实现类] {}", this.applicationContext.getClass().getName());
        LOGGER.info("[IMessageService 实现类] {}", this.applicationContext.getBean("messageServiceImpl").getClass().getName());
    }
}

```
	结果：
	[ApplicationContext 实现类] org.springframework.context.support.GenericApplicationContext
	[IMessageService 实现类] jdk.proxy3.$Proxy23
	更改后：
	[ApplicationContext 实现类] org.springframework.context.support.GenericApplicationContext
	[IMessageService 实现类] com.why.service.Impl.MessageServiceImpl$$SpringCGLIB$$0
## 面试题——AOP代理配置选项
#Spring面试题 
![[截屏2023-07-21 15.03.44.png]]
 

