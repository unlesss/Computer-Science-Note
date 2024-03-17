![[截屏2023-07-19 09.14.59.png]]
# BeanDefinition源码分析

## 接口定义
```java
package org.springframework.beans.factory.config;
import org.springframework.beans.BeanMetadataElement;
import org.springframework.beans.MutablePropertyValues;
import org.springframework.core.AttributeAccessor;
import org.springframework.core.ResolvableType;
import org.springframework.lang.Nullable;
public interface BeanDefinition extends AttributeAccessor, BeanMetadataElement {
	String SCOPE_SINGLETON = ConfigurableBeanFactory.SCOPE_SINGLETON;
	String SCOPE_PROTOTYPE = ConfigurableBeanFactory.SCOPE_PROTOTYPE;//标记
	
	int ROLE_APPLICATION = 0;//角色应用
	int ROLE_SUPPORT = 1;//角色支持
	int ROLE_INFRASTRUCTURE = 2;
	
	void setParentName(@Nullable String parentName);//父结构Bean名称
	@Nullable
	String getParentName();//获取父结构Bean名称
	
	void setBeanClassName(@Nullable String beanClassName);//Bean类名称
	@Nullable
	String getBeanClassName();//获取保存的Bean名称，完整路径，可通过反射获取
	
	void setScope(@Nullable String scope);//scope
	@Nullable
	String getScope();
	
	void setLazyInit(boolean lazyInit);//设置延迟初始化
	boolean isLazyInit();
	
	void setDependsOn(@Nullable String... dependsOn);//设置依赖关联（配置顺序）
	@Nullable
	String[] getDependsOn();//获取依赖结构
	
	void setAutowireCandidate(boolean autowireCandidate);//自动注入处理
	boolean isAutowireCandidate();//判断是否进行自动注入
	
	void setPrimary(boolean primary);//设置主要注入Bean
	boolean isPrimary();
	
	void setFactoryBeanName(@Nullable String factoryBeanName);//FactoryBean名称
	@Nullable
	String getFactoryBeanName();
	
	void setFactoryMethodName(@Nullable String factoryMethodName);//工厂方法名称
	@Nullable
	String getFactoryMethodName();//返回工厂方法名称
	
	ConstructorArgumentValues getConstructorArgumentValues();//构造方法参数
	default boolean hasConstructorArgumentValues() {//是否存在构造方法参数
		return !getConstructorArgumentValues().isEmpty();
	}
	MutablePropertyValues getPropertyValues();//获取属性内容
	default boolean hasPropertyValues() {
		return !getPropertyValues().isEmpty();
	}
	
	void setInitMethodName(@Nullable String initMethodName);//设置初始化方法
	@Nullable
	String getInitMethodName();
	
	void setDestroyMethodName(@Nullable String destroyMethodName);//设置销毁方法名称
	@Nullable
	String getDestroyMethodName();
	
	void setRole(int role);//角色
	int getRole();
	
	void setDescription(@Nullable String description);//设置描述信息
	@Nullable
	String getDescription();

	ResolvableType getResolvableType();//解析类型
	
	boolean isSingleton();
	boolean isPrototype();
	boolean isAbstract();
	
	@Nullable
	String getResourceDescription();//资源描述
	@Nullable
	BeanDefinition getOriginatingBeanDefinition();//获取原始Bean定义
}
```
	配置文件或者注解配置的所有配置项，都保存在该接口实例中，反射处理时，只需要获取到接口实例（程序的级别），完成对象的整体管理
	如果启动Spring容器，可直接获取BeanDefinition实例
## 操作实例
	使用AnnotationConfigApplicationContext注册方式启动Spring容器

![[截屏2023-07-26 10.44.45.png]]

## 面试题——Spring中所有Bean信息存储位置 
#Spring面试题 

![[截屏2023-07-19 09.27.34.png]]

# BeanDefinitionParserDelegate

![[截屏2023-07-19 09.30.34.png]]
![[截屏2023-07-19 09.30.43.png]]
![[截屏2023-07-19 09.31.04.png]]
## 具体解析操作

### 获取Document接口

![[截屏2023-07-19 09.36.07.png]]
### 获取XMLReader上下文环境

![[截屏2023-07-19 09.37.10.png]]
### 具体获取操作
	获取Xml文件中的配置，使用XmlBeanDefinitionReader进行解析

![[截屏2023-07-26 11.09.48.png]]
	![[截屏2023-07-26 11.12.26.png]]

