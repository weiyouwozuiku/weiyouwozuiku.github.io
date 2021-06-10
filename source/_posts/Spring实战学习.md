---
title: Spring实战学习
author: 不二
mathjax: true
date: 2020-08-13 23:58:08
tags: 
- Spring
- Java
img: https://cdn.jsdelivr.net/gh/weiyouwozuiku/weiyouwozuiku.github.io@src/source/_posts/PageImg/spring.png
categories: 程序设计
---

## 前言

本文记录学习《Spring实战》(第五版)中的知识点，并将一些开发项目中遇到的相关经验与理解记录在这里。

> 唯一不变的就是变化    ----希腊哲学家 赫拉克利特

## 第一部分 Spring基础

Spring的核心提供了一个叫做Spring上下文(Spring application context)的容器，其创建和管理应用组件(bean)。将bean组装在一起的行为是通过基于依赖注入(dependency injection,DI)的模式实现。组件不会创建它所依赖的组件并管理它们的生命周期，使用依赖注入的应用依赖于单独的实体（容器）来创建和维护所有的组件，并将其注入到它们所需的bean中。

以往，使用一个或多个xml文件指导Spring上下文将bean装配在一起。现在推荐使用基于java的配置。`@Configuration`注释告知Spring这是一个配置类，为Spring应用提供上下文。使用`@Bean`注释标注，表明方法返回的对象会以bean的形式添加到Spring的上下文。(默认这些bean对应的bean ID与定义它们的方法名称相同)。

基于java的配置相较于xml有更强的类型安全性和更好的重构能力。

**只有当Spring不能进行自动配置时，才需要显示配置。**

$自动配置\begin{cases}\text{自动装配：Spring能够自动为组件注入所依赖的其他bean} \\ \text{组件扫描:Spring自动发现应用路径下的组件，并将它们创建成Spring应用上下文的组件}\end{cases}$

Spring Initializr是一个能够生成Spring Boot项目的REST API。

Spring项目的目录结构：

- mvnw和mvnw.cmd：Maven包装器(wrapper)脚本。即使机器上没有安装Maven，也能构建项目。
- pom.xml：Maven构建规范。
- application.properties：最初该文件是空的，为我们提供制定配置属性的地方。
- resources/static：存放任意为浏览器提供服务的静态内容。
- resources/templates：存放用来渲染内容到浏览器的模板文件。

Spring Boot starter依赖它们本身不包含库代码，而是传递性的拉取其他的库。这样做的好处如下：

- 构建文件会显著减小并易于管理。不必为每个所需的依赖库都声明依赖。
- 能够根据它们所提供的功能来思考依赖，而不是库名称。
- 不必担心库版本问题。可以相信给定版本的Spring Boot，传递性引入的库的版本是兼容的。只要关心Spring Boot版本就行。

Spring Boot maven plugin：1.提供一个maven goal，允许我们使用maven来运行应用。2.确保依赖的所有库都包含在可执行的jar文件中，并保证它们在运行时类路径下可用。3.它会在jar中生成一个manifest文件，将引导类声明为可执行jar主类。

`@SpringBootApplication`是一个组合注释，如下：

1. `@SpringBootConfiguration`：将该类声明为配置类。实际上该注释是`@Configuration`的特殊形式。
2. `@EnableAutoConfiguration`：启用Spring Boot的自动配置。
3. `@ComponentScan`：启用组件扫描。

测试类带有`@RunWith(SpringRunner.class)`注解。其是Junit的注解，它会运行一个测试运行器(runner)来指导JUnit如何运行测试。本例子中为JUnit提供的是SpringRunner，这个是一个Spring提供的测试运行器，它会创建测试运行所需的Spring应用上下文。`@SpringBootTest`告诉JUit在启动测试时添加上Spring Boot功能

SpringRunner是SpringJUit4ClassRunner的别名，于Spring4.3引入，移除了对特定JUit版本的关联。

Spring自带一个强大的Web框架，Spring MVC。其核心是控制器的理念(处理请求并以某种方式进行信息响应的类)。

在Spring Boot2.2之后，默认的测试工具变成了Junit5。有两种方法进行测试:1.直接写`@SpringBootTest`，如果测试MVC加上注释`@AutoConfigureMockMvc`，注入一个`Autowired`的`MockMvc`对象。2.声明`@ExtendWith(SpringExtension.class)`替换`@RunWith(SpringRunner.class)`。设置`@WebMvcTest(所需测试的Controller.class)`。

```java
import static org.hamcrest.Matchers.containsString;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.view;
//@SpringBootTest
//@AutoConfigureMockMvc
@ExtendWith(SpringExtension.class)
@WebMvcTest(HomeController.class)
public class HomeControllerTest{
    @Autowired
    private MockMvc mockMvc;
    @Test
    public void testHomePage() throws Exception {
        mockMvc.perform(get("/"))    // <3>

                .andExpect(status().isOk())  // <4>

                .andExpect(view().name("home"))  // <5>

                .andExpect(content().string(           // <6>
                        containsString("Welcome to...")));
    }
}
```

`@RequestMapping`：注释到类级别的时候，它能够指定该控制器所处理的请求类型。

在方法上的`@GetMapping`注释对类级别的`@RequestMapping`细化。`GetMapping`是Spring4.3引入，之前的是`@RequestMapping(method=RequestMethod.GET)`。

| Spring MVC 的请求注解 | 描述                |
| --------------------- | ------------------- |
| @RequestMapping       | 通用的请求处理      |
| @GetMapping           | 处理HTTP GET请求    |
| @PostMapping          | 处理HTTP POST请求   |
| @PutMapping           | 处理HTTP PUT请求    |
| @DeleteMapping        | 处理HTTP DELETE请求 |
| @PatchMapping         | 处理HTTP PATCH请求  |

在为控制器方法声明请求映射时，越具体越好。意味着至少要声明路径(或者从类级别的QRequestMapping继承一个路径)以及它所处理的HTTP方法。**推荐在类级别上使用@RequestMapping以便于指定基本的路径。在每个处理器方法上，使用更具体的@GetMapping或@PostMapping注解。**

DevTools是Spring开发人员的开发期工具，包括：

- 代码变更后应用自动重启。
- 当面向浏览器的资源（如模板、Js、样式表）等发生变化时，会自动刷新浏览器。
- 自动禁用模板缓存。
- 如果使用了H2数据库，内置H2控制台。

当DevTools运行时，应用程序会被加载到Java虚拟机两个独立的类加载器中。其中一个类加载器会加载Jva代码、属性文件和项目中"src/main"路径下几乎所有的内容。另一个类加载器会加载依赖的库。当检测到变更时，DevTools只会重新加载包含项目代码的类加载器，并重启应用上下文。另一个类加载器和jvm保持不变，缩短应用启动时间。但**导致自动重启无法反映依赖项的变化。需手动重新启动应用**。DevTools在应用程序运行时，自启动一个LiveReload服务器。当LiveReload服务器与LiveReload浏览器插件结合时，有变化时自动刷新浏览器。

**推荐使用Idea中的插件JRBEL**

#### Lombok

>Lombok是一种JAVA的实用工具，可用来帮助开发人员消除JAVA的冗长，通过注解实现这一目的。

常见的Lombok注释：

- @Data：注解在类上。提供类所有属性的getter和setter方法，还提供了equals、canEqual、hashCode、toString方法。

- @Setter：注解在属性上；为属性提供setting方法

- @Getter：注解在属性上；为属性提供getting方法

- @SneakyThrows：无需在签名处显式抛出异常

- @Log4j：注释在类上；为类提供一个属性名为log的log4j日志对象

- @Slf4j：同上。其作用同如下代码

    ```java
    private static final org.slf4j.Logger log=org.slf4j.LoggerFactory.getLogger(DesignTacoController.class);
    ```

- @NoArgsConstructor：注释在类上；为类提供一个无参的构造方法

- @AllArgsConstructor：注释在类上；为类提供一个全参的构造方法

## Tip

### IDEA集成热部署

JRebel and XRebel for intellij（低版本叫JRebel for intellij，下方还有一个JRebel mybatisPlus extension是针对mybatis的mapper.xml的热部署）

### 修改Spring启动字符

每次spring项目启动时都会在终端打印`SPRING`。如果需要换成其他字符。在`src/main/resources/`路径下创建`banner.txt`即可。其中放入你想打印的字符。

