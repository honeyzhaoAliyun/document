# 创建缓存服务提供者
## 创建项目
创建一个名为 itoken-service-redis 的服务提供者项目

## POM
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.vvdd</groupId>
        <artifactId>itoken-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../itoken-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>itoken-service-redis</artifactId>
    <packaging>jar</packaging>

    <name>itoken-service-redis</name>
    <url>http://www.vvdd.com</url>
    <inceptionYear>2018-Now</inceptionYear>

    <dependencies>
        <!-- Project Begin -->
        <dependency>
            <groupId>com.vvdd</groupId>
            <artifactId>itoken-common</artifactId>
            <version>${project.parent.version}</version>
        </dependency>
        <!-- Project End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zipkin</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->

        <!-- Spring Boot Admin Begin -->
        <dependency>
            <groupId>org.jolokia</groupId>
            <artifactId>jolokia-core</artifactId>
        </dependency>
        <dependency>
            <groupId>de.codecentric</groupId>
            <artifactId>spring-boot-admin-starter-client</artifactId>
        </dependency>
        <!-- Spring Boot Admin End -->

        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.vvdd.itoken.service.redis.RedisApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```
主要增加了 org.springframework.boot:spring-boot-starter-data-redis，org.apache.commons:commons-pool2 两个依赖

## Application
```
package com.vvdd.itoken.service.redis;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@EnableEurekaClient
@SpringBootApplication
public class RedisApplication {
    public static void main(String[] args) {
        SpringApplication.run(RedisApplication.class, args);
    }
}
```
## 本地配置
### bootstrap.yml
```
spring:
  cloud:
    config:
      uri: http://localhost:8888
      name: itoken-service-redis
      label: master
      profile: dev
```
### bootstrap-prod.yml
```
spring:
  cloud:
    config:
      uri: http://192.168.75.137:8888
      name: itoken-service-redis
      label: master
      profile: prod
```
## 云配置
### itoken-service-redis-dev.yml
```
spring:
  application:
    name: itoken-service-redis
  boot:
    admin:
      client:
        url: http://localhost:8084
  zipkin:
    base-url: http://localhost:9411
  redis:
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        max-wait: -1ms
        min-idle: 0
    sentinel:
      master: mymaster
      nodes: 192.168.75.140:26379

server:
  port: 8502

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/

management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        include: health,info
```
### itoken-service-redis-prod.yml
```
spring:
  application:
    name: itoken-service-redis
  boot:
    admin:
      client:
        url: http://192.168.75.137:8084
  zipkin:
    base-url: http://192.168.75.137:9411
  redis:
    lettuce:
      pool:
        max-active: 8
        max-idle: 8
        max-wait: -1ms
        min-idle: 0
    sentinel:
      master: mymaster
      nodes: 192.168.75.140:26379, 192.168.75.140:26380, 192.168.75.140:26381

server:
  port: 8502

eureka:
  client:
    serviceUrl:
      defaultZone: http://192.168.75.137:8761/eureka/,http://192.168.75.137:8861/eureka/,http://192.168.75.137:8961/eureka/

management:
  endpoint:
    health:
      show-details: always
  endpoints:
    web:
      exposure:
        include: health,info
```
## 创建接口
### RedisService
```
package com.vvdd.itoken.service.redis.service;

public interface RedisService {
    public void set(String key, Object value, long seconds);
    public Object get(String key);
}
```
### RedisServiceImpl
```
package com.vvdd.itoken.service.redis.service.impl;

import com.vvdd.itoken.service.redis.service.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class RedisServiceImpl implements RedisService {

    @Autowired
    private RedisTemplate redisTemplate;

    @Override
    public void set(String key, Object value, long seconds) {
        redisTemplate.opsForValue().set(key, value, seconds, TimeUnit.SECONDS);
    }

    @Override
    public Object get(String key) {
        return redisTemplate.opsForValue().get(key);
    }
}
```
### Controller
```
package com.vvdd.itoken.service.redis.controller;

import com.vvdd.itoken.service.redis.service.RedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class RedisController {

    private static final String RESULT_OK = "ok";

    @Autowired
    private RedisService redisService;

    @RequestMapping(value = "put", method = RequestMethod.GET)
    public String set(String key, String value, long seconds) {
        redisService.set(key, value, seconds);
        return RESULT_OK;
    }

    @RequestMapping(value = "find", method = RequestMethod.GET)
    public String find(String key) {
        String json = null;

        Object obj = redisService.get(key);
        if (obj != null) {
            json = (String) redisService.get(key);
        }

        return json;
    }
}
```