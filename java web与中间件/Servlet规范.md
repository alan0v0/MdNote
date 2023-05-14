参考链接：https://www.cnblogs.com/RunForLove/p/5688731.html

https://hacpai.com/article/1510120586530

servlet规范中文翻译：https://waylau.gitbooks.io/servlet-3-1-specification/docs/Preface/Preface.html

## 综述

1. 主要流程

Java EE 中 一个web应用通过war包进行部署，servlet 容器启动后，加载特定目录下所有 war包，管理应用实例，并将客户端请求分发给对应的应用。一个war包里面必须包含/WEB-INF/web.xml文件。web.xml文件声明本项目中所有servlet、filter、listener类。Tomcat 内部使用一个Context 容器管理war包所有servlet filter, 可利用Context 容器实现 不同servlet 、filter 之间交互。

2. DispatcherServlet启动过程

## web.xml 解析



### \<context-param>  \<listener>标签

```xml
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring/applicationContext.xml</param-value>
  </context-param>

  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
```

1. 启动一个WEB项目的时候,容器(如:Tomcat)会去读它的配置文件web.xml.读两个节点: `<listener></listener> `和 `<context-param></context-param>`

2. 紧接着,容器创建一个ServletContext(上下文),这个WEB项目所有部分都将共享这个上下文.

3. 容器将`<context-param></context-param>`转化为**键值对**,并交给ServletContext.

4. 容器创建`<listener></listener>`中的类实例,即创建监听.

5. 在监听中会有`contextInitialized(ServletContextEvent args)`初始化方法,在这个方法中获得`ServletContext = ServletContextEvent.getServletContext();`

6. 得到这个context-param的值之后,你就可以做一些操作了.注意,这个时候你的WEB项目还没有完全启动完成.这个动作会比所有的Servlet都要早.换句话说,这个时候,你对<context-param>中的键值做的操作,将在你的WEB项目完全启动之前被执行

7. 举例.你可能想在项目启动之前就打开数据库.
    那么这里就可以在<context-param>中设置数据库的连接方式,在监听类中初始化数据库的连接.

  ### 加载顺序

  web.xml 的加载顺序：context-param -> listener -> filter -> servlet 

  `<servlet>`中`<init-param>`元素 用于为servlet 提供标签

  ### \<load-on-startup>

  在servlet的配置当中，<load-on-startup>5</load-on-startup>的含义是：

  标记容器是否在启动的时候就加载这个servlet。

  当值为0或者大于0时，表示容器在应用启动时就加载这个servlet；

  当是一个负数时或者没有指定时，则指示容器在该servlet被选择时才加载。

  正数的值越小，启动该servlet的优先级越高。


## 1. Servlet

>JSRs: Java Specification Requests
>
>JSR 315: Java Servlet 3.0 Specification ( 最新是Servlet 4.0 )

Servlet，全称Java Servlet，是用Java编写的服务器端程序。其主要功能在于交互式地浏览和修改数据，生成动态Web内容。狭义的Servlet是指Java语言实现的一个接口，广义的Servlet是指任何实现了这个Servlet接口的类，一般情况下，人们将Servlet理解为后者。Servlet运行于支持Java的应用服务器中。从实现上讲，Servlet可以响应任何类型的请求，**但绝大多数情况下Servlet只用来扩展基于HTTP协议的Web服务器**。

```java
public interface Servlet {
    void init(ServletConfig var1) throws ServletException;

    ServletConfig getServletConfig();

    void service(ServletRequest var1, ServletResponse var2) throws ServletException, IOException;

    String getServletInfo();

    void destroy();
}
```



```xml
<servlet>
		<servlet-name>HServlet</servlet-name>
		<servlet-class>com.kim.demo.HelloServlet</servlet-class>
	 <load-on-startup>1</load-on-startup> 
</servlet>
<servlet-mapping>
		<servlet-name>HServlet</servlet-name>
		<url-pattern>/HServlet</url-pattern>
</servlet-mapping>
```

### HttpServlet



## 2. Filter



Filter 类可以改变一个 request和修改一个response. Filter 不是一个servlet,它不能产生一个response,它能够在一个request到达servlet之前预处理request,也可以在离开 servlet时处理response.

```xml
    <filter>
        <filter-name>shiroFilter</filter-name>
        <filter-class>org.apache.shiro.web.servlet.ShiroFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>shiroFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

filter的URL过滤模式是？

一个请求的url匹配多个filter ,filter执行顺序是？

## 3. Listener

Listener 是用于监听某些特定动作的监听器。当特定动作发生时，监听该动作的监听器就会自动调用对应的方法。
Servlet 中的 8 个 Listener 接口，可分为三类：

第一类，ServletContext 相关接口
ServletContextListener：用于监听 ServletContext 的启动和销毁。
ServletContextAttributeListener：用于监听 application 范围的属性变化。

第二类，HttpSession 相关接口
HttpSessionListener：用于监听 session 的**创建和销毁**。
HttpSessionIdListener：用于监听 session 的 **id 是否被更改**。
HttpSessionAttributeListener：用于监听 session 范围的**属性变化**。
HttpSessionActivationListener：用于监听绑定在 HttpSession 对象中的 JavaBean 状态。
HttpSessionBindingListener：用于监听对象与 session 的绑定和解绑。

第三类，ServletRequest 相关接口
ServletRequestListener：用于监听 ServletRequest 对象的初始化和销毁。
ServletRequestAttributeListener：用于监听 ServletRequest 对象的属性变化。

```xml
    <listener>
        <listener-class>org.apache.shiro.web.env.EnvironmentLoaderListener</listener-class>
    </listener>
    <context-param>
        <param-name>shiroEnvironmentClass</param-name>
        <param-value>org.apache.shiro.web.env.IniWebEnvironment</param-value><!-- 默认先从/WEB-INF/shiro.ini，如果没有找classpath:shiro.ini -->
    </context-param>
```

## 4. ServletContext

ServletContext是Servlet容器上下文环境对象，定义一组方法，servlet 使用这些方法与其 servlet 容器进行通信，例如，获取文件的 MIME 类型、分发请求或写入日志文件。每个web应用都有且仅有一个ServletContext对象，这个对象在所有的Servlet都可以使用。

## servlet 3.x 特性



### servlet 3.0支持异步，读写body仍然是bio

AsyncContext asyncContext = request.startAsync()

调用该方法后，servlet 容器在从 servlet.service()方法返回过后不再认为response报文 已经准备就绪，认为 response还在其他线程计算生成中，在 AsyncContext.complete()被调用时 触发向客户端tcp通道返回response 报文。另外asyncContext支持添加listener，可针对一些异常情况做处理。需要设置超时时间，某个时间点 计时器开始工作，当发生超时时，会通知listener，若listener.onTimeout方法指定了响应报文生成方式，则按该方式处理，否则。。。

https://stackoverflow.com/questions/15447666/javaee-6-asynccontext-behavior-after-timeout

servlet 在 读 request body 、写 response时，仍然使用的BIO，对请求处理的过程不完全多路复用模式，仍然会有线程阻塞，做不到完全事件驱动，无线程阻塞？ 这是dubbo等服务框架 选用 netty作为网络通信组件的原因之一吗？（之前有看到说 jdk 原生nio 不太好用，存在bug?）

### servlet 3.1 读写body支持nio
