# Mybatis-generator

1. Mapper 文件bug

generator多次运行生成的Mapper 文件包含**重复内容，必须去除**

2. Mapper 文件位置

生成的Mapper文件路径需要与Application.yml保持一致

3. @Mapper 

生成Mapper 类需要添加注解@Mapper，被识别创建 到IOC 容器

## 配置

![enter image description here](https://lh3.googleusercontent.com/L54s1IJeiIgqm3lq3mxrREV-7yXcniardJYu2p0bz5QXkZVPoTy2Q1LiW66wLiYkt1kYFILB1Q6g)

MyBatis应用程序以一个实现了SqlSessionFactory（接口）的实例为核心，通过builder设计模式生成实例，一般由SqlSessionFactoryBuilder实例通过xml配置文件或者Configuration类实例 构建SqlSessionFactory实例。

Mybatis 提供"mybatis-spring" 构件与spring 集成。
```
<dependency>  
 <groupId>org.mybatis</groupId>  
 <artifactId>mybatis-spring</artifactId>  
 <version>1.3.0</version>  
</dependency>
```
使用SqlSessionFactoryBean提供SqlSessionFactory实例，利用MapperScannerConfigurer转换器将映射接口转换为Spring 容器中 Bean。

```
<bean id="sqlSessionFactory"  
  class="org.mybatis.spring.SqlSessionFactoryBean"  
  p:dataSource-ref="dataSource"  
  p:configLocation="classpath:myBatisConfig.xml"  
  p:mapperLocations="classpath:mybatis/UserMapper.xml"/>
  
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer"  
  p:sqlSessionFactory-ref="sqlSessionFactory"  
  p:basePackage="alan.orm.mybatis"/>
```

## 映射器

mapper元素的 namespace属性值为映射接口的全限定名，保证此mapper的唯一。
### 传入参数 parameterType

* 可以传入基本数据类型、pojo、Map 实例
* 使用map<String,Object>传参代码可读性差，也无法限定value的类型
* 通过注解传递参数 @Param( 使用注解时可以不需要指定parameterType)
* 混合使用
```
User selectOne(@Param("userId") id)
select * from users where user_id=#{userId}
```


### 结果映射 resultType resultMap

1. 自动映射和驼峰映射
mybatis 默认开启自动映射，列可以通过**别名**与pojo属性绑定。
驼峰映射：需要手动开启，列和属性映射类似 role_name --roleName

2. resultType也可以将结果集转换成list

3. resultMap 可以实现级联、控制延迟加载、配置缓存等功能
* 级联包括discriminator association（一对一） collection(一对N)
* 延迟加载[https://blog.csdn.net/eson_15/article/details/51668523](https://blog.csdn.net/eson_15/article/details/51668523)

一级缓存 二级缓存