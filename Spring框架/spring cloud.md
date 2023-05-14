## 服务注册 发现 Eurake

### 注册中心

需要考虑高可用

### 服务提供者

1. 启动类添加注解`@EnableDiscoveryClient`

2. 修改application.yml， 添加配置

   ```yaml
   eureka:
     client:
       serviceUrl:
         defaultZone: http://localhost:8761/eureka/  #注册中心
   spring:
     application:
       name: weather-city-eureka
   server:
     port: 8083
   ```

### 服务调用者

1. 启动类添加注解`@EnableDiscoveryClient`

2. 修改application.yml， 添加配置

   ```
   spring:
     application:
       name: weather-collection-eureka-feign
   
   eureka:
     client:
       serviceUrl:
         defaultZone: http://localhost:8761/eureka/
   server:
     port: 8081
   
   feign:
     client:
       config:
         feignName:
           connectTimeout: 5000
           readTimeout: 5000
   ```

3. 编写远程服务接口类（方法签名需要保证一致？并使用@RequestMaping 指定对应URL？），添加注解`@FeignClient("服务名")`

## 熔断器 Hystrix

> 在服务调用方额外添加一个回调类，该类与远程服务实现了相同的接口，当调用远程服务故障后，转而使用该类，实现快速失败。==所以每个调用者都要实现自己的fallback 类？==
>
> 是的，每个服务调用者 通过feign客户端访问服务提供者，feign 接口及其fallback 一般放在公共模块中，不需要 反复重写

1. 修改application.yml， 开启feign 的Hystrix开关。

2. ==@EnableFeignClients==  @EnableCircuitBreaker

3. `@FeignClient(name="服务名",fallback = DataClientFallBack.class)`

## 服务网关 Zuul

1. ```java
   @EnableDiscoveryClient
   @EnableZuulProxy
   ```

2. ```java
   spring:
     application:
       name: msa-weather-eureka-client-zuul
   
   eureka:
     client:
       serviceUrl:
         defaultZone: http://localhost:8761/eureka/
   zuul:
     routes:
       city:
         path: /city/**
         serviceId: weather-city-eureka
       data:
         path: /data/**
         serviceId: weather-data-eureka
   server:
     port: 8085
   ```