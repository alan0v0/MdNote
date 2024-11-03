> 参考资料

[极客学院 跟我学shiro](http://wiki.jikexueyuan.com/project/shiro/)

## shiro 基本用法
### 简介
Java安全框架，相比Spring Security的功能强大，Shiro小而简单，基本满足大部分项目需求。
Shiro 支持JavaSE、JavaEE，也就是说Shiro的功能实现不依赖运行环境的基础设施。Shiro可实现身份认证、授权、会话管理、与Web集成、缓存等功能。

![](http://wiki.jikexueyuan.com/project/shiro/images/1.png)

**Authentication**：身份认证 / 登录，验证用户是不是拥有相应的身份；

**Authorization**：授权，即权限验证，验证某个已认证的用户是否拥有某个权限；即判断用户是否能做事情，常见的如：验证某个用户是否拥有某个角色。或者细粒度的验证某个用户对某个资源是否具有某个权限；

**Session Manager**：会话管理，即用户登录后就是一次会话，在没有退出之前，它的所有信息都在会话中；会话可以是普通 JavaSE 环境的，也可以是如 Web 环境的；

**Cryptography**：加密，保护数据的安全性，如密码加密存储到数据库，而不是明文存储；

**Web Support**：Web 支持，可以非常容易的集成到 Web 环境；

**Caching**：缓存，比如用户登录后，其用户信息、拥有的角色 / 权限不必每次去查，这样可以提高效率；

**Concurrency**：shiro 支持多线程应用的并发验证，即如在一个线程中开启另一个线程，能把权限自动传播过去；

**Testing**：提供测试支持；

**Run As**：允许一个用户假装为另一个用户（如果他们允许）的身份进行访问；

**Remember Me**：记住我，这个是非常常见的功能，即一次登录后，下次再来的话不用登录了。

### 基本概念

![](http://wiki.jikexueyuan.com/project/shiro/images/2.png)

可以看到：应用代码直接交互的对象是 Subject，也就是说 Shiro 的对外 API 核心就是 Subject；其每个 API 的含义：

**Subject**：主体，代表了当前 “用户”，这个用户不一定是一个具体的人，与当前应用交互的任何东西都是 Subject，如网络爬虫，机器人等；即一个抽象概念；所有 Subject 都绑定到 SecurityManager，与 Subject 的所有交互都会委托给 SecurityManager；可以把 Subject 认为是一个门面；SecurityManager 才是实际的执行者；

**SecurityManager**：安全管理器；即所有与安全有关的操作都会与 SecurityManager 交互；且它管理着所有 Subject；可以看出它是 Shiro 的核心，它负责与后边介绍的其他组件进行交互，如果学习过 SpringMVC，你可以把它看成 DispatcherServlet 前端控制器；

**Realm**：域，Shiro 从从 Realm 获取安全数据（如用户、角色、权限），就是说 SecurityManager 要验证用户身份，那么它需要从 Realm 获取相应的用户进行比较以确定用户身份是否合法；也需要从 Realm 得到用户相应的角色 / 权限进行验证用户是否能进行操作；可以把 Realm 看成 DataSource，即安全数据源。

也就是说对于我们而言，最简单的一个 Shiro 应用：

1. 应用代码通过 Subject 来进行认证和授权，而 Subject 又委托给 SecurityManager；
2. 我们需要给 Shiro 的 SecurityManager 注入 Realm，从而让 SecurityManager 能得到合法的用户及其权限进行判断。

**从以上也可以看出，Shiro 不提供维护用户 / 权限，而是通过 Realm 让开发人员自己注入。**

接下来我们来从 Shiro 内部来看下 Shiro 的架构，如下图所示：

![](http://wiki.jikexueyuan.com/project/shiro/images/3.png)

**Subject**：主体，可以看到主体可以是任何可以与应用交互的 “用户”；

**SecurityManager**：相当于 SpringMVC 中的 DispatcherServlet 或者 Struts2 中的 FilterDispatcher；是 Shiro 的心脏；所有具体的交互都通过 SecurityManager 进行控制；它管理着所有 Subject、且负责进行认证和授权、及会话、缓存的管理。

**Authenticator**：认证器，负责主体认证的，这是一个扩展点，如果用户觉得 Shiro 默认的不好，可以自定义实现；其需要认证策略（Authentication Strategy），即什么情况下算用户认证通过了；

**Authrizer**：授权器，或者访问控制器，用来决定主体是否有权限进行相应的操作；即控制着用户能访问应用中的哪些功能；

**Realm**：可以有 1 个或多个 Realm，可以认为是安全实体数据源，即用于获取安全实体的；可以是 JDBC 实现，也可以是 LDAP 实现，或者内存实现等等；由用户提供；注意：Shiro 不知道你的用户 / 权限存储在哪及以何种格式存储；所以我们一般在应用中都需要实现自己的 Realm；

**SessionManager**：如果写过 Servlet 就应该知道 Session 的概念，Session 呢需要有人去管理它的生命周期，这个组件就是 SessionManager；而 Shiro 并不仅仅可以用在 Web 环境，也可以用在如普通的 JavaSE 环境、EJB 等环境；所有呢，Shiro 就抽象了一个自己的 Session 来管理主体与应用之间交互的数据；这样的话，比如我们在 Web 环境用，刚开始是一台 Web 服务器；接着又上了台 EJB 服务器；这时想把两台服务器的会话数据放到一个地方，这个时候就可以实现自己的分布式会话（如把数据放到 Memcached 服务器）；

**SessionDAO**：DAO 大家都用过，数据访问对象，用于会话的 CRUD，比如我们想把 Session 保存到数据库，那么可以实现自己的 SessionDAO，通过如 JDBC 写到数据库；比如想把 Session 放到 Memcached 中，可以实现自己的 Memcached SessionDAO；另外 SessionDAO 中可以使用 Cache 进行缓存，以提高性能；

**CacheManager**：缓存控制器，来管理如用户、角色、权限等的缓存的；因为这些数据基本上很少去改变，放到缓存中后可以提高访问的性能

**Cryptography**：密码模块，Shiro 提高了一些常见的加密组件用于如密码加密 / 解密的。



### 登陆/退出

1、首先准备一些用户身份 / 凭据（shiro.ini）

```ini
[users]
zhang=123
wang=123
```

此处使用 ini 配置文件，通过 [users] 指定了两个主体：zhang/123、wang/123。

2、测试用例（com.github.zhangkaitao.shiro.chapter2.LoginLogoutTest）

```java
@Test
public void testHelloworld() {
    //1、获取SecurityManager工厂，此处使用Ini配置文件初始化SecurityManager  Factory<org.apache.shiro.mgt.SecurityManager> factory =
            new IniSecurityManagerFactory("classpath:shiro.ini");
    //2、得到SecurityManager实例 并绑定给SecurityUtils   org.apache.shiro.mgt.SecurityManager securityManager = factory.getInstance();
    SecurityUtils.setSecurityManager(securityManager);
    //3、得到Subject及创建用户名/密码身份验证Token（即用户身份/凭证）
    Subject subject = SecurityUtils.getSubject();
    UsernamePasswordToken token = new UsernamePasswordToken("zhang", "123");
    try {
        //4、登录，即身份验证
        subject.login(token);
    } catch (AuthenticationException e) {
        //5、身份验证失败
    }
    Assert.assertEquals(true, subject.isAuthenticated()); //断言用户已经登录
    //6、退出
    subject.logout();
}
```

- 首先通过 new IniSecurityManagerFactory 并指定一个 ini 配置文件来创建一个 SecurityManager 工厂；
- 接着获取 SecurityManager 并绑定到 SecurityUtils，这是一个全局设置，设置一次即可；
- 通过 SecurityUtils 得到 Subject，其会自动绑定到当前线程；如果在 web 环境在请求结束时需要解除绑定；然后获取身份验证的 Token，如用户名 / 密码；
- 调用 subject.login 方法进行登录，其会自动委托给 SecurityManager.login 方法进行登录；
- 如果身份验证失败请捕获 AuthenticationException 或其子类，常见的如： DisabledAccountException（禁用的帐号）、LockedAccountException（锁定的帐号）、UnknownAccountException（错误的帐号）、ExcessiveAttemptsException（登录失败次数过多）、IncorrectCredentialsException （错误的凭证）、ExpiredCredentialsException（过期的凭证）等，具体请查看其继承关系；对于页面的错误消息展示，最好使用如 “用户名 / 密码错误” 而不是 “用户名错误”/“密码错误”，防止一些恶意用户非法扫描帐号库；
- 最后可以调用 subject.logout 退出，其会自动委托给 SecurityManager.logout 方法退出。

**从如上代码可总结出身份验证的步骤**：

1. 收集用户身份 / 凭证，即如用户名 / 密码；

2. 调用 Subject.login 进行登录，如果失败将得到相应的 AuthenticationException 异常，根据异常提示用户错误信息；否则登录成功；

3. 最后调用 Subject.logout 进行退出操作。

   

## ShiroFilter

### 与Spring集成

1. Shiro使用Sevlet规范中的Filter接口进行安全控制，所以基于Servlet规范开发的Web项目都可以使用Shiro（不用管Servlet内部如何实现），下面介绍如何与Spring集成，即我们在Spring IOC 中创建一个ShiroFilter实例，如何使这个实例能够被servlet容器调用。基于Spring 提供的机制：

   

   ```xml
   <!--应用上下文context-->
   <context-param>
   	<param-name>contextConfigLocation</param-name>
   	<param-value>classpath:/applicationContext.xml</param-value>
   </context-param>
   
   <!--应用listener-->
   <listener>
   	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
   </listener>
   <!--DelegatingFilterProxy 作用是自动到 spring 容器查找名字为 shiroFilter（filter-name）的 bean 并把所有 Filter 的操作委托给它。-->
   <filter>
       <filter-name>shiroFilter</filter-name>
       <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
       <init-param>
           <param-name>targetFilterLifecycle</param-name>
           <param-value>true</param-value>
       </init-param>
   </filter>
   <!--设置filter url匹配范围-->
   <filter-mapping>
       <filter-name>shiroFilter</filter-name>
       <url-pattern>/*</url-pattern>
   </filter-mapping>
   ```

2. 在Spring配置文件中声明id为shiroFilter的**ShiroFilterFactoryBean**实例

   ```xml
       <!-- Shiro的Web过滤器 -->
       <bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
           <property name="securityManager" ref="securityManager"/>
           <property name="loginUrl" value="/login.jsp"/>
           <property name="unauthorizedUrl" value="/unauthorized.jsp"/>
           <property name="filters">
               <util:map>
                   <entry key="authc" value-ref="formAuthenticationFilter"/>
               </util:map>
           </property>
           <property name="filterChainDefinitions">
               <value>
                   /index.jsp = anon
                   /unauthorized.jsp = anon
                   /login.jsp = authc
                   /logout = logout
                   /** = user
               </value>
           </property>
       </bean>
   ```

shiroFilter正常工作需要依赖securityManager对象，securityManager提供认证、授权功能。loginUrl属性值指定登陆页面url，unauthorizedUrl属性值指定未授权页面url。filters属性、filterChainDefinitions属性 作用是？ 

### 内部拦截器

shiroFilter支持内部过滤链，通过设置filterChainDefinitions属性值可以为url指定拦截器（如上图xml文件配置），这里有一个枚举类，从中可以知道可用拦截器及其对应的字符串，：

```java
public enum DefaultFilter {
    anon(AnonymousFilter.class),
    authc(FormAuthenticationFilter.class),
    authcBasic(BasicHttpAuthenticationFilter.class),
    logout(LogoutFilter.class),
    noSessionCreation(NoSessionCreationFilter.class),
    perms(PermissionsAuthorizationFilter.class),
    port(PortFilter.class),
    rest(HttpMethodPermissionFilter.class),
    roles(RolesAuthorizationFilter.class),
    ssl(SslFilter.class),
    user(UserFilter.class);
};
```

FormAuthenticationFilter可以帮助处理登陆表单请求，如果验证失败，则在HttpServletRequst中添加属性` shiroLoginFailure`，告知错误类型，该post请求被放行到处理登陆表单请求的servlet对象中。也就是说FormAuthenticationFilter接管了登陆请求，如果身份验证通过，则不会把请求转发给处理登录请求的servlet，可以选择转发请求到验证前请求的页面，或者固定的页面，（其属性successfulUrl指定？）为了FormAuthenticationFilter可以正常获取到表单请求中的用户名和密码数据，需要对其进行配置，例如：

```xml
    <!-- 基于Form表单的身份验证过滤器 -->
    <bean id="formAuthenticationFilter"
        class="org.apache.shiro.web.filter.authc.FormAuthenticationFilter">
        <property name="usernameParam" value="username" />
        <property name="passwordParam" value="password" />
        <property name="loginUrl" value="/login" />
        <property name="successUrl" value="/news/newsList"></property>
    </bean>
```

该Filter处理请求流程图：

![](https://img-blog.csdn.net/20150301232241892)

## web SecurityManager 配置

### 会话管理

### 缓存



## Shiro 权限注解

### 常见注解

```
@RequiresAuthentication
```

表示当前 Subject 已经通过 login 进行了身份验证；即 Subject.isAuthenticated() 返回 true。

```
@RequiresUser
```

表示当前 Subject 已经身份验证或者通过记住我登录的。

```
@RequiresGuest
```

表示当前 Subject 没有身份验证或通过记住我登录过，即是游客身份。

```
@RequiresRoles(value={“admin”, “user”}, logical= Logical.AND)
```

表示当前 Subject 需要角色 admin 和 user。

```
@RequiresPermissions (value={“user:a”, “user:b”}, logical= Logical.OR)
```

表示当前 Subject 需要权限 user：a 或 user：b。

### springMVC集成Shiro 注解

Shiro 提供了相应的注解用于权限控制，如果使用这些注解就需要使用 AOP 的功能来进行判断，如 Spring AOP；Shiro 提供了 Spring AOP 集成用于权限注解的解析和验证。

在 spring-mvc.xml 配置文件添加 Shiro Spring AOP 权限注解的支持：

```
<aop:config proxy-target-class="true"></aop:config>
<bean class="
org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor">
    <property name="securityManager" ref="securityManager"/>
</bean>&nbsp;
```

如上配置用于开启 Shiro Spring AOP 权限注解的支持；`<aop:config proxy-target-class="true">` 表示代理类。

接着就可以在相应的控制器（AnnotationController）中使用如下方式进行注解：

```java
@RequiresRoles("admin")
@RequestMapping("/hello2")
public String hello2() {
    return "success";
};
```

访问 hello2 方法的前提是当前用户有 admin 角色。

当验证失败，其会抛出 UnauthorizedException 异常，此时可以使用 Spring 的 ExceptionHandler（DefaultExceptionHandler）来进行拦截处理：

```java
@ExceptionHandler({UnauthorizedException.class})
@ResponseStatus(HttpStatus.UNAUTHORIZED)
public ModelAndView processUnauthenticatedException(NativeWebRequest request, UnauthorizedException e) {
    ModelAndView mv = new ModelAndView();
    mv.addObject("exception", e);
    mv.setViewName("unauthorized");
    return mv;
};
```