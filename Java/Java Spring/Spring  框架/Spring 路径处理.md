	启动流程分析取决于运行环境，使用XML配置。查看Spring如何解析XML资源
可以使用两个处理类：
1. ClassPathXmlApplicationContext
2. FileSystemXmlApplicationContext
都继承**AbstractXmlApplicationContext**

# ClassPathXmlApplicationContext结构分析
## 继承结构

### 继承 AbstractRefreshableConfigApplicationContext
```java
public abstract class AbstractXmlApplicationContext extends AbstractRefreshableConfigApplicationContext
```
### 继承 AbstractRefreshableApplicationContext
```java
public abstract class AbstractRefreshableConfigApplicationContext extends AbstractRefreshableApplicationContext implements BeanNameAware, InitializingBean
```
### 继承 AbstractApplicationContext
```java
public abstract class AbstractRefreshableApplicationContext extends AbstractApplicationContext
```
### 继承 ConfigurableApplicationContext
```java
public abstract class AbstractApplicationContext extends DefaultResourceLoader
		implements ConfigurableApplicationContext
```
### 继承 ApplicationContext
```java
public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver
```
	具体初始化前提，需要先解析出所有的XML文件的路径，才可以通过Spring内置工具类进行XML配置内容解析

## 方法分析

### 构造方法
```java
	public ClassPathXmlApplicationContext(ApplicationContext parent) {
		super(parent);
	}

	public ClassPathXmlApplicationContext(String configLocation) throws BeansException {
		this(new String[] {configLocation}, true, null);
	}

	public ClassPathXmlApplicationContext(
			String[] configLocations, boolean refresh, @Nullable ApplicationContext parent)
			throws BeansException {

		super(parent);
		setConfigLocations(configLocations);//配置文件路径解析
		if (refresh) {
			refresh();//注解配置的模式进行Spring容器启动时，需要开发者自行调用此方法
		}
	}
	
	public ClassPathXmlApplicationContext(String... configLocations) throws BeansException {
		this(configLocations, true, null);
	}
```
### 面试题——为什么使用XML配置方式启动Spring容器时候不需要使用refresh方法？
#Spring面试题
	错误，都需要调用refresh方法，只是有些过程内对其调用进行隐藏
### setConfigLocation 
	解析文件路径核心方法
##### 源代码
```java
	private String[] configLocations;//保存所有配置文件路径

	public void setConfigLocations(@Nullable String... locations) {
		if (locations != null) {
			Assert.noNullElements(locations, "Config locations must not be null");
			this.configLocations = new String[locations.length];//字符串数组初始化
			for (int i = 0; i < locations.length; i++) {
				this.configLocations[i] = resolvePath(locations[i]).trim();//路径解析处理
			}
		}
		else {
			this.configLocations = null;
		}
	}

	protected String resolvePath(String path) {
		return getEnvironment().resolveRequiredPlaceholders(path);
	}
```
# 操作

![[截屏2023-07-20 10.41.23.png]]![[截屏2023-07-20 10.41.41.png]]