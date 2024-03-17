# refresh方法
```java
public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {//刷新时进行同步处理
			//启动步骤的记录，不同操作会有不同的启动步骤，这里主要做标记
			StartupStep contextRefresh = this.applicationStartup.start("spring.context.refresh");

			// Prepare this context for refreshing.
			prepareRefresh();//执行刷新处理

			// Tell the subclass to refresh the internal bean factory.
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();//获取Bean工厂

			// Prepare the bean factory for use in this context.
			prepareBeanFactory(beanFactory);//BeanFactory初始化

			try {
				// Allows post-processing of the bean factory in context subclasses.
				postProcessBeanFactory(beanFactory);//BeanFactoryPostProcessor 处理接口

				StartupStep beanPostProcess = this.applicationStartup.start("spring.context.beans.post-process");
				// Invoke factory processors registered as beans in the context.
				invokeBeanFactoryPostProcessors(beanFactory);//执行BeanFactoryPostProcessor 处理接口

				// Register bean processors that intercept bean creation.
				registerBeanPostProcessors(beanFactory);//注册BeanPostProcessor
				beanPostProcess.end();//Bean初始化完成

				// Initialize message source for this context.
				initMessageSource();//初始化MessageSource 接口实例

				// Initialize event multicaster for this context.
				initApplicationEventMulticaster();//初始化事件广播

				// Initialize other special beans in specific context subclasses.
				onRefresh();//初始化其他特殊的操作类，方法为空

				// Check for listener beans and register them.
				registerListeners();//注册监听

				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);//实例化剩余对象（非Lazy）

				// Last step: publish corresponding event.
				finishRefresh();// 相关事件发布
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {//日志记录
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();//销毁

				// Reset 'active' flag.
				cancelRefresh(ex);//设置存活状态标记

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();//重置缓冲
				contextRefresh.end();//结束
			}
		}
	}
```
	本质上还是建立在JVM上的应用

## StartupStep 
![[截屏2023-07-20 10.59.53.png]]![[截屏2023-07-20 11.04.06.png]]

## prepareRefresh 

### 源码分析

```java
protected void prepareRefresh() {
		// Switch to active.
		//同时提供一个closed标记
		this.startupDate = System.currentTimeMillis();
		this.closed.set(false);
		this.active.set(true);

		if (logger.isDebugEnabled()) {
			if (logger.isTraceEnabled()) {
				logger.trace("Refreshing " + this);
			}
			else {
				logger.debug("Refreshing " + getDisplayName());
			}
		}

		// Initialize any placeholder property sources in the context environment.
		//Spring容器 可以通过context命名空间配置所有要加载的资源信息
		initPropertySources();//表示保存有多个属性源

		// Validate that all properties marked as required are resolvable:
		// see ConfigurablePropertyResolver#setRequiredProperties
		//根据当前配置环境进行必要的属性验证处理
		getEnvironment().validateRequiredProperties();

		// Store pre-refresh ApplicationListeners...
		if (this.earlyApplicationListeners == null) {//判断是否有早期的存储事件
			//如果发现集合内容为空，需要立即准备出一个新的集合，有集合才能保存事件的监听
			this.earlyApplicationListeners = new LinkedHashSet<>(this.applicationListeners);
		}
		else {//有了事件的集合
			// Reset local application listeners to pre-refresh state.
			this.applicationListeners.clear();
			this.applicationListeners.addAll(this.earlyApplicationListeners);//保存早期事件
		}

		// Allow for the collection of early ApplicationEvents,
		// to be published once the multicaster is available...
		this.earlyApplicationEvents = new LinkedHashSet<>();//准备进行事件监听处理
	}

	private final AtomicBoolean active = new AtomicBoolean();//当前容器是否处于启动状态
	
```

![[截屏2023-07-20 11.28.45.png]]
### 面试题——在Spring容器里面启动耗时的统计操作由哪个方法发出开启
#Spring面试题
![[截屏2023-07-20 11.33.00.png]]
![[截屏2023-07-20 13.14.30.png]]
### initPropertySources
	在当前的Spring容器中没有任何代码提供，结合到WEB容器上可以进行Servlet初始化资源处理

![[截屏2023-07-20 13.17.11.png]]
![[截屏2023-07-20 13.21.04.png]]

![[截屏2023-07-20 13.21.35.png]]![[截屏2023-07-20 13.22.10.png]]

## obtainFreshBeanFactory
	BeanFactory是Spring中所有Bean管理的操作规范，其内部实现三级缓存，管理Bean对象以及解决各种可能存在的循环依赖初始化操作

![[截屏2023-07-20 13.34.01.png]]
![[截屏2023-07-20 13.34.50.png]]
![[截屏2023-07-20 13.36.36.png]]
![[截屏2023-07-20 13.37.14.png]]
![[截屏2023-07-20 13.38.52.png]]
![[截屏2023-07-20 13.42.15.png]]
![[截屏2023-07-20 13.42.07.png]]![[截屏2023-07-20 13.41.51.png]]![[截屏2023-07-20 13.43.30.png]]
![[截屏2023-07-20 13.48.34.png]]![[截屏2023-07-20 13.48.59.png]]
![[截屏2023-07-20 13.49.16.png]]
![[截屏2023-07-20 13.50.31.png]]![[截屏2023-07-20 13.50.56.png]]
![[截屏2023-07-20 13.52.35 1.png]]

## prepareBeanFactory

	预处理Bean
	
![[截屏2023-07-20 14.04.17.png]]

## prepareBeanFactory 
```java
protected void prepareBeanFactory(ConfigurableListableBeanFactory beanFactory) {
		// Tell the internal bean factory to use the context's class loader etc.
		beanFactory.setBeanClassLoader(getClassLoader());//配置类加载器
		beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));
		beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));//添加属性编辑器的注册管理

		// Configure the bean factory with context callbacks.
		//回调处理
		//BeanFactory准备完成之后，需要配置有各个Aware接口处理类，实现各类核心资源的注入
		beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
		beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
		beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
		beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
		beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
		beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
		beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);
		beanFactory.ignoreDependencyInterface(ApplicationStartupAware.class);

		// BeanFactory interface not registered as resolvable type in a plain factory.
		// MessageSource registered (and found for autowiring) as a bean.
		//设置解析处理中所有核心的结构
		beanFactory.registerResolvableDependency(BeanFactory.class, beanFactory);
		beanFactory.registerResolvableDependency(ResourceLoader.class, this);
		beanFactory.registerResolvableDependency(ApplicationEventPublisher.class, this);
		beanFactory.registerResolvableDependency(ApplicationContext.class, this);

		// Register early post-processor for detecting inner beans as ApplicationListeners.
		beanFactory.addBeanPostProcessor(new ApplicationListenerDetector(this));//早期监听

		// Detect a LoadTimeWeaver and prepare for weaving, if found.
		if (!NativeDetector.inNativeImage() && beanFactory.containsBean(LOAD_TIME_WEAVER_BEAN_NAME)) {
			beanFactory.addBeanPostProcessor(new LoadTimeWeaverAwareProcessor(beanFactory));//LTW技术
			// Set a temporary ClassLoader for type matching.
			beanFactory.setTempClassLoader(new ContextTypeMatchClassLoader(beanFactory.getBeanClassLoader()));
		}

		// Register default environment beans.
		if (!beanFactory.containsLocalBean(ENVIRONMENT_BEAN_NAME)) {//是否有指定的内容
			beanFactory.registerSingleton(ENVIRONMENT_BEAN_NAME, getEnvironment());//注册
		}
		if (!beanFactory.containsLocalBean(SYSTEM_PROPERTIES_BEAN_NAME)) {
			beanFactory.registerSingleton(SYSTEM_PROPERTIES_BEAN_NAME, getEnvironment().getSystemProperties());
		}
		if (!beanFactory.containsLocalBean(SYSTEM_ENVIRONMENT_BEAN_NAME)) {
			beanFactory.registerSingleton(SYSTEM_ENVIRONMENT_BEAN_NAME, getEnvironment().getSystemEnvironment());
		}
		if (!beanFactory.containsLocalBean(APPLICATION_STARTUP_BEAN_NAME)) {
			beanFactory.registerSingleton(APPLICATION_STARTUP_BEAN_NAME, getApplicationStartup());
		}
	}
```
### getClassLoader 源码
```java
public ClassLoader getClassLoader() {
		return (this.classLoader != null ? this.classLoader : ClassUtils.getDefaultClassLoader());
	}
```
	获取到类加载器就是当前资源的类加载器

#### 面试题——代理设计模式操作的织入实现一般会有三类方式
#Spring面试题 
![[截屏2023-07-20 14.17.38.png]]
![[截屏2023-07-20 14.28.33.png]]

## initMessageSource
	MessageSource 接口可以实现所有资源配置文件的加载，而后所有的资源就可以通过 MessageSource 接口提供的方法实现key与value内容的设置与返回
	
![[截屏2023-07-20 14.42.00.png]]
![[截屏2023-07-20 14.42.26.png]]
![[截屏2023-07-20 14.43.58.png]]

## initApplicationEventMulticaster

![[截屏2023-07-20 14.55.08.png]]

```java
protected void initApplicationEventMulticaster() {
		ConfigurableListableBeanFactory beanFactory = getBeanFactory();//获取BeanFactory
		//Spring内部保存有大量的系统实例，此时进行Bean是否存在的判断
		if (beanFactory.containsLocalBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME)) {
			this.applicationEventMulticaster =
					beanFactory.getBean(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, ApplicationEventMulticaster.class);//获取Bean实例
			if (logger.isTraceEnabled()) {
				logger.trace("Using ApplicationEventMulticaster [" + this.applicationEventMulticaster + "]");
			}
		}
		else {//没有指定Bean实例
			this.applicationEventMulticaster = new SimpleApplicationEventMulticaster(beanFactory);
			beanFactory.registerSingleton(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, this.applicationEventMulticaster);
			if (logger.isTraceEnabled()) {
				logger.trace("No '" + APPLICATION_EVENT_MULTICASTER_BEAN_NAME + "' bean, using " +
						"[" + this.applicationEventMulticaster.getClass().getSimpleName() + "]");
			}
		}
	}
```
	容器内部存在一个事件广播器，实现所有事件的发布处理

```java
@Override
	public void multicastEvent(ApplicationEvent event, @Nullable ResolvableType eventType) {
		ResolvableType type = (eventType != null ? eventType : ResolvableType.forInstance(event));
		Executor executor = getTaskExecutor();
		for (ApplicationListener<?> listener : getApplicationListeners(event, type)) {
			if (executor != null) {//存在线程池
				executor.execute(() -> invokeListener(listener, event));//监听调用
			}
			else {
				invokeListener(listener, event);
			}
		}
	}
```

## registerListeners
```java
// Initialize event multicaster for this context.
				initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				onRefresh();

				// Check for listener beans and register them.
				registerListeners();

				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);
```
### 源代码
```java
protected void registerListeners() {
		// Register statically specified listeners first.
		for (ApplicationListener<?> listener : getApplicationListeners()) {
			getApplicationEventMulticaster().addApplicationListener(listener);
		}

		// Do not initialize FactoryBeans here: We need to leave all regular beans
		// uninitialized to let post-processors apply to them!
		//获取当前Spring上下文之中所有指定类型的Bean名称实例
		String[] listenerBeanNames = getBeanNamesForType(ApplicationListener.class, true, false);
		for (String listenerBeanName : listenerBeanNames) {//Bean名称迭代
		//将当前获取到的Bean名称，加入当前的广播管理（便于广播消息，可以收到事件消息
			getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName);
		}

		// Publish early application events now that we finally have a multicaster...
		//有可能在Spring容器没有初始化完成时就保存了一些事件，获取这些事件信息
		Set<ApplicationEvent> earlyEventsToProcess = this.earlyApplicationEvents;
		this.earlyApplicationEvents = null;//清空早期的事情
		if (!CollectionUtils.isEmpty(earlyEventsToProcess)) {//当前有事件处理的集合项
			for (ApplicationEvent earlyEvent : earlyEventsToProcess) {//获取事件的类型
				getApplicationEventMulticaster().multicastEvent(earlyEvent);//广播时依据事件类型处理
			}
		}
	}

	public Collection<ApplicationListener<?>> getApplicationListeners() {
		return this.applicationListeners;
	}

	private final Set<ApplicationListener<?>> applicationListeners = new LinkedHashSet<>();

	public void addApplicationListener(ApplicationListener<?> listener) {
		synchronized (this.defaultRetriever) {
			// Explicitly remove target for a proxy, if registered already,
			// in order to avoid double invocations of the same listener.
			Object singletonTarget = AopProxyUtils.getSingletonTarget(listener);
			if (singletonTarget instanceof ApplicationListener) {
				this.defaultRetriever.applicationListeners.remove(singletonTarget);
			}
			this.defaultRetriever.applicationListeners.add(listener);
			this.retrieverCache.clear();
		}
	}
```
![[截屏2023-07-20 15.32.52.png]]

## finishBeanFactoryInitialization
	完成式所需要的方法
### 源代码
```java
String CONVERSION_SERVICE_BEAN_NAME = "conversionService";

protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
		// Initialize conversion service for this context.
		//初始化ConversationService服务处理（可以实现数据转型的服务支持），
		if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&
				beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {
			beanFactory.setConversionService(//进行Bean实例获取
					beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));
		}

		// Register a default embedded value resolver if no BeanFactoryPostProcessor
		// (such as a PropertySourcesPlaceholderConfigurer bean) registered any before:
		// at this point, primarily for resolution in annotation attribute values.
		if (!beanFactory.hasEmbeddedValueResolver()) {//是否包含内嵌的数据解析器
			beanFactory.addEmbeddedValueResolver(strVal -> getEnvironment().resolvePlaceholders(strVal));//进行解析器的注册
		}

		// Initialize LoadTimeWeaverAware beans early to allow for registering their transformers early.
		//在进行Java动态代理织入时一般考虑LTW的设计问题，此时解决LTW的注册
		String[] weaverAwareNames = beanFactory.getBeanNamesForType(LoadTimeWeaverAware.class, false, false);//获取要处理的bean实例类型
		for (String weaverAwareName : weaverAwareNames) {
			getBean(weaverAwareName);//获取Bean
		}

		// Stop using the temporary ClassLoader for type matching.
		beanFactory.setTempClassLoader(null);清除临时的类加载器

		// Allow for caching all bean definition metadata, not expecting further changes.
		beanFactory.freezeConfiguration();//缓存所有的Bean定义信息

		// Instantiate all remaining (non-lazy-init) singletons.
		beanFactory.preInstantiateSingletons();//实例化剩余的单例Bean
	}
```
### freezeConfiguration 由 ConfigurableListableBeanFactory 接口子类实现
```java
public void freezeConfiguration() {
		clearMetadataCache();
		this.configurationFrozen = true;
		this.frozenBeanDefinitionNames = StringUtils.toStringArray(this.beanDefinitionNames);
	}
```

```java
public void preInstantiateSingletons() throws BeansException {
		if (logger.isTraceEnabled()) {
			logger.trace("Pre-instantiating singletons in " + this);
		}

		// Iterate over a copy to allow for init methods which in turn register new bean definitions.
		// While this may not be part of the regular factory bootstrap, it does otherwise work fine.
		List<String> beanNames = new ArrayList<>(this.beanDefinitionNames);//获取Bean名称

		// Trigger initialization of all non-lazy singleton beans...
		for (String beanName : beanNames) {//Bean名称迭代 
			RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);//获取Bean定义
			if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {//各种判断
				if (isFactoryBean(beanName)) {//是否为FactoryBean
					Object bean = getBean(FACTORY_BEAN_PREFIX + beanName);
					if (bean instanceof SmartFactoryBean<?> smartFactoryBean && smartFactoryBean.isEagerInit()) {//是否为FactoryBean实例处理
						getBean(beanName);//获取Bean
					}
				}
				else {
					getBean(beanName);//直接获取Bean
				}
			}
		}

		// Trigger post-initialization callback for all applicable beans...
		for (String beanName : beanNames) {//CallBack 处理
			Object singletonInstance = getSingleton(beanName);//获取单例Bean信息
			if (singletonInstance instanceof SmartInitializingSingleton smartSingleton) {
				StartupStep smartInitialize = this.getApplicationStartup().start("spring.beans.smart-initialize")
						.tag("beanName", beanName);//配置启动步骤
				smartSingleton.afterSingletonsInstantiated();//SmartInitializingSingleton 接口初始化
				smartInitialize.end();
			}
		}
	}
```

## finishRefresh

### 源代码

```java
	protected void finishRefresh() {
		// Clear context-level resource caches (such as ASM metadata from scanning).
		clearResourceCaches();//清空资源缓存

		// Initialize lifecycle processor for this context.
		initLifecycleProcessor();//初始化生命周期处理

		// Propagate refresh to lifecycle processor first.
		getLifecycleProcessor().onRefresh();//生命周期处理刷新

		// Publish the final event.
		publishEvent(new ContextRefreshedEvent(this));//发布一个容器处理事件（刷新完成）
	}


	protected void initLifecycleProcessor() {
		ConfigurableListableBeanFactory beanFactory = getBeanFactory();//获取BeanFactory
		if (beanFactory.containsLocalBean(LIFECYCLE_PROCESSOR_BEAN_NAME)) {
		//获取所有的LifecycleProcessor接口实例
			this.lifecycleProcessor =
					beanFactory.getBean(LIFECYCLE_PROCESSOR_BEAN_NAME, LifecycleProcessor.class);
			if (logger.isTraceEnabled()) {
				logger.trace("Using LifecycleProcessor [" + this.lifecycleProcessor + "]");
			}
		}
		else {//没有发现指定的Bean存在
			DefaultLifecycleProcessor defaultProcessor = new DefaultLifecycleProcessor();
			defaultProcessor.setBeanFactory(beanFactory);//设置BeanFactory
			this.lifecycleProcessor = defaultProcessor;
			beanFactory.registerSingleton(LIFECYCLE_PROCESSOR_BEAN_NAME, this.lifecycleProcessor);
			if (logger.isTraceEnabled()) {
				logger.trace("No '" + LIFECYCLE_PROCESSOR_BEAN_NAME + "' bean, using " +
						"[" + this.lifecycleProcessor.getClass().getSimpleName() + "]");
			}
		}
	}

	public void onRefresh() {
		startBeans(true);
		this.running = true;
	}

	private void startBeans(boolean autoStartupOnly) {//Bean开始处理
		Map<String, Lifecycle> lifecycleBeans = getLifecycleBeans();
		Map<Integer, LifecycleGroup> phases = new TreeMap<>();

		lifecycleBeans.forEach((beanName, bean) -> {
			if (!autoStartupOnly || (bean instanceof SmartLifecycle smartLifecycle && smartLifecycle.isAutoStartup())) {
				int phase = getPhase(bean);//处理阶段
				phases.computeIfAbsent(
						phase,
						p -> new LifecycleGroup(phase, this.timeoutPerShutdownPhase, lifecycleBeans, autoStartupOnly)//增加生命周期管理组
				).add(beanName, bean);
			}
		});
		if (!phases.isEmpty()) {//阶段不为空
			phases.values().forEach(LifecycleGroup::start);//开始执行初始化调用
		}
	}
```
 
