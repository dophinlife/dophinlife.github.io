---
title: "Spring Boot 2.0 深度实践之核心技术篇"
layout: post
category: daily-note
tags: [springboot]
excerpt: "《Spring Boot 2.0 深度实践之核心技术篇》视频课程笔记，跟随课程的联系项目 [在此]() "
---
# 概览

## Spring Boot 特性

- **组件自动装配：** 规约大于配置，专注于核心业务
- **嵌入式容器：** 内置容器、无需部署、独立运行
- **Production-Ready：** 一站式运维， [生态](https://spring.io/blog/2018/03/16/micrometer-spring-boot-2-s-new-application-metrics-collector)无缝整合
- 外部化配置：一次构建、按需调配，到处运行
- Spring Boot Starter：简化依赖、按需装配、自我包含

### 易学难精

- **组件自动装配：** 模式注解、@Enable 模块、条件装配、加载机制
- **外部化配置：** Environment 抽象、生命周期、破坏性变更
- **嵌入式容器：** Servlet Web 容器、Reactive Web 容器
- **Spring Boot Starter：** 依赖管理、装配条件、装配顺序
- **Production-Ready：** 健康检查、数据指标、@Endpoint 管控

### 核心特性

#### 组件自动装配

- 激活：@EnableAutoConfiguration
- 配置：/META-INF/spring.factories
- 实现：XXXAutoConfiguration

#### 嵌入式 Web 容器

- Web Servlet: Tomcat、Jetty 和 Undertow
- Web Reactive: Netty Web Server

##### 传统 Servlet 应用

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

##### Spring Web MVC 应用

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

##### Spring Web Flux 应用

- Reactor 基础：Java Lambda、Mono、Flux
- Web Flux 核心：Web MVC 注解、函数式声明、异步非阻塞
- 使用场景：Web Flux 优势和限制

##### Web Server 应用

- 切换 Web Server
- 自定义 Servlet Web Server
- 自定义 Reactive Web Server

#### Production-Ready

- 指标：/actuator/metrics
- 健康检查：/actuator/health
- 外部化配置：/actuator/configprops

## Spring Boot 与 Java EE 规范

- Web : Servlet
- SQL : JDBC
- 数据校验 : Bean Validation
- 缓存 : Java Caching API
- WebSockets : Java API for WebSocket
- Web Services : JAX-WS
- Java 管理 : JMX
- 消息 : JMS

## 数据介绍

- JDBC：数据源、JdbcTemplate、自动装配
- JPA：实体映射关系、实体操作、自动装配
- 事务：Spring 事务抽象、JDBC 事务处理、自动装配

## Spring Boot 应用

- SpringApplication: 失败分析、应用特性、事件监听等
- Spring Boot 配置: 外部化配置、Profile、配置属性
- Spring Boot Starter: Starter 开发、最佳实践

# 走向自动装配

## Spring Framework 手动装配

### Spring 模式注解装配

**定义**: 一种声明在应用中扮演"组件"角色的注解。

**举例**：@Component、@Service、@Configuration等。

**装配**：<context:component-scan> 或者 @ComponentScan。

### Spring @Enable 模块装配

**定义**：具备相同领域功能的组件集合，组合所形成一个独立的单元。

**举例**：@EnableWebMvc、@EnableAutoConfiguration 等。

**实现**：注解方式、编程方式。

### Spring 条件装配

**定义**：Bean 装配的前置条件。

**举例**：@Profile、@Conditional。

**实现**：注解方式、编程方式。

- 注解方式：@Profile
- 编程方式：

    1 实现 Condition 接口
    ```java
  /**
   * 系统属性条件判断
   *
   * @author guangp
   * @since 2020/6/30
   */
  public class OnSystemPropertyCondition implements Condition {
      /**
       * 判断
       * @param context 条件上下文
       * @param metadata 注解、方法的元数据信息
       * @return
       */
      @Override
      public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
          Map<String, Object> attributes =
                  metadata.getAnnotationAttributes(ConditionalOnSystemProperty.class.getName());
          String propertyName = (String) attributes.get("name");
          String propertyValue = (String) attributes.get("value");
          String systemPropertyValue = System.getProperty(propertyName);
          return propertyValue.equals(systemPropertyValue);
      }
  }
    ```
  2 创建条件注解
  ```java
  /**
   * Java 系统属性 条件判断
   *
   * @author guangp
   * @since 2020/6/30
   */
  @Target({ElementType.TYPE, ElementType.METHOD})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Conditional(OnSystemPropertyCondition.class)
  public @interface ConditionalOnSystemProperty {
      /**
       * Java 系统属性名称
       * @return
       */
      String name();
  
      /**
       * Java 系统属性值
       * @return
       */
      String value();
  }
  ```
  3 应用注解
  ```
  @Bean
  @ConditionalOnSystemProperty(name = "user.name", value = "guangp")
  public String helloWorld() {  
      return "Hello, World. 2020";
  }
  ```

## Spring Boot 自动装配
