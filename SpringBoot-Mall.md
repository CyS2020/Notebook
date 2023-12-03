### 项目总结

#### 开发工具的插件安装
- IDEA: Free MyBaits plugin; Lombok; One Dark Theme;
- VS code: 
Auto Close Tag; Auto Rename Tag; Chinese (Simplified); ESLint; HTML CSS Support; 
HTML Snippets; JavaScript (ES6) code snippets; Live Server; open in browser; Vetur

#### 创建SpringBoot项目
- 下载maven, 并配置maven settings.xml文件
- 使用github创建仓库, .gitignore选java, 复制url地址然后使用IDEA -> File -> New -> Project from Version Control
- https://start.spring.io/ 使用网站创建一个springBoot项目(微服务), 选择必要的starter, 然后下载
- 修改springBoot版本，启动项目若启动失败查看pom文件中其他依赖有没有配置与springBoot相一致的版本
- 清理文件保留.idea, src, pom.xml, .iml, .gitignore文件,其他的和代码无关的文件可以删除
```
<mirror> 
    <id>alimaven</id>  
    <name>aliyun maven</name>  
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
    <mirrorOf>central</mirrorOf> 
 </mirror>
<profile>
    <id>jdk-1.8</id>
    <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    
    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```

#### 设计表
```sql
DROP TABLE IF EXISTS `mall_order`;
CREATE TABLE `mall_order` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '订单id',
  `order_no` bigint(20) DEFAULT NULL COMMENT '订单号',
  `user_id` int(11) DEFAULT NULL COMMENT '用户id',
  `shipping_id` int(11) DEFAULT NULL,
  `payment` decimal(20,2) DEFAULT NULL COMMENT '实际付款金额,单位是元,保留两位小数',
  `payment_type` int(4) DEFAULT NULL COMMENT '支付类型,1-在线支付',
  `postage` int(10) DEFAULT NULL COMMENT '运费,单位是元',
  `status` int(10) DEFAULT NULL COMMENT '订单状态:0-已取消-10-未付款，20-已付款，40-已发货，50-交易成功，60-交易关闭',
  `payment_time` datetime DEFAULT NULL COMMENT '支付时间',
  `send_time` datetime DEFAULT NULL COMMENT '发货时间',
  `end_time` datetime DEFAULT NULL COMMENT '交易完成时间',
  `close_time` datetime DEFAULT NULL COMMENT '交易关闭时间',
  `create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  `update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `order_no_index` (`order_no`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```
- 表名；表字段(表字段名；类型；是否空；设置默认值；注释)；设置key与索引；设置存储引擎；设置编码

#### windows下mysql常用命令
- net start mysql_cys : 启动mysql
- mysql -u root -p : 登录mysql
- show databases; : 显示数据库 
- use xxx; : 使用某个数据库
- show tables; : 显示某个数据库中的表
- desc xxx; : 显示某张表的结构
- exit : 退出数据库
- net stop mysql_cys : 关闭mysql

#### 事务
- @Transactional 注解底层是由数据库来决定的, 常用的引擎有InnoDB和MyISAM(不支持事务)
- 事务一定是数据库的事务, 而不是java代码的事务, 出现RuntimeException的时候回滚
- Redis所有单个命令的执行都是原子性的, redis不支持回滚操作

#### 主键与索引
- 表关系、表结构、唯一索引、单索引及组合索引、时间戳
- 主键一定是唯一性索引，唯一性索引并不一定就是主键。
- 一个表中可以有多个唯一性索引，但只能有一个主键。
- 主键列不允许空值, 而唯一性索引列允许空值

#### java对象
- po: persistent object 持久层对象
- pojo: plain ordinary java object 普通java对象只有属性+get/set方法

#### Model层(模型层)
- dao层：dao层叫数据访问层, 全称为data access object, 属于一种比较底层, 比较基础的操作, 具体到对于某个表、某个实体的增删改查, 取出数据封装后返回给service层
- service层：service层叫服务层, 被称为服务, 肯定是相比之下比较高的层, 比如取得连接、关闭数据库连接、事务回滚, 一些复杂的逻辑业务, 相当于将几种操作封装起来

#### 框架理解
- 想要框架去执行某些文件xml中的方法需要让框架找到xml, 则需要application.yml配置文件
- 关于配置文件application.yml的配置，可以通过命令行在部署的时候修改，例如修改日志输出位置等
- java代码与框架交互，要么通过注解要么配置xml, 将调用转向注解或者xml的具体行为，java定义抽象

#### mybatis三剑客
- Mybatis-generator: 用来生成pojo(类)、dao(接口)、mapper(xml)文件
1. 需要配置generatorConfig.xml文件
2. mvn mybatis-generator:generate 生成命令
3. 插件会去连接数据库，找到表，然后生成表的类，查询接口，链接mysql的xml
4. 项目中会把使用mybatis插件生成代码的任务单独设置一个模块，不和业务代码扯在一起；mybatis依赖还是需要的
```
<plugin>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-maven-plugin</artifactId>
    <version>1.3.7</version>
    <configuration>
        <overwrite>true</overwrite>
    </configuration>
</plugin>
```
- Mybatis-plugin: 用来查找方法对应的xml中的sql语句
1. 此款插件是在IDEA里安装的，替代品为：Free MyBaits plugin
2. 在xml文件手动编写xml语句时，如果编写正确插件的箭头就会指向对应的接口的
3. 整个过程就是在拼凑sql查询语句，非<>代码就是固定的sql语句，<>内的代码是在根据参数在拼凑变化的那部分
- Mybaits-PageHelper: 数据库分页插件

#### Spring
- IOC 控制反转, DI依赖注入从不同角度描述同一件事情; 原来代码直接new实现类写死代码, 现在通过创建合适的实现类注入到服务中, 
可以注入不同的实现类只要实现接口即可, 即我给你配置了什么类你就用什么类就行 了, 代码的控制权发生了变化, 以前是你决定用什么; 
例如伐木工人砍树需要创建(new)一把斧子然后执行砍树操作, 现在给伐木工人一把斧子然后执行砍树操作, 至于是什么类型的斧子并不重要(只要实现了砍树功能接口)
- AOP面向切片编程, 横向抽取机制, 例如给类添加性能检测, 事务控制, 日志打印等无法, 并不能使用纵向继承这些功能. 
spring aop就是将切面所定义的横向逻辑织入到切面所制定的连接点中; spring aop使用包括: 目标类、注解(非必须项)、切面类(定义切点和通知)
- MVC: Model(模型)、View(视图)、Controller(控制器);主要包含四个接口: DispatcherServlet(整个Web应用的控制器)、
HandlerMapping、Controller(单个Http请求处理过程中的控制器)和ViewResolver

#### BigDecimal类
- 使用BigDecimal.valueOf(0.01), 不要使用new BigDecimal(0.01)将来精度会出问题
- 上述与new BigDecimal("0.01")等价, 注意查看源码实现

#### 微信支付账户
- Native支付，用户扫商户的二维码
- appid: 微信公众账号或开放平台APP的唯一标识，一个应用可以对应多个支付产品
- mch_id: 微信支付分配的商户收款账号
- mchKey: 用于交易过程生成签名的密钥
- notify_url: 用于接收微信发送的异步通知的链接(地址)
- openid是微信用户在公众号appid下的唯一标识，appid不同获取到的openid就不同，公众号小程序必传参数

#### 支付宝支付账户
- 电脑网站支付，用户扫商户的二维码
- 商户应用公钥、商户用用私钥(Java应用需要)、支付宝公钥(Java应用需要)、支付宝私钥(对于我们未知)
- 发起支付: 商户(商户应用私钥签名) -> 支付宝(商户应用公钥验签) 
- 异步通知: 支付宝(支付宝私钥签名) -> 商户(支付宝公钥验签)
- 这种方式为RSA非对称加密(私钥签名-公钥验签), 支付内容加密一般使用AES密钥(对称)
- app_id + private_key + ali_pay_public_key + notify_url + return_url

### 支付过程
- 用户在购买物品的时候一般会选择微信或者支付宝进行支付
- 因此在点击下单之后，需要发起微信/支付宝支付，支付完后微信/支付宝会把钱打到我们的账户
- 根据设置好的微信支付账户/支付宝支付账户(对公账户)，传入订单信息调用其开放接口，会返回给我们一些用于生成二维码的文本信息，同步过程
- 拿到这个文本使用前端技术生成二维码，用户便可以扫码支付了，支付成功后微信/支付宝会发异步通知给我们，异步过程
- 关于二维码的生成，在拿到用于支付的文本信息后，可以使用后端技术也可以使用前端技术JQuery生成二维码，发起支付时需要跳转到浏览器中进行支付
- 发起支付与异步通知是两次独立的http请求，一次是我们(pay)请求(post)微信/支付宝，返回用于生成二维码的链接，
一次是微信/支付宝请求(post)我们(pay)，返回给他固定内容的文本(表示已经收到异步通知了)，可以使用retrofit客户端

#### 转账过程
- 建行(张三) -> 网联 -> 农行(李四) 给你转钱了(request请求)
- 建行(张三) <- 网联 <- 农行(李四) 我收到钱了(request请求) 支付成功结果此处为准:即平台发来的异步通知
- 跳转会直接跳到浏览器中打开,参数放在url中,使用跳转发起支付场景, 而get请求会获得网页的html源码
- 在浏览器中支付成功后，会收到支付平台的异步通知，然后支付系统(pay)在发异步通知给业务系统(mall)
- 微信支付中模式二不需要在微信支付后台开发配置中设置扫码回调链接
- notify_url是用来接收支付平台的异步通知的, notify_url不一定必须用域名, 也可以使用ip地址
- 云服务器的公网ip可以直接访问系统, 家庭宽带公网ip也不行, 使用natapp软件

#### 重复发起支付
```
业务系统订单号    支付系统订单号    支付平台
     1111             2222       ->     
     1111             3333       ->
```
- 发起第二次支付前, 把上一次的支付订单关闭(调用支付平台的API)
- ajax(前端技术)请求后端的api(通过订单号查询支付状态), 如果是已支付则进行跳转；在我们返回的ftl界面里定时轮询请求后端api

#### 模块的分割
- 一般来说商城系统, 支付系统, 包括用于生产dao层和pojo的mybatis系统都是各自独立的
- 此项目中为了(比较小)就没有将用mybatis生成dao层和pojo的系统独立出来, 商城系统-支付系统-MyBatis系统

#### 用户模块开发
- 如何查看是否前后端分离架构：F12 -> NetWork -> XHR -> Response
- Content-Type:application/json 这是调用API的请求格式，在请求头中(Headers)
- 开发顺序: Dao -> Service -> Controller
- 涉及对象: 前端表单对象、业务处理对象、数据库存储对象、后端返回对象
- 单元测试: Service层(主要的业务逻辑)，Controller层的Api由测试人员负责
- 对于无法自动生成的查询接口需要在xml文件中编写按照格式编写sql语句，Mybatis打印SQL语句
- Spring中自带MD5散列算法DigestUtils类，实现对password的加密

#### 前端请求方式
- body使用x-www-form-urlencoded + key-value来请求: 后端可以使用对象中的域来接收(配合@RequestParam), 或者使用对象来接收(无需注解)
- body使用raw + json格式来请求: 后端使用对象来接收(配合@RequestBody), 使用@Valid验证对象中字段是否符合要求, @NotBlank/@NotNull/@NotEmpty
- url中使用的入参注解@PathVariable来绑定传入的参数, 不是url中带的则需要使用上面方式定义form对象来接收

#### cookie session 跨域
- 域名与域名不一样、IP与IP地址不一样、域名与IP(localhost与127.0.0.1)比较都算跨越
- cookie不安全session安全, 后端开发只操作session, session与cookie不分家, session就是用到了cookie来实现的, cookie是实现Session的一种方式
- 服务端需要通过session来识别具体的用户, 服务端要为特定用户创建特定的session, 用于标识这个用户并且跟踪
- 那么问题来了session如何来识别具体的用户呢？客户端会将cookie信息发送到服务端, cookie里面记录一个Session ID
- session是抽象的概念, cookie是具体的概念, cookie是session一种具体的实现方式
- 会话跟踪cookie与session，可以理解为cookie是一个箱子，里面可以填内容信息；如果填具体信息那就是cookie客户端机制，如果是填sessionId具体信息存在服务器则是session机制
- 在很多操作中都需要检查用户是否登录因此通过在代码中编写拦截器进行预检查(实现HandlerInterceptor.preHandle())还需配置拦截哪些url; 另外还可以用AOP的方式拦截
```
       前端        ->           后端
cookie(sessionId)  ->   session(HttpSession)
```
#### 耗时
- http(请求微信api) > 内网 + 磁盘(mysql) > 内存
- 代码中最忌讳的就是在for循环中做http、sql

#### NoSQL数据库
- Redis(高性能), MongoDB(海量数据), Elasticsearch/HBase(大数据)

#### 订单
- 不可变：商品图片、收货地址; 可变：状态、价格(波动)
- 订单中需要将一些会变化的数据存贮起来, 不能用到的时候去数据库里查询不能代表下单时的数据了

#### 基础知识
- 关于Linux命令, vim命令, docker命令的补充学习; java的http客户端学习
- 外网无法访问的调试技巧curl http://127.0.0.1:8080/xxx接口xxx 查看内网访问能否成功

### centos7环境
#### 配置静态ip
- VMware - 编辑 -虚拟网络编辑 -选择桥接模式 -桥接至主机网络适配器上
- dhclient命令生成ip，然后修改vim /etc/sysconfig/network-scripts/ifcfg-ens33
- ps -ef | grep dhclient 查看进程 然后 kill -9 进程id 关闭该进程
- 配置静态ip地址，注意的是先查看主机ip地址信息然后配置ip，子网掩码，网关，dns; 虚拟机网络适配器选桥接模式(不勾选复制...)
- 配置静态ip后, 虚拟机linux执行systemctl restart network.service就可以连接外网
- 虚拟机上网，主机与虚拟机互相ping，才算成功；一般情况下主机都能ping通虚拟机，只是虚拟机有时候ping不通主机
- 控制面板\网络和 Internet\网络和共享中心\高级共享设置--启用文件和打印机共享，虚拟机即可ping通主机
- 如果还出现ping不通的情况检查下面的参数肯定写错了，检查不出来就删了从写
- 发现数据库还是连接不上, 或者其他服务redis等通过 telnet ip port 来测试端口访问, 访问失败则关闭防火墙
- 防火墙状态变化可能会引起docker自定义链有问题, 最好重启下docker
```
BOOTPROTO=static
ONBOOT=yes
IPADDR=192.168.0.109
NETMASK=255.255.255.0
GATEWAY=192.168.0.1
DNS1=8.8.8.8
```

#### 相关部署知识
- 在root目录下: vim .bash_profile 根目录下配置环境变量并source .bash_profile
- 配置环境变量后: 使用ngnix来启动Nginx服务端程序运行容器类似于Tomcat, 使用java运行jar包
- systemctl status firewalld.service查看防火墙状态 systemctl stop firewalld.service关闭防火墙
- systemctl daemon-reload systemctl restart docker.service 启动docker
- 安装mysql, 修改访问密码, 重启mysql并开启远程访问的ip地址(使用数据库服务器的地址), Navicat连接成功
- systemctl start  mysqld.service启动mysql systemctl status mysqld.service查看mysql状态
- systemctl start  dcoker 启动docker 然后查看下状态命令同查mysql的命令
- docker run -d -p 5672:5672 -p 15672:15672 rabbitmq:3.8.2-management 启动rabbitmq
- docker run -d -p 6379:6379 redis:5.0.7 启动redis 并在管理界面创建相应的队列payNotify
- 下载运行natapp使用内网穿透访问虚拟机ip地址, 接收微信和支付宝发来的异步通知
- 运行两个jar包, 即mall项目与pay项目, 一定要先运行natapp再启动pay项目, 保证natapp在Online状态

#### 运行jar包
- java -jar mall-0.0.1-SNAPSHOT.jar 命令运行主程序
- java -cp [jar包的路径] [类路径] 命令行运行某个类
- 将所有依赖的包打在一起，使用下面的插件，可以配置主类入口信息，下面没有配置
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>2.4.3</version>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>shade</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

#### 关于启动java服务
- 运行jar包命令：java -jar -Dspring.profiles.active=prod -Dserver.port=8081 -Dlogging.path=/root/logs/mall/ /root/mall.jar
- 或者作为服务后台运行，创建文件/etc/systemd/system/mall.service 然后运行 systemctl daemon-reload 与 systemctl start mall
```
[Unit]
Description=mall
After=syslog.target

[Service]
User=root
ExecStart=/user/local/jdk1.8.0_231/bin/java -jar -Dspring.profiles.active=prod -Dserver.port=8081 -Dlogging.path=/root/logs/mall/ /root/mall.jar


[Install]
WantedBy=multi-user.target
```

#### 反向代理
- http://192.168.1.4/api/products  前端访问的
- http://192.168.1.4:8080/products 后端提供的
- 通过修改nginx配置文件来进行反向代理cd /user/local/nginx/conf/
- 添加如下代理后 nginx -s reload 刷新配置
```
location /api/ {
    proxy_pass http://127.0.0.1:8080/;
}
```

### Spring常用注解
#### `spring-boot-starter-test`
- `@RunWith(SpringRunner.class)` : 注解是一个测试启动器，可以加载Springboot测试注解。
- `@SpringBootTest` : 通过@RunWith 和 @SpringBootTest启动spring容器。
- `@Transactional` : 这个是数据库事务的注解; 在单测中使用该注解避免修改数据库该注解会自动回滚，他会自动带有@Rollback(true)

#### `spring-boot-starter-web`
- `@Component` : 调用无参构造器创建一个bean对象, 并把对象存入spring的IOC容器, 交由spring容器进行管理
- `@Controller` : 作用@Component相同, 一般用于表现层注解
- `@Service` : 作用@Component相同, 一般用于业务层注解
- `@Repository` : 作用@Component相同, 一般用于持久层注解
- `@Bean` : 用于把当前方法的返回值作为bean对象存入spring的IOC容器中, 一般是需要初始化数据的类
- `@Configuration` : 搭配@Bean使用在方法中设置必要信息, 一般用于配置类; 启动时即运行类中的方法
- `@Autowired` : 是spring动态装配bean的注解, 默认按照类型进行装配(byType)
- `@ConfigurationProperties(prefix = "xxx")` : 参数配置在application.properties或application.yml文件中, 搭配@Component使用
- `@SpringBootApplication` : 目的是开启自动配置 = @Configuration + @EnableAutoConfiguration + @ComponentScan。
- `@ControllerAdvice` : 全局异常处理@ExceptionHandler, 添加全局数据@ModelAttribute, 请求参数预处理@InitBinder
- `@ExceptionHandler(RuntimeException.class)` : 注解用来指明异常的处理类型, 如果这里指定为RuntimeException；可以更具体空指针数组越界之类的

<br/>

- `@RequestMapping` : 映射HTTP请求，也就是通过它来指定控制器可以处理哪些URL请求, 主要有path、method、params属性
- `@PostMapping` : 注解将HTTP的POST请求映射到特定的处理程序方法, 等于@RequestMapping(method = RequestMethod.POST)
- `@DeleteMapping` : 注解将HTTP的DELETE请求映射到特定的处理程序方法, 等于@RequestMapping(method = RequestMethod.DELETE)
- `@PutMapping` : 注解将HTTP的PUT请求映射到特定的处理程序方法, 等于@RequestMapping(method = RequestMethod.PUT)
- `@GetMapping` : 注解将HTTP的GET请求映射到特定的处理程序方法, 等于@RequestMapping(method = RequestMethod.GET)
- `@PathVariable` : 将 URL 中的占位符绑定到控制器的处理方法的参数中，占位符使用{}括起来；url中的占位符与?后的参数是有区别的
- `@RequestBody` : 用来接收前端传递给后端的json字符串中的数据的(请求体中的数据的), 前端使用`非GET`方式进行提交, spring组装json为对象
- `@RequestParam` : 用来处理body使用x-www-form-urlencoded来请求; url中的?后面参数也可以用@RequestParam来接收
- `@ResponseBody` : 将java对象转为json格式的数据, 将controller的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到response对象的body区
- `@ResponseStatus` : 用就是为了改变HTTP响应的状态码, 返回你指定的状态码
- `@RestController` : 相当于@ResponseBody ＋ @Controller, @Controller注解会返回一个ModelAndView; 如果需要返回到指定页面, 则需要用 
@Controller配合视图解析器InternalResourceViewResolver才行; 如果需要返回JSON，XML或自定义mediaType内容到页面，则需要在对应的方法上加上@ResponseBody注解。
<br/>

#### `mybatis-spring-boot-starter`
- `@Mapper` : 添加了@Mapper注解之后这个接口在编译时会生成相应的实现类, 需要注意的是:这个接口中不可以定义同名的方法，因为会生成相同的id也就是说这个接口是不支持重载的
- `@Select("select * from mall_category where id = #{id}")` : 对于多个参数来说, 每个参数之前都要加上@Param注解, 要不然会找不到对应的参数进而报错
- `@MapperScan(basePackages = "xxx")` : 想要每个接口都要变成实现类, 那么需要在每个接口类上加上@Mapper注解比较麻烦解决这个问题用@MapperScan, 是在Springboot启动类上面添加
- `@Param` : sql语句查询时传入的不是基本数据类型时，要使用该注解绑定下参数到xml中

#### 其他注解
- `@Valid` : 对象属性字段的规则检测, 对象属性需要进行验证; 在定义对象的字段上使用@NotNull、@NotBlank、@NotEmpty常用作入参检验；javax包里的
需要注意的是 @Valid 和 BindingResult 是一一对应的，如果有多个 @Valid，那么每个 @Valid 后面都需要添加 BindingResult 用于接收 bean 中的校验信息
- `@JsonInclude(value = JsonInclude.Include.NON_NULL)` : 序列化不为null的那些属性，是jackson包下的，spring自带的序列化工具就是它

### linux命令
#### 文件和目录
- `cd` 进入个人的主目录
- `cd /home` 进入 '/ home' 目录; 
- `cd ..` 返回上一级目录
- `pwd` 显示工作路径
- `ls` 查看目录中的文件
- `ls -l` 显示文件和目录的详细资料 
- `mkdir dir1` 创建一个叫做 'dir1' 的目录' 
- `mkdir -p /tmp/dir1/dir2` 创建一个目录树 
- `rm -rf dir1` 删除一个叫做 'dir1' 的目录并同时删除其内容 
- `mv dir1 new_dir` 重命名/移动 一个目录到新目录里
- `cp file1 file2` 复制一个文件 
- `chmod +x a.txt` 给所有用户给予a.txt文件可执行权限
- `scp local_file remote_username@remote_ip:remote_folder` 从本地复制到远程

#### 其他命令
- `su root` 切换为root身份需要输入密码
- ·`vim .bash_profile` 根目录下配置环境变量用cd直接进入根目录
- `source .bash_profile` 使环境变量生效
- `yum list` 列出所有可安裝的软件清单命令
- `yum install <package_name>` 仅安装指定的软件命令
- `yum update <package_name>`仅更新指定的软件命令
- `yum remove <package_name>` 删除软件包命令
- `wget  https:// xxx .tar.gz .zip` 下载安装包
- `tar -xzvf test.tar.gz` 解压文件
- `unzip test.zip` 解压文件需要安装zip程序
- `./configure --prefix=/usr/local/test` 配置
- `make` 编译
- `make install` 安装
- `./` 表示执行当前目录下的某个文件
- `ps -ef | grep java` 显示当前java进程
- `kill -9 xxid` 杀死进程
- `systemctl daemon-reload` 重新加载某个服务的配置文件
- `systemctl start *d.service` 启动服务
- `systemctl stop *d.service` 停止服务
- `systemctl restart *d.service` 重启服务
- `systemctl reload *d.service` 重新加载服务配置文件
- `systemctl status *d.service` 查询服务运行状态

### spring框架思考
- 依赖注入是服务与服务(组件)之间的关系，如果你本身不是服务或者组件，内部注入不注入的没什么用
- 常用注入方式: 构造器注入，属性注入，方法参数注入
- 配置方式: xml配置，注解配置，java配置。涉及基本(数据库)配置用xml，涉及业务配置用注解，涉及第三方jar包中的类用java配置
- AOP的作用: 某写已经存在的方法上追加功能，且源码无需修改，通知类代码有则使用注解，无则使用@DeclareParents连接目标类与代理类
- 实现AOP的操作: 1.编写通知类打上@Aspect注解, 2.编写切点方法打上@Pointcut注解, 3.方法上设置通知注解, 4.编写通知方法执行逻辑
- 引入中间件三部曲: 1. 配置依赖--pom文件, 2. 中间件基本配置信息--yml文件, 3. main函数上引入必要的注解

### 关于serlvet的思考
1. 任何web应用程序，必然包括 接收请求、处理请求、响应请求，然而接收与响应是共性的，且没有差异。
2. 响应与接收抽取成web服务器，而处理逻辑是不同的抽取成servlet，定义了一套接口，遵循这个接口就能处理请求
3. 使用java代码实现servlet的处理逻辑就是后端开发了，开发后的程序需要运行在servlet容器上例如tomcat
4. web服务器处理http请求时，负责处理静态请求，遇到动态请求交给servlet容器，servlet容器根据url找到对应的servlet应用程序(映射器)，spring框架核心组件DispatcherServlet
5. tomcat等servlet容器，将请求的http报文转为HttpServletRequest、HttpServletResponse(空)对象，处理后数据填入会填入HttpServletResponse返回
6. 后端的入参来自HttpServletRequest我们在控制器中参数列表内容来自这里，然而我们基本不会用到这个类，框架会帮我们解析参数列表，这就涉及到Controller参数绑定，前后端对象的定义
7. servlet容器会为每一个servlet请求创建一个新的java线程，spring能在多线程的环境下将各个线程隔离，用的就是ThreadLocal

### 面试问题
#### Spring与SpringBoot区别
- 创建独立的 Spring应用。
- 嵌入式 Tomcat、 Jetty、 Undertow容器（无需部署war文件）。
- 提供的 starters 简化构建配置 
- 尽可能自动配置 spring应用。
- 提供生产指标,例如指标、健壮检查和外部化配置
- 完全没有代码生成和 XML配置要求

#### SpringBoot Starter工作原理
- Spring Boot 在启动时扫描项目所依赖的JAR包，寻找包含spring.factories文件的JAR
- 根据spring.factories配置加载AutoConfigure类
- 根据 @Conditional注解的条件，进行自动配置并将Bean注入Spring Context

#### Spring事务什么时候会失效
- 发生自调用，类里面使用this调用本类的方法(this通常省略)，此时这个this不是代理对象，而是UserService类本身，所以失效。
解决办法很简单，让this变成UserService的代理类即可。（即从spring的IOC容器中取出对象）
- 方法不是public的，@Transactional只能用于public的方法上，否则事务会失效，如果要用在非public上，可以开启AspectJ代理模式
- 数据库不支持事务 （Mysql的Myisam引擎）
- 没有被spring管理
- 异常被吃掉，事务不会回滚。

#### Bean 生命周期
- 实例化（Instantiation）：在这个阶段，Spring 会根据配置信息创建一个 Bean 的实例。通常情况下，Spring 支持通过构造函数实例化 Bean 或者使用工厂方法进行实例化。
- 属性赋值（Populate Properties）：在实例化之后，Spring 将根据配置或注解中定义的属性值，将相应的属性值赋给 Bean 实例。
- 初始化（Initialization）：
  - 初始化回调方法：如果 Bean 实现了 InitializingBean 接口，Spring 将调用其 afterPropertiesSet() 方法进行初始化。
  - 自定义初始化方法：如果配置文件或注解中指定了自定义初始化方法（如 `<bean init-method="init">` 或 @PostConstruct 注解），Spring 将调用该方法进行初始化。
- 使用（In Use）：此时 Bean 实例已完成初始化，并可以被应用程序使用。在这个阶段，Bean 可以处理业务逻辑、响应请求等。
- 销毁（Destruction）：
  - 销毁回调方法：如果 Bean 实现了 DisposableBean 接口，Spring 将调用其 destroy() 方法进行销毁。
  - 自定义销毁方法：如果配置文件或注解中指定了自定义销毁方法（如 `<bean destroy-method="cleanup">` 或 @PreDestroy 注解），Spring 将调用该方法进行销毁。

OC（Inversion of Control，控制反转）和 AOP（Aspect-Oriented Programming，面向切面编程）是 Spring 框架的两个核心概念。

#### IOC 原理：
IOC 的核心思想是将对象的创建和依赖关系的管理从应用程序代码中解耦，由容器负责管理对象的生命周期和依赖关系。
在 Spring 中实现 IOC 的方式是通过依赖注入（Dependency Injection，DI）。即，在配置文件或注解中定义对象之间的依赖关系，Spring 容器在启动时负责创建和管理这些对象，并自动将依赖注入到相应的位置。
Spring 使用反射、工厂模式和代理等机制来实现 IOC。它利用配置文件（如 XML 配置文件）、注解或 Java 配置类来描述对象及其依赖关系，然后使用反射创建对象并将依赖项注入到对象中。

#### AOP 原理：
AOP 是一种编程范式，旨在提供一种用于处理横切关注点（Cross-cutting Concerns）的方式。横切关注点是指存在于应用程序多个部分中的共享功能，如日志记录、事务管理等。
在 Spring 中实现 AOP 的方式是通过代理模式和动态代理来实现。Spring 使用动态代理技术，在运行时生成代理对象，将横切逻辑织入到目标对象的方法中，从而实现对目标对象的增强。
Spring 提供了两种代理方式：基于接口的 JDK 动态代理和基于类的 CGLIB 代理。JDK 动态代理要求目标对象实现接口，而 CGLIB 代理可以代理没有接口的目标对象。
AOP 基于切面（Aspect）的概念，切面定义了横切逻辑的具体实现，如日志记录、性能监控等。通过在配置文件或注解中定义切面，并通过切点（Pointcut）指定哪些方法会被拦截，Spring 将切面织入到目标对象的方法中。

#### 多个拦截器的顺序
在 Spring 中，可以通过配置多个拦截器（Interceptors）来对请求进行预处理和后处理。拦截器的顺序非常重要，它决定了拦截器执行的顺序。
默认情况下，Spring 拦截器按照它们在配置中声明的顺序进行执行。即先声明的拦截器会先被执行，后声明的拦截器会后执行。可以通过调整拦截器的添加顺序或使用 order() 方法来改变拦截器的执行顺序。
