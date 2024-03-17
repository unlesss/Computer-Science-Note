# 注解解析

![[截屏2023-07-27 15.28.51.png]]

## 直接响应
	程序修改后需要重新部署，热部署需要单独设置

	项目中多个控制层的类中每个类都有不同的访问路径，需要采用SpringBoot本身约定进行代码结构优化（父包启动类，子包组件类）

**保证当前项目中只存在一个程序启动类**
多个启动类需要手动选择
# 注解

## @SpringBootApplication
```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })//扫描包配置
public @interface SpringBootApplication {}
```

使用路径进行测试访问

