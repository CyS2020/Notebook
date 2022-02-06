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
      - 利用 getAutoConfigurationEntry(annotationMetadata) 给容器中批量导入一些组件；默认134个组件
      - 内部进入利用工厂加载 Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) 得到所有的组件
      - 从`META-INF/spring.factories`位置来加载一个文件。默认扫描我们当前系统里面所有(依赖)`META-INF/spring.factories`位置的文件
      - 文件里面写死了spring-boot一启动就要给容器中加载的所有配置类；将这些文件中所声明的配置类全部加载到容器中(只加载未实例化)
      - 例如spring-boot-autoconfigure-2.6.2.jar 包里面就有`META-INF/spring.factories`
  - @ComponentScan
    - 指定扫描的包，扫描路径，默认值扫描Application的同级和下级路径，scan可以添加上级路径

#### 按需开启自动配置项
- 虽然我们134个场景的所有自动配置启动的时候默认全部加载。xxxAutoConfiguration
- 按照条件装配规则 (@Conditional)，最终会按需配置。加载是要的，实例化并注入到容器则按需处理
- SpringBoot 默认会在底层配置好所有的组件(@ConditionalOnMissingBean)，默认都会绑定配置文件指定的值，但是如果用户配置了就以用户的配置优先
- xxxAutoConfiguration里面的值都会从 xxxProperties.class 类中取，xxxProperties.class 又和配置文件进行了绑定
- xxxAutoConfiguration ---> 导入组件 ---> xxxProperties.class里面拿值 ----> application.properties配置值
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
- Rest风格支持(使用HTTP请求方式动词来表示对资源的操作)
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
- HandlerMapping中找到能处理请求的Handler(Controller.method())为当前Handler找一个适配器HandlerAdapter
- RequestMappingHandlerAdapter：支持方法上标注@RequestMapping的Handler，我们也可以自己给容器中放入自定义HandlerAdapter
- 执行Handler方法前需要确定将要执行的目标方法的每一个参数的值是什么，参数解析器-HandlerMethodArgumentResolver
- SpringMVC目标方法能写多少种参数类型。取决于参数解析器。默认有26个(不同版本略有区别)
- Handler方法的返回值能多少种类型取决于，返回值处理器-HandlerMethodReturnValueHandler。默认15个
- Handler方法参数也可以传入Servlet API来作为参数，HttpServletRequest参数使用了ServletRequestMethodArgumentResolver来解析
- 参数解析器内部利用 MessageConverters(MappingJackson2HttpMessageConverter) 将json数据转为接收确定的参数类型
- 调用Handler(Controller.method()) 方法进行业务处理
- 判断返回值处理器是否支持这种类型返回值 supportsReturnType()；返回值处理器调用 handleReturnValue() 进行处理
- RequestResponseBodyMethodProcessor 可以处理返回值标了 @ResponseBody 注解的
- 返回值处理器内部利用 MessageConverters(MappingJackson2HttpMessageConverter) 进行处理将数据写为json

#### Controller接收请求
- 注解方式
  - @RequestParam(获取请求参数)
  - @PathVariable(路径变量)
  - @RequestBody(获取请求体【POST】)
  - @RequestHeader(获取请求头)
  - @CookieValue(获取cookie值)
  - @MatrixVariable(矩阵变量)
  - @RequestAttribute(获取request域属性)
  - @ModelAttribute(获取Model中的属性)
  - @RequestPart(获取请求文件，封装为MultipartFile)
  - URL中使用`{}`占位符使用@PathVariable；`?, &`组成的K-V使用@RequestParam；`;`后组成的K-V使用@MatrixVariable
- Servlet API
  - WebRequest、ServletRequest、MultipartRequest、 HttpSession、javax.servlet.http.PushBuilder、Principal
  - InputStream、Reader、HttpMethod、Locale、TimeZone、ZoneId
- 复杂参数
  - Map、Model(map、model里面的数据会被放在request的请求域  request.setAttribute())
  - 目标方法执行完成将所有的数据都放在 ModelAndViewContainer；包含要去的页面地址View。还包含Model数据。
  - Errors/BindingResult、RedirectAttributes(重定向携带数据)
  - ServletResponse(response)、SessionStatus、UriComponentsBuilder、ServletUriComponentsBuilder
- 自定义对象参数
  - 可以自动类型转换与格式化，可以级联封装。
  - WebDataBinder 利用它里面的 Converters 将请求数据转成指定的数据类型。再次封装到JavaBean中
- 原生请求
  - HttpServletRequest
  - HttpServletResponse

#### Controller返回结果
- ModelAndView
- Model
- View
- ResponseEntity
- ResponseBodyEmitter
- StreamingResponseBody
- HttpEntity
- HttpHeaders
- Callable
- DeferredResult
- ListenableFuture
- CompletionStage
- WebAsyncTask
- 有 @ModelAttribute 且为对象类型的
- @ResponseBody 注解 --返回值处理器器--> RequestResponseBodyMethodProcessor

#### 内容协商
- 判断当前响应头中是否已经有确定的媒体类型。MediaType；没有前置拦截过程就为null
- 获取客户端(PostMan、浏览器)支持接收的内容类型。(获取客户端Accept请求头字段)
- 遍历循环所有当前系统的 MessageConverter，看谁支持操作这个对象(Person)
- 找到支持操作返回值类型(Person)的converter，把converter支持的媒体类型统计出来
- 客户端需要【application/xml】。服务端能力【10种、json、xml】
- 进行内容协商的最佳匹配媒体类型，并调用对应的converter进行转化
- 可以在代码中自定义内容协商策略，通过给容器中注册一个WebMvcConfigurer类，并复写对应的方法
```
# 开启浏览器参数方式内容协商功能
spring:
  contentnegotiation:
    favor-parameter: true  
```

#### 拦截器使用
- HandlerInterceptor接口
  - preHandle: 目标方法执行之前，拦截放行规则，返回boolean值
  - postHandle: 目标方法执行之后，还未到达页面之前
  - afterCompletion: 页面渲染完之后，即整个请求处理完成后
- 编写一个拦截器配置类实现HandlerInterceptor接口，实现拦截放行规则
- 定制化MVC功能，复写addInterceptors()方法
- 将编写好的拦截器配置类添加到该方法的入参中，即registry--拦截器注册中心
- 配置拦截器规则，拦截哪些请求(addPathPatterns)，放行那些请求(excludePathPatterns)

#### 拦截器原理
- 根据当前请求，找到HandlerExecutionChain【可以处理请求的handler以及handler的所有拦截器】
- 先来顺序执行所有拦截器的preHandle方法
  - 如果当前拦截器preHandler返回为true。则执行下一个拦截器的preHandle
  - 如果当前拦截器返回为false。直接倒序执行所有已经执行了的拦截器的afterCompletion
- 如果任何一个拦截器返回false。直接跳出不执行目标方法
- 所有拦截器都返回True。执行目标方法
- 倒序执行所有拦截器的postHandle方法
- 前面的步骤有任何异常都会直接倒序触发afterCompletion
- 页面成功渲染完成以后，也会倒序触发afterCompletion
![拦截器原理](https://github.com/CyS2020/Notebook/blob/master/images/%E6%8B%A6%E6%88%AA%E5%99%A8%E5%8E%9F%E7%90%86.png?raw=true)

#### 文件上传
- 文件上传自动配置类 - MultipartAutoConfiguration - MultipartProperties
- 自动配置好了 StandardServletMultipartResolver【文件上传解析器】

#### 异常处理
- 默认情况下，SpringBoot提供 `/error` 处理所有错误的映射
- 对于机器客户端，它将生成JSON响应，其中包含错误，HTTP状态和异常消息的详细信息
- 对于浏览器客户端，响应一个 "whitelabel" 错误视图，以HTML格式呈现相同的数据
- ErrorMvcAutoConfiguration自动配置异常处理规则

#### 定制错误处理逻辑
- DefaultErrorAttributes先来处理异常。把异常信息保存到request域，并且返回null
- /error请求。会被底层的BasicErrorController处理，遍历到默认的DefaultErrorViewResolver，作用是把响应状态码作为错误页的地址，例如error/500.html
- error/404.html error/5xx.html；有精确的错误状态码页面就匹配精确，没有就找4xx.html；如果都没有就触发白页
- @ControllerAdvice + @ExceptionHandler处理全局异常；底层是ExceptionHandlerExceptionResolver支持的 -- (推荐)
- @ResponseStatus + 自定义异常；使用时controller抛出该自定义异常；底层是ResponseStatusExceptionResolver，把@ResponseStatus注解的信息底层调用response.sendError(statusCode, resolvedReason)；tomcat发送的/error
- Spring底层的异常，如参数类型转换异常；DefaultHandlerExceptionResolver处理框架底层的异常，调用response.sendError(HttpServletResponse.SC_BAD_REQUEST, ex.getMessage());
- 自定义实现HandlerExceptionResolver处理异常；可以作为的全局异常处理规则
![系统默认异常解析器](https://github.com/CyS2020/Notebook/blob/master/images/%E7%B3%BB%E7%BB%9F%E9%BB%98%E8%AE%A4%E5%BC%82%E5%B8%B8%E8%A7%A3%E6%9E%90%E5%99%A8.png?raw=true)

#### Web原生组件注入（Servlet、Filter、Listener）
- 使用Servlet API
  - @ServletComponentScan(basePackages = "xxx"): 指定原生Servlet组件都放在那里
  - @WebServlet(urlPatterns = "/my"): tomcat直接响应，遵循url精确匹配原则
  - @WebFilter(urlPatterns={"/css/*","/images/*"}): 拦截修改请求/响应
  - @WebListener: 监听某种事件，通过回调函数，反馈给程序去做一些响应的处理
- 使用RegistrationBean
  - ServletRegistrationBean
  - FilterRegistrationBean
  - ServletListenerRegistrationBean

#### 嵌入式Servlet容器
- 默认支持的webServer, Tomcat, Jetty, Undertow
- ServletWebServerApplicationContext容器启动寻找ServletWebServerFactory并引导创建服务器
- 内嵌服务器，就是手动调用启动服务器的代码（前提是tomcat核心jar包已经依赖）
- 定制Servlet容器
  - 修改配置文件对应的属性ServerProperties.class
  - 直接自定义 ConfigurableServletWebServerFactory
  - WebServerFactoryCustomizer程序化定制

#### springMVC相关知识(了解)
- 登录成功后重定向到主页，重定向可以防止表单重复提交

#### 定制化原理
- 修改配置文件
- 使用xxxCustomizer定制
- 编写自定义的配置类 xxxConfiguration + @Bean 替换、增加容器中默认组件
- 定制化Web功能：编写配置类实现WebMvcConfigurer接口即可定制web功能 + @Bean扩展组件 --（推荐）
- @EnableWebMvc + WebMvcConfigurer + @Bean 可以全面接管SpringMVC，所有规则全部自己配置，实现定制和扩展功能
- 原理分析套路：**场景starter** -> xxxAutoConfiguration -> @Bean导入xxx组件 -> **绑定xxxProperties.class**

#### 数据源
- 数据源自动配置HikariDataSource
- 官方不导入驱动因为官方不知道我们接下要操作什么数据库
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>
```
![数据源配置](https://github.com/CyS2020/Notebook/blob/master/images/%E6%95%B0%E6%8D%AE%E6%BA%90%E9%85%8D%E7%BD%AE.png?raw=true)
- 使用Druid数据源DruidDataSource
- DruidDataSourceAutoConfigure自动配置类；配置项：spring.datasource
  - DruidSpringAopConfiguration: 监控SpringBean的；配置项：spring.datasource.druid.aop-patterns
  - DruidStatViewServletConfiguration: 监控页的配置：spring.datasource.druid.stat-view-servlet；默认开启
  - DruidWebStatFilterConfiguration: web监控配置；spring.datasource.druid.web-stat-filter；默认开启
  - DruidFilterConfiguration: 所有Druid自己filter的配置
```
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
</dependency>
```

#### 整合MyBatis操作
- 数据源和ORM功能不同，数据源提供访问数据库，监控SQL等功能
- MybatisAutoConfiguration自动配置类；配置项绑定类：MybatisProperties；配置项：mybatis
- 导入mybatis官方starter，在application.yml配置mysql相关配置信息
- 配置@MapperScan扫描的包路径(项目启动类)
- 配置classpath扫描的xml文件路径(配置文件)
- 编写接口并添加@Mapper(若配置了@MapperScan可省略)注解，编写sql映射文件并绑定到mapper接口
```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
</dependency>
```
![mybatis包依赖](https://github.com/CyS2020/Notebook/blob/master/images/mybatis%E5%8C%85%E4%BE%9D%E8%B5%96.png?raw=true)

#### 整合Mybatis-Plus
- MybatisPlusAutoConfiguration自动配置类；配置项绑定类：MybatisPlusProperties；配置项：mybatis-plus
- 只需要我们的Mapper接口(Dao层接口)继承`BaseMapper接口`就可以拥有crud能力
- 也可以从Service接口层面继承`IService<T>`， 然后从Service实现类层面继承`ServiceImpl<M extends BaseMapper<T>, T>`从而拥有crud能力
```
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId> 
</dependency>
```

#### 整合redis
- RedisAutoConfiguration自动配置类；配置项绑定类：RedisProperties.class；配置项：spring.redis
- redis连接工厂：LettuceConnectionConfiguration、JedisConnectionConfiguration 使用jedis客户端
- RedisTemplate，StringRedisTemplate连接redis，JdbcTemplate连接mysql；整合第三方连接工具xxxTemplate
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
  
#### 整合JUnit5
- SpringBoot 2.4 以上版本移除了默认对 Vintage 的依赖。如果需要兼容junit4需要自行引入
- 了解常用注解：`https://junit.org/junit5/docs/current/user-guide/#writing-tests-annotations`
![Junit5框架](https://github.com/CyS2020/Notebook/blob/master/images/JUnit5%E6%A1%86%E6%9E%B6.png?raw=true)
  
#### 指标监控SpringBoot Actuator
- Actuator场景，使得我们每个微服务快速引用即可获得生产级别的应用监控、审计等功能；参与线上管理的功能
- EndPoint监控端点：默认以JMX方式暴露，需要配置以Web方式暴露所有端点
- 最常用的EndPoint：Health(监控状况)，Metrics(运行时指标)，Loggers(日志记录)
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

#### Profile功能
- 默认配置文件`application.yaml`；任何时候都会加载
- 指定环境配置文件`application-{env}.yaml`
- 激活指定环境：配置文件激活、命令行激活
- 其他重要功能：@Profile条件装配功能、profile分组功能
  
#### 外部配置
- 外部配置源：Java属性文件、YAML文件、环境变量、命令行参数
- 配置文件查找位置
  - classpath 根路径
  - classpath 根路径下config目录
  - jar包当前目录
  - jar包当前目录的config目录
  - /config子目录的直接子目录
- 配置文件加载顺序
  - 当前jar包内部的application.properties和application.yml
  - 当前jar包内部的application-{profile}.properties 和 application-{profile}.yml
  - 引用的外部jar包的application.properties和application.yml
  - 引用的外部jar包的application-{profile}.properties 和 application-{profile}.yml
- 指定环境优先，外部优先，后面的可以覆盖前面的同名配置项

#### 自定义starter
- 一个场景启动器默认只声明当前场景的依赖，没有任何代码；引入当前场景的自动配置包
- starter-pom引入`autoconfigure`包，场景的代码写在该包内
![starter原理](https://github.com/CyS2020/Notebook/blob/master/images/starter%E5%8E%9F%E7%90%86.PNG?raw=true)
- autoconfigure包中配置使用`META-INF/spring.factories`中`EnableAutoConfiguration`的值，使得项目启动加载指定的自动配置类
- 编写自动配置类 xxxAutoConfiguration -> xxxProperties.class
  - @Configuration
  - @Conditional
  - @EnableConfigurationProperties
  - @Bean
  - ......
- 引入starter -> xxxAutoConfiguration -> 容器中放入组件 -> 绑定xxxProperties.class -> 配置项

#### 启动关键组件
- ApplicationContextInitializer
- ApplicationListener
- SpringApplicationRunListener
- ApplicationRunner
- CommandLineRunner
- 前三个去`spring.factories`里面找；后两个容器中找