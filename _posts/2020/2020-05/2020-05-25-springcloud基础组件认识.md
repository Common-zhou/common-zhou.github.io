---
layout: post
title:  "springcloud组件"
categories: springcloud
tags: springcloud
excerpt: springcloud基础组件认识及使用
auth: zhou
---

* content
{:toc}


# 1.springcloud组件

## 1.1Eureka(注册中心)

将不同的组件注册在同一个位置，可以让其他的组件获取到

## 1.2Robbin(负载均衡)

同一个组件可能会有多个实体，在调用的时候，可以选择合适的负载均衡策略(轮询、随机)，来让请求分发到不同的实体上。

## 1.3Hystix(熔断器)

当一个服务不可用的时候，需要将其踢出服务队列，否则积压越来越多，这样容易拖垮系统。

## 1.4Feign(远程调用)

可以直接通过服务名调用。或者命名相同的接口。

## 1.5Zuul(过滤器)



# 2.springcloud使用

## 2.1Eureka使用

### 2.1.1配置注册中心服务(Eureka Server)

```
单独建一个springboot应用，导入web、springcloud的server包
```

```xml
<properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Hoxton.SR4</spring-cloud.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.2</version>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

```yml
启动类上加入注解 打开server
@EnableEurekaServer

yml配置文件配置选项
server:
  port: 10086
spring:
  application:
    name: eureka-server
eureka:
  client:
    service-url: # EurekaServer的地址，现在是自己的地址，如果是集群，需要加上其它Server的地址。
      defaultZone: http://127.0.0.1:${server.port}/eureka
```

之后启动，注册中心就配置好了。 之后配置提供方和消费方

可以通过localhost:10086 可以访问注册中心，查看注册进入的组件

### 2.1.2Porvider配置方式

```
导入依赖，依旧是web 还有Eureka client
```



```xml
<properties>
    <java.version>1.8</java.version>
</properties>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        <version>2.2.2.RELEASE</version>
    </dependency>
</dependencies>

<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Hoxton.SR4</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```



配置配置文件（配置注册中心位置，还有应用名称）

提供方(Provider)

```yml
server:
  port: 80
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10086/eureka
spring:
  application:
    name: service-provider
```

消费方(Consumer)

```yml
server:
  port: 80
eureka:
  client:
    service-url:
      defaultZone: http://127.0.0.1:10086/eureka
spring:
  application:
    name: service-consumer
```

消费方调用方法

```java
需要向spring中注册一个RestTemplate

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
    
    @Autowired
    private RestTemplate restTemplate;

    @Autowired
    DiscoveryClient discoveryClient;

    @GetMapping("/user")
    @ResponseBody
    public User queryUserById(@RequestParam("id") Long id) {
        //可以通过该方法获取所有的实例(该id)
        List<ServiceInstance> instances = discoveryClient.getInstances("service-provider");
        ServiceInstance serviceInstance = instances.get(0);

        User user = this.restTemplate.getForObject("http://" + serviceInstance.getHost() + ":" + serviceInstance.getPort() + "/user/" + id, User.class);
        return user;
    }
```

## 2.2Ribbon使用

```java
需要在restTemplate注解的地方增加一个@LoadBalanced注解
@GetMapping("/user/ribbon")
@ResponseBody
public User queryUserByIdRibbon(@RequestParam("id") Long id) {
    //直接写服务名就可以调用服务，并且有ribbon做负载均衡
    String baseUrl = "http://service-provider/user/" + id;

    User user = this.restTemplate.getForObject(baseUrl, User.class);
    return user;
}
```

## 2.3hystix使用