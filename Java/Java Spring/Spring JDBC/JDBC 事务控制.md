	![[截屏2023-07-23 21.09.56 1.png]]
	![[截屏2023-07-23 22.55.10.png]]![[截屏2023-07-23 22.55.40.png]]
# 面试题
#JDBC面试题 
![[截屏2023-07-23 22.56.26.png]]

# 事务处理架构
	对已有jdbc事务的处理包装

![[截屏2023-07-23 23.05.16.png]]
## 核心接口

![[截屏2023-07-23 23.06.38.png]]
![[截屏2023-07-23 23.07.32.png]]

## TransactionStatus

![[截屏2023-07-23 23.09.04.png]]
```java
public interface TransactionStatus extends TransactionExecution, SavepointManager, Flushable {
	boolean hasSavepoint();
	@Override
	void flush();
```
### 父接口结构

![[截屏2023-07-23 23.13.15.png]]

# 编程式事务控制

## 面试题——PlatformTransactionManager 
#JDBC面试题
![[截屏2023-07-23 23.18.13.png]]

## 实例

### 配置类修改
![[截屏2023-07-23 23.17.45.png]]
### 事务操作的实现

##### 没有引入业务时
	数据需要满足的条件不满足时，不进行回滚
	
![[截屏2023-07-23 23.27.26.png]]
![[截屏2023-07-23 23.27.17.png]]
##### 引入业务

![[截屏2023-07-23 23.28.55.png]]

## TransactionStatus
	![[截屏2023-07-23 23.44.27.png]]

![[截屏2023-07-23 23.46.35.png]]
#### 常用方法
![[截屏2023-07-24 00.15.28.png]]

#### 具体操作
![[截屏2023-07-24 00.17.30.png]]
	![[截屏2023-07-24 00.19.37.png]]

#### savePoint
![[截屏2023-07-24 00.20.15.png]]

##### 增加保存点
![[截屏2023-07-24 00.21.48.png]]

## 事务隔离级别

	![[截屏2023-07-24 00.24.05.png]]
	保证数据的正确性
### 示例

#### 多线程实现
![[截屏2023-07-24 00.28.45.png]]![[截屏2023-07-24 00.28.37.png]]
	数据不同步
#### 事务级别隔离

##### 读取数据出现的问题

###### 脏读

![[截屏2023-07-24 00.30.39.png]]

###### 不可重复读

![[截屏2023-07-24 00.31.38.png]]

###### 幻读

![[截屏2023-07-24 00.32.10.png]]

##### 隔离级别的常量
	一般使用数据库的默认隔离级别
	
#JDBC面试题 
![[截屏2023-07-24 00.32.50.png]]
###### mysql 查看隔离级别

![[截屏2023-07-24 00.34.14.png]]

## 事务传播机制

	![[截屏2023-07-24 00.36.24.png]]

![[截屏2023-07-24 14.04.58.png]]
### 实操
![[截屏2023-07-24 14.13.25.png]]
	使用嵌套事务管理，父调用出现了错误，子业务同时会进行回滚处理
### 只读事务处理
	所有数据不运行更新操作，只能够进行读取的操作控制，通过setReadOnly方法
	
![[截屏2023-07-24 14.20.39.png]]
	在查询操作过程中需要只读进行控制

# 注解式事务处理
	配置简单
## Transactional 

### 源码分析
```java
package org.springframework.transaction.annotation;
import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.aot.hint.annotation.Reflective;
import org.springframework.core.annotation.AliasFor;
import org.springframework.transaction.TransactionDefinition;
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@Reflective
public @interface Transactional {
	@AliasFor("transactionManager")
	String value() default "";
	@AliasFor("value")
	String transactionManager() default "";
	String[] label() default {};
	Propagation propagation() default Propagation.REQUIRED;
	Isolation isolation() default Isolation.DEFAULT;
	int timeout() default TransactionDefinition.TIMEOUT_DEFAULT;
	String timeoutString() default "";
	boolean readOnly() default false;
	Class<? extends Throwable>[] rollbackFor() default {};
	String[] rollbackForClassName() default {};
	Class<? extends Throwable>[] noRollbackFor() default {};
	String[] noRollbackForClassName() default {};

}
```
### 结构
![[截屏2023-07-24 14.35.47.png]]

## 模拟业务层
![[截屏2023-07-24 14.20.39 1.png]]
## 模拟测试
![[截屏2023-07-24 14.41.27.png]]

# AOP实现事务控制
	![[截屏2023-07-24 14.43.07.png]]

## 服务实现类
	无任何事务处理

![[截屏2023-07-24 14.44.59.png]]
## 使用tx配置事务管理
![[截屏2023-07-24 14.47.56.png]]
	有异常则全部回滚

# 基于Bean配置实现事务管理

## 核心事务配置
![[截屏2023-07-24 14.56.23.png]]
## 切面配置信息
![[截屏2023-07-24 14.59.36.png]]

