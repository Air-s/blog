---
layout: post
title: Springboot
tags: [基础]
excerpt_separator: <!--more-->
---

<!--more-->

- [<https://start.spring.io/>](<https://start.spring.io/>)：技术整合与快速搭建

- starter：将常见依赖分组打包集合，例如 web-starter 集合了常见 web 相关依赖（对应一组依赖）（省略了大量`<Dependency>` 标签，且不用考虑版本兼容问题）

  > 按住 Ctrl 键，点击 spring-boot-starter-web 等 artifactId、groupId 中的内容，可定位其 pom 文件，官方文档 [见此](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter)

- 自动配置：省略了大量 `<bean>` 标签对相关类的配置

- 省略了大量的 `xml` 配置文件

- 对于 web 项目，无需生成 war 放入 tomcat 等服务器中运行，内嵌 tomcat 等服务器，无需部署直接运行

- 通过 start.spring，下载好一个工程，然后通过 idea 导入（选择项目文件夹即可），稍等一会（下载各类插件），即可运行

- 将 project 视图从 project 转换为 project files（左上角选择）好一些

- SpringBoot parent 和 dependencyManagement：如果不想直接继承 spring-boot-starter-parentspring-boot-starter-parent 可以采用 dependencyManagement 方式管理，但插件需要自己引入

  > 详解见 [这篇文章](<https://blog.csdn.net/vtopqx/article/details/79034835>) 和 [这篇文章](<https://www.cnblogs.com/youzhibing/p/5427130.html>)，注意的是 dependencyManagement 只是声明并没有引用

- 在 pom.xml 中右键-maven-show Dependencies，使用依赖图查看依赖关系，可以排除依赖冲突

  > 可安装 Maven Helper 插件

- Ps：project structure 建议选 java1.8 而不是选择 idea 的包

  > 否则很容易出现 SLF4J: Class path contains multiple SLF4J bindings 的错误

- [maven常用插件总结](<https://www.jianshu.com/p/36348e4972be>)

  > 一些插件无需配置，springboot已经配置好

- 建立 submodule 时注意工程位置，不能与父工程重合

- 如果父工程是使用 dependencyManagement，子工程需要自行 Dependency，但无需版本号（version）。如果父工程使用 parent 继承 spring-boot-starter-parent，则子模块可直接使用父工程依赖。

- 启动类（带有@SpringBootApplication的类）不能直接放在 java 文件夹下，需要建一个包放入，否则会出现 Your ApplicationContext is unlikely to start due to a @ComponentScan of the default package 的警告，如果没有配置扫描范围，你的controller也要放在这个包里。

  ```
  src
   +- main
       +- java
           +- App(新建一个包)
               +- Application.java（启动类在这个新建包中）
               +- order(其他业务代码，最好另建包不要和启动类混合)
                   +- Order.java
                   +- OrderController.java
                   +- OrderService.java
                   +- OrderRepository.java
  ```

- controller映射URL不要用大写

- 给工程或子模块打包，使用IDEA的maven project工具即可无需输入命令，在指定模块下拉点击package即可，在包目录下使用 java -jar 包名.jar 即可启动包

- 默认扫描范围：启动类（@SpringBootApplication）所在的包及其子包

- 热部署插件（仅在debug使用，生产环境禁用）

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-devtools</artifactId>
      <optional>true</optional>
  </dependency>
  ```

  IDEA需要 Ctrl+F9 触发热部署，[这里](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools-restart) 有相应解释。

  > 原理是两个类加载器，不会变更的代码或依赖包加载至 *base* classloader 中，开发代码加载至 *restart* classloader 中。具体可看 Restart vs Reload 部分。

  > 可在 application.yml/application.properties 中通过 spring.devtools.restart.additional-exclude属性排除，具体可看 [这里](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools-restart-exclude)。关于路径表示可搜索**Ant模式通配符**相关内容，或者参考 [这篇文章](https://blog.csdn.net/zsg88/article/details/69663482) 或 [这篇文章](https://blog.csdn.net/klosero/article/details/80481847) 。Spring中默认排除的路径在 DevToolsProperties 类中定义。
  >
  > ```
  > "META-INF/maven/**,"
  >   + "META-INF/resources/**,resources/**,static/**,public/**,templates/**,"
  >   + "**/*Test.class,**/*Tests.class,git.properties,META-INF/build-info.properties"
  > ```

  > 还有一些资源（例如css/js等静态资源）发生变更时，通过 [LiveReload](https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-devtools-livereload) 机制刷新。大致过程是通知浏览器刷新已获得这些更新的资源。启动时日志中有一个 OptionalLiveReloadServer 就是 LiveReload 服务。

- 配置文件可放四个位置：

  - 1.父工程 config 文件夹 

  - 2.父工程根目录（pom.xml同级）

  - 3.resources-config 文件夹 

  -  4.resources 文件夹 

    > 优先级依次降低（如果相同覆盖），如果存在不同配置可形成互补，即所有位置配置文件都会被读取到。

- 自定义配置文件（名称与位置），有可能配置文件在外部（例如集群管理）需要从外部读取配置文件，具体设置可看 [这篇文章](https://my.oschina.net/u/2391658/blog/734743)

- 读取 yaml 值至实体类（VO），[官方文档见此](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-external-config-java-bean-binding)

  ```yaml
  yml:
    type:
      num: 123
      str: 字符串不需要加双引号
      specificStr: "转义字符\n需要在双引号内"
      doubleNum: 666.666
      birth: 2020/9/1 23:10:00
      nums: [1, 2, 3]
      nums:
        - 1
        - 2
        - 3
      map: {1: 11, 2: 22}
      map: 
        1: 11
        2: 22
  ```

  ```java
  @Component
  @ConfigurationProperties(prefix = "yml.type")//设置指定值前缀位置
  public class YamlModel {
      public int num;
      public String str;
      public String specificStr;
      public double doubleNum;
      public Date birth;
      public List<String> nums = new LinkedList<>();
      public Map<String, Integer> map = new HashMap<>();
      //省略setter getter 方法
      //可使用lombok生成方法，注意内部类同样需要打上@Data注解
  }
  ```
```
  
  ```java
  @Autowired
  private YamlModel yamlModel;
```

  如果是通过VO去编写yaml，可以引入以下依赖方便编写yaml（IDE会给出相应字段提示）

  ```xaml
  <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
  </dependency>
  ```

  

  

