### SpringBoot简介

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
  - 配置类本身也是组件
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