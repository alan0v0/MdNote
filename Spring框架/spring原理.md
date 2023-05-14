```java
DefaultResourceLoader implements ResourceLoader
// refresh方法，启动主流程，先实例化内部beanfactory然后load bean definition 
AbstractApplicationContext extends DefaultResourceLoader
		implements ConfigurableApplicationContext, DisposableBean
// refresh支持被重复调用，重新根据配置文件、配置类 生成新的内部beanfactory
AbstractRefreshableApplicationContext extends AbstractApplicationContext
AbstractRefreshableConfigApplicationContext extends AbstractRefreshableApplicationContext
		implements BeanNameAware, InitializingBean
AbstractXmlApplicationContext extends AbstractRefreshableConfigApplicationContext
FileSystemXmlApplicationContext extends AbstractXmlApplicationContext 
```







ApplicationContext bean管理委托给内部BeanFactory(DefaultListableBeanFactory)。初始化过程 一部分包括对该BeanFactory的配置。


ClassPathXmlApplicationContext过程包括：
```java
ApplicationContext applicationContext = 
  new ClassPathXmlApplicationContext(new String[]{""}, true, null);
 
```

refresh完成下面工作

1. 通知子类刷新内部beanfactory（完成对配置文件bean信息加载）,获取ConfigurableListableBeanFactory接口实例，对内部beanfactory作配置，
2. BeanFactoryPostProcessor实例化并调用，修改BeanFactory
3. BeanFactoryPostProcessor实例化并调用
4. 实例化剩余singlton bean

```java
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			// Prepare this context for refreshing.
			prepareRefresh();

			// Tell the subclass to refresh the internal bean factory.
          // 调用方法refreshBeanFactory 让子类refresh内部beanFactory
          // 1. 
          // 
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
          	

			// Prepare the bean factory for use in this context.
			prepareBeanFactory(beanFactory);

			try {
				// Allows post-processing of the bean factory in context subclasses.
				postProcessBeanFactory(beanFactory);

				// Invoke factory processors registered as beans in the context.
				invokeBeanFactoryPostProcessors(beanFactory);

				// Register bean processors that intercept bean creation.
				registerBeanPostProcessors(beanFactory);

				// Initialize message source for this context.
				initMessageSource();

				// Initialize event multicaster for this context.
				initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				onRefresh();

				// Check for listener beans and register them.
				registerListeners();

				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				finishRefresh();
			}

			catch (BeansException ex) {
				if (logger.isWarnEnabled()) {
					logger.warn("Exception encountered during context initialization - " +
							"cancelling refresh attempt: " + ex);
				}

				// Destroy already created singletons to avoid dangling resources.
				destroyBeans();

				// Reset 'active' flag.
				cancelRefresh(ex);

				// Propagate exception to caller.
				throw ex;
			}

			finally {
				// Reset common introspection caches in Spring's core, since we
				// might not ever need metadata for singleton beans anymore...
				resetCommonCaches();
			}
		}
	}
```




## bean 初始化

https://www.jianshu.com/p/43b65ed2e166

1. xml文件可指定自定义的 init / destroy method
2. IntializingBean DisposableBean 接口回调
3. JSR-250 @PostConstruct和@PreDestroy注解

一个bean同时实现上述三种形式,调用顺序为:

- 创建bean时

```
1. @PostConstruct 注解方式
2. InitializingBean 实现接口方式
3. custom init() 自定义初始化方法方式

```

- 销毁bean时

```
1. @PreDestroy 注解方式
2. DisposableBean  实现接口方式
3. custom destroy() 自定义销毁方法方式
```