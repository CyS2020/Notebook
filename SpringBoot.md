### SpringBoot简介

#### 文档网址
- `https://www.yuque.com/atguigu/springboot/qb7hy2`

#### 所处时代背景
- 微服务
- 分布式
- 云原生：简单地把云原生理解为，云原生 = 微服务 + DevOps + 持续交付(CI&CD) + 容器化

#### SpringBoot特性
- 依赖管理
  - 父项目依赖管理 `<dependencyManagement/>`
  - 自动版本仲裁，就近优先原则 `<properties/>`
  - starter 场景启动器 `spring-boot-starter-*`
    - 某场景所需要的一组依赖，提供一站式的服务
    - 第三方提供的场景启动器 `*-spring-boot-starter`
    - 所有场景最底层的依赖 `spring-boot-starter`
- 自动配置
  - 自动配置好了 tomcat；SpringMVC 全套组件；Web 常见功能；。。。
  - 默认包结构，主程序所在的包及其所有子包内的组件都会被默认扫描
  - 各种配置均有默认值，最终都会绑定到某个类上并托管到容器中
  - 按需加载所有自动配置项，自动配置功能包`spring-boot-test-autoconfigure`

#### 容器功能
- @Configuration
  - 配置类里面使用 @Bean 标注在方法上给容器注册组件，默认是单例
  - 配置类本身也是组件，这种 @Configuration + @Bean 方式多用于注入第三方类、不是组件的类
  - proxyBeanMethods 参数设置代理 bean 的方法
    - Full(proxyBeanMethods = true)；保证每个 @Bean 方法被调用多少次返回的组件都是单实例的
    - Lite(proxyBeanMethods = false)：每个 @Bean 方法被调用多少次返回的组件都是新创建的是多实例的
    - 配置类组件之间无依赖关系用Lite模式加速容器启动过程，减少判断
    - 配置类组件之间有依赖关系，方法会被调用得到之前单实例组件，用Full模式
- @Bean、@Component、@Controller、@Service、@Repository
- @ComponentScan、@Import
  - @Import 给容器中自动创建出这两个类型的组件、默认组件的名字就是全类名
- @Conditional
  - 满足Conditional指定的条件，则进行组件注入
- @ImportResource
  - 原生配置文件引入, 即老项目的beans.xml文件

#### 配置绑定
- @ConfigurationProperties(prefix = "")
  - 指定prefix将类字段与配置文件中的前缀的字段一一绑定
- @EnableConfigurationProperties(xxx.class) + @ConfigurationProperties
  - 在配置类中开启某属性配置功能参数是 xxx.class, 并把 xxx.class 实例注入容器
  - @EnableConfigurationProperties 注解的作用是让使用 @ConfigurationProperties 注解的类生效: 开启某个属性的配置绑定功能并注册到容器
- @Component + @ConfigurationProperties
  - 只有容器中的组件，才拥有 springboot 提供的强大功能，因此需要添加 @Component

#### 自动配置原理
- @SpringBootApplication
  - @SpringBootConfiguration
    - 内部是 @Configuration 代表当前是一个配置类，类 Application 也是 SpringBoot 的一个配置类
  - @EnableAutoConfiguration
    - @AutoConfigurationPackage -> @Import(AutoConfigurationPackages.Registrar.class)
      - 利用 Registrar 给容器中导入一系列组件，将指定的一个包下(Application)的所有组件导入
      - 就是将我们编写的那些配置类进行导入，即那些使用了@Configuration注解的类，注册到容器
    - @Import(AutoConfigurationImportSelector.class)
      - 利用 getAutoConfigurationEntry(annotationMetadata) 给容器中批量导入一些组件
      - 调用 List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes) 获取到所有需要导入到容器中的配置类
      - 利用工厂加载 Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) 得到所有的组件
      - 从 META-INF/spring.factories 位置来加载一个文件。默认扫描我们当前系统里面所有(依赖) META-INF/spring.factories 位置的文件
      - spring-boot-autoconfigure-2.6.2.jar 包里面也有 META-INF/spring.factories
  - @ComponentScan
    - 指定扫描的包，扫描路径，默认值扫描Application的同级和下级路径，scan可以添加上级路径

#### 按需开启自动配置项
- 虽然我们134个场景的所有自动配置启动的时候默认全部加载。xxxAutoConfiguration
- 按照条件装配规则 (@Conditional)，最终会按需配置。加载是要的，实例化并注入到容器则按需处理
- SpringBoot 默认会在底层配置好所有的组件(@ConditionalOnMissingBean)，默认都会绑定配置文件指定的值，但是如果用户配置了就以用户的配置优先
- xxxAutoConfiguration里面的值都会从 xxxProperties.class 类中取，xxxProperties.class 又和配置文件进行了绑定
- xxxAutoConfiguration ---> 组件  ---> xxxProperties.class里面拿值  ----> application.properties
- 定制化配置最佳实战:
  - 用户自己配置的话就在配置类里面使用 @Bean 自己创建该类并设置参数然后注册到容器
  - 用户去看这个组件是获取的配置文件什么值就去配置文件中(application.properties)修改

#### 最佳实践
- 引入场景依赖
- 查看自动配置了那些(选做)
  - 自己分析，引入场景对应的自动配置一般都生效了
  - 配置文件中debug=true开启自动配置报告。Negative(不生效)/Positive(生效)
- 是否需要修改
  - 参照文档修改配置项
  - 自己分析，xxxProperties.class 绑定了配置文件的哪些前缀
  - 自定义加入或者替换组件，@Bean, @Component...
  - 自定义器 xxxCustomizer

#### 开发工具
- lombok
- devtools
- processor
- Spring Initializr

#### 配置提示
```
# 开发使用
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>

# 打包忽略
<configuration>
    <excludes>
        <exclude>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
        </exclude>
    </excludes>
</configuration>
```

#### SpringMVC静态资源(了解)
- 只要静态资源放在类路径下：`/static` or `/public` or `/resources` or `/META-INF/resources`
- 访问方式：当前项目根路径 `/ + 静态资源名`
- 静态映射原理：请求进来，先去找 Controller 看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应 404 页面
- 配置访问前缀 `/res/**`: `spring.mvc.static-path-pattern=/res/**`
- 配置资源文件 `/res/**`: `spring.web.resources.static-locations=[classpath:/res]`
- 欢迎页 `index.html` 和 浏览器显示图标 `favicon.ico` 放到静态文件夹 `/static` 下就可以了

#### SpringMVC配置原理(了解)
- SpringMVC功能的自动配置类 WebMvcAutoConfiguration，自动生效
- 配置类只有一个有参构造器，有参构造器所有参数的值都会从容器中确定

#### REST风格
- @xxxMapping
- Rest风格支持（使用HTTP请求方式动词来表示对资源的操作）
  - 以前：`/getUser` 获取用户；`/deleteUser` 删除用户；`/editUser` 修改用户；`/saveUser` 保存用户
  - 现在： `/user` GET-获取用户；DELETE-删除用户；PUT-修改用户；POST-保存用户
```
  @RequestMapping(value = "/user", method = RequestMethod.GET) = @GetMapping("/user")
  @RequestMapping(value = "/user", method = RequestMethod.POST) = @PostMapping("/user")
  @RequestMapping(value = "/user", method = RequestMethod.PUT) = @PutMapping("/user")
  @RequestMapping(value = "/user", method = RequestMethod.DELETE) = @DeleteMapping("/user")
```

#### 请求映射原理
- SpringMVC功能分析都从 org.springframework.web.servlet.DispatcherServlet.doDispatch() 方法开始
- RequestMappingHandlerMapping：保存了所有 @RequestMapping 和 HandlerMethod(Controller) 的映射规则
- 所有的请求映射都在HandlerMapping中。我们需要一些自定义的映射处理，我们也可以自己给容器中放入自定义HandlerMapping
- HandlerMapping中找到能处理请求的Handler(Controller.method())为当前Handler 找一个适配器 HandlerAdapter
- RequestMappingHandlerAdapter：支持方法上标注@RequestMapping的Handler，我们也可以自己给容器中放入自定义HandlerAdapter
- 执行Handler方法前需要确定将要执行的目标方法的每一个参数的值是什么，参数解析器-HandlerMethodArgumentResolver
- SpringMVC目标方法能写多少种参数类型。取决于参数解析器。默认有26个（不同版本略有区别）
- Handler方法的返回值能多少种类型取决于，返回值处理器-HandlerMethodReturnValueHandler。默认15个
- Handler方法参数也可以传入Servlet API来作为参数，HttpServletRequest参数使用了ServletRequestMethodArgumentResolver来解析

#### Controller接收请求
- 注解方式
  - @RequestParam（获取请求参数）
  - @PathVariable（路径变量）
  - @RequestBody（获取请求体【POST】）
  - @RequestHeader（获取请求头）
  - @CookieValue（获取cookie值）
  - @MatrixVariable（矩阵变量）
  - @RequestAttribute（获取request域属性）
  - @ModelAttribute（获取Model中的属性）
  - URL中使用`{}`占位符使用@PathVariable；`?, &`组成的K-V使用@RequestParam；`;`后组成的K-V使用@MatrixVariable
- Servlet API
  - WebRequest、ServletRequest、MultipartRequest、 HttpSession、javax.servlet.http.PushBuilder、Principal
  - InputStream、Reader、HttpMethod、Locale、TimeZone、ZoneId
- 复杂参数
  - Map、Model（map、model里面的数据会被放在request的请求域  request.setAttribute() ）
  - 目标方法执行完成将所有的数据都放在 ModelAndViewContainer；包含要去的页面地址View。还包含Model数据。
  - Errors/BindingResult、RedirectAttributes（ 重定向携带数据）
  - ServletResponse（response）、SessionStatus、UriComponentsBuilder、ServletUriComponentsBuilder
- 自定义对象参数
  - 可以自动类型转换与格式化，可以级联封装。
  - WebDataBinder 利用它里面的 Converters 将请求数据转成指定的数据类型。再次封装到JavaBean中
- 原生请求
  - HttpServletRequest
  - HttpServletResponse