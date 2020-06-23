---
title: "Spring Boot 2.0 深度实践之核心技术篇"
layout: post
category: daily-note
tags: [springboot]
excerpt: "《Spring Boot 2.0 深度实践之核心技术篇》视频课程笔记，跟随课程的联系项目 [在此]() "
---------------------------------------------------------

# Spring Boot 特性

- **组件自动装配：** 规约大于配置，专注于核心业务
- **嵌入式容器：** 内置容器、无需部署、独立运行
- **Production-Ready：** 一站式运维， [生态](https://spring.io/blog/2018/03/16/micrometer-spring-boot-2-s-new-application-metrics-collector)无缝整合
- 外部化配置：一次构建、按需调配，到处运行
- Spring Boot Starter：简化依赖、按需装配、自我包含

## 易学难精

- **组件自动装配：** 模式注解、@Enable 模块、条件装配、加载机制
- **外部化配置：** Environment 抽象、生命周期、破坏性变更
- **嵌入式容器：** Servlet Web 容器、Reactive Web 容器
- **Spring Boot Starter：** 依赖管理、装配条件、装配顺序
- **Production-Ready：** 健康检查、数据指标、@Endpoint 管控

## 核心特性

### 组件自动装配

- 激活：@EnableAutoConfiguration
- 配置：/META-INF/spring.factories
- 实现：XXXAutoConfiguration

### 嵌入式 Web 容器

- Web Servlet: Tomcat、Jetty 和 Undertow
- Web Reactive: Netty Web Server

#### 传统 Servlet 应用

- Servlet 组件：Servlet、Filter、Listener
  - Servlet
    - 实现
      - @WebServlet
      - extends HttpServlet
      - 注册
    - URL 映射
      - @WebServlet(urlPatterns="/my/servlet")
    - 注册
      - @ServletComponentScan(basePackages = "com.imooc.diveinspringboot.web.servlet")
- Servlet 注册：Servlet 注解（Servlet 3.0）、Spring Bean、RegistrationBean
- 异步非阻塞（Servlet 3.0+）：异步 Servlet（3.0）、非阻塞 Servlet（3.1）

#### Spring Web MVC 应用

- Web MVC 视图：模板引擎、内容协商、异常处理等
  - 视图
    - ViewResolver
    - View
  - 模板引擎
    - Thymeleaf
    - FreeMarker
    - JSP
  - 内容协商
    - ContentNegotiationConfigurer
    - ContentNegotiationStrategy
    - ContentNegotiationResolver
  - 异常处理
    - @ExceptionHandler
    - HandleExceptionResolver
      - ExceptionHandleExceptionResolver
    - BasicErrorController
- Web MVC Rest：资源服务、资源跨域、服务发现等
  - 资源服务
    - `@RequestMapping`
      - `@GetMapping`
    - `@ResponseBody`
    - `@RequestBody`
  - 资源跨域
    - `CrossOrigin`
    - `WebMvcConfigurer#addCorsMappings`
    - 传统解决方案
      - IFrame
      - JSONP
  - 服务发现
    - HATEOS
- Web MVC 核心：核心架构、处理流程、核心组件
  - 核心组件
    - `DispatcherServlet`
    - `HandlerMapping`
    - `HandlerAdapter`
    - `ViewResolver`
    - ...

### Production-Ready

- 指标：/actuator/metrics
- 健康检查：/actuator/health
- 外部化配置：/actuator/configprops

# Spring Boot 与 Java EE 规范

- Web : Servlet
- SQL : JDBC
- 数据校验 : Bean Validation
- 缓存 : Java Caching API
- WebSockets : Java API for WebSocket
- Web Services : JAX-WS
- Java 管理 : JMX
- 消息 : JMS