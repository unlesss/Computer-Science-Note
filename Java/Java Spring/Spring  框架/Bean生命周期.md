	![[截屏2023-07-18 13.43.23.png]]
	![[bean生命周期.webp|3700]]
	
## Java 对象初始化的不同形式
![[截屏2023-07-18 13.43.32.png|1025]]

[[原型模式]]
[[单例模式]]
## Prototype原型模式

### 示例
![[截屏2023-07-14 11.34.16.png]]
![[截屏2023-07-14 11.34.52.png]]
### 解释
bean 编号相同，使用单例模式设计
### 面试题 spring 中的bean是否为线程安全
#Spring面试题 
单例模式中使用懒汉式设计需要考虑线程安全，但是spring对象中的实例默认在spring容器启动时初始化，不涉及多线程访问处理问题
#### 可能出现——bean中提供全局属性的定义

### 不使用单例模式 scope=prototype

配置bean的scope属性，bean的使用范围 scope=prototype
编号不同，但**不是每一次都产生一个新对象，使用new**，而是使用了原形设计模式
![[截屏2023-07-14 11.50.40.png]]
![[截屏2023-07-14 13.34.39.png]]
**spring中使用的是深克隆**
#深克隆 

### Java中如何处理对象的克隆
[[Java 克隆机制]]
#### 面试题——解释spring中bean的范围（scope）
#Spring面试题 
1. 默认情况下为了减少bean的数量，spring采用单例设计模式，不管使用多少次的bean，都只会存在一个实例
2. 另外一种采用原型设计模式，每次可以深克隆出一个新对象bean
3. 如果想进行bean的配置，可以使用scope属性或者@Scope注解，默认为单例singleton
4. 后续说明spring源码的相关结构

### Bean延迟初始化

所有配置的bean信息会在spring容器启动时进行初始化处理操作，这个初始化包括了对象的实例化，属性的配置以及一系列后续的处理流程
#### 仅初始化spring容器
![[截屏2023-07-14 13.53.33.png]]
#### 不在spring容器中初始化bean实例——延迟初始化
**添加属性 lazy-init = true**
所谓延迟初始化是将整个bean处理放在容器启动后进行，**意味着spring在启动时需要不同状态的判断**

## XML方式销毁和初始化

### xml文件注册bean的初始化和销毁方法

![[截屏2023-07-18 13.54.36.png]]
#### main 初始化spring容器

![[截屏2023-07-18 13.55.12.png]]
#### 销毁方式

##### context.close() & context.registerShutdownHook()
	实际上都调用 doclose()函数
##### doClose()
```Java
protected void doClose() {  
// Check whether an actual close attempt is necessary...  
if (this.active.get() && this.closed.compareAndSet(false, true)) {  
if (logger.isDebugEnabled()) {  
logger.debug("Closing " + this);  
}  
  
try {  
// Publish shutdown event.  
publishEvent(new ContextClosedEvent(this));  
}  
catch (Throwable ex) {  
logger.warn("Exception thrown from ApplicationListener handling ContextClosedEvent", ex);  
}  
  
// Stop all Lifecycle beans, to avoid delays during individual destruction.  
if (this.lifecycleProcessor != null) {  
try {  
this.lifecycleProcessor.onClose();  
}  
catch (Throwable ex) {  
logger.warn("Exception thrown from LifecycleProcessor on context close", ex);  
}  
}  
  
// Destroy all cached singletons in the context's BeanFactory.  
destroyBeans();  
  
// Close the state of this context itself.  
closeBeanFactory();  
  
// Let subclasses do some final clean-up if they wish...  
onClose();  
  
// Reset local application listeners to pre-refresh state.  
if (this.earlyApplicationListeners != null) {  
this.applicationListeners.clear();  
this.applicationListeners.addAll(this.earlyApplicationListeners);  
}  
  
// Switch to inactive.  
this.active.set(false);  
}  
}
```

## 接口方式进行销毁和初始化
![[截屏2023-07-18 14.22.11.png]]
	避免bean注解和xml文件依赖
	
### 复写接口
![[截屏2023-07-18 14.29.41.png]]

## JSR-250 方式管理生命周期
	annotation-api 依赖

![[截屏2023-07-18 14.44.58.png]]

### bean配置注入@Resource
	等同于Autowired + Qualified

需要导入 jarkata 包

## Bean初始化流程
	p89 重新复习

![[截屏2023-07-19 17.18.12.png]]



# Lifecycle接口

![[截屏2023-07-18 15.03.10.png]]

## 实现基本方法

![[截屏2023-07-18 15.08.02.png]]

### 根据不同环境，改变running的值

### 需要显式调用start()函数启动spring容器

**保持容器控制和生命周期控制的一致性**

## SmartLifecycle
![[截屏2023-07-18 15.16.13.png|1150]]
### 实现SmartLifecycle接口

#### isAutoStart()方法
![[截屏2023-07-18 15.19.08.png]]
#### getPhase()方法
	返回排序，值越小越优先进行启动

*默认值 Integer.MIN_VALUE Integer.MAX_VALUE*     最小和最大值
**先启动后关闭**

## SmartInitializingSingleton 回调处理


![[截屏2023-07-18 15.37.16.png]]
![[截屏2023-07-18 15.37.04.png]]
### 实现接口
在bean实体类定义中
![[截屏2023-07-18 15.39.18.png]]
	在实例化完成之后，仅对singleton对象有效，使用该接口进行回调处理

当实例化的属性值不合理时，对属性值进行更改

## BeanDefinitionReader 
![[截屏2023-07-18 15.58.22.png]]

### 接口定义
![[截屏2023-07-18 16.04.32.png]]
	根据指定资源路径读取所需要Bean数据信息
	

![[截屏2023-07-18 16.47.17.png]]

接口一般提供抽象类，提供中间过渡，即使接口增加某些抽象方法，不需要全部子类进行覆写

## XMLBeanDefinitionReader
![[截屏2023-07-18 17.36.36.png]]
![[截屏2023-07-18 17.36.15.png]]
![[截屏2023-07-18 17.33.25.png]]














