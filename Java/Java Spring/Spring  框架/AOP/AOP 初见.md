	面向切面，动态代理机制实现，最大应用在于事务控制

# 模拟不使用AOP机制

## 接口及其实现
```java
public interface IDeptService {
    public boolean add(Dept dept);
}

public class DeptServiceImpl implements IDeptService {
    //需要注入DAO接口实例，才可以实现数据层代码的开发处理
    private static final Logger LOGGER = LoggerFactory.getLogger(DeptServiceImpl.class);

    @Override
    public boolean add(Dept dept) {
        //手工开启事务
        LOGGER.info("[部门增加] 部门编号 :{},部门名称 :{},部门位置 :{}", dept.getDeptno(), dept.getDname(), dept.getLoc());//核心事务
        //手工的提交或者回滚事务
        return false;
    }
}
```
## 代理子类
```java
package com.why.service.proxy;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ServiceProxy implements InvocationHandler {
    private static final Logger LOGGER = LoggerFactory.getLogger(ServiceProxy.class);
    private Object target;

    public Object bind(Object target) {
        this.target = target;
        return Proxy.newProxyInstance(target.getClass().getClassLoader(), target.getClass().getInterfaces(), this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //判断当前操作方法之中是否需要进行代理控制
        if (this.isOpenTransaction(method)) {
            LOGGER.info("[JDBC 事务] 当前的业务方法需要进行事务的开启");
        }
        Object result = method.invoke(this.target, args);
        if(this.isOpenTransaction(method)){
            LOGGER.info("[JDBC 事务] 当前的业务方法需要事务提交或者是回滚处理")
        }
        return result;
    }

    private boolean isOpenTransaction(Method method) {
        //如果需要修改，或者要更多的处理方法
        if (method.getName().startsWith("add")) {
            return true;
        }
        return false;
    }
}
```
## 业务代理包装 需要工厂类
```java
package com.why.factory;

import com.why.service.proxy.ServiceProxy;

import java.lang.reflect.InvocationTargetException;

public class ServiceFactory {
    private ServiceFactory() {
    };

    private static <T> T getInstance(Class<T> clazz) throws Exception {
        Object target = clazz.getConstructors()[0].newInstance();//对象实例化
        return (T) new ServiceProxy().bind(target);//绑定对象
    }
}
```
## 应用类
```java
package com.why.main;

import com.why.factory.ServiceFactory;
import com.why.service.IDeptService;
import com.why.service.Impl.DeptServiceImpl;
import com.why.vo.Dept;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class StartService {
    private static final Logger LOGGER = LoggerFactory.getLogger(StartService.class);

    public static void main(String[] args) throws Exception {
        IDeptService deptService = ServiceFactory.getInstance(DeptServiceImpl.class);
        Dept dept = new Dept();
        dept.setDeptno(10L);
        dept.setLoc("长春");
        dept.setDname("测试");
        LOGGER.info("[部门数据增加] {}", deptService.add(dept));
    }
}
```
## 结果
![[截屏2023-07-21 11.48.54.png]]

![[截屏2023-07-21 11.49.40.png]]

# AOP简介
## 学习建议
![[截屏2023-07-21 11.51.05.png]]
## AOP技术本质

![[截屏2023-07-21 13.11.36.png]]![[截屏2023-07-21 13.13.59.png]]
![[截屏2023-07-21 13.16.53.png]]
