### Code规约
- 命名规范(包名.类名.函数名.变量名)、函数40行类400行、严格使用访问限制符(private、protected、public)、代码格式化类引入优化
- 捕获异常需要打印描述信息还需要打印堆栈信息：logger.error(e.getMessage, e);
- 一般情况下从map中get某个值都需要进行判空处理, 或者使用getOrDefault()方法; map并行流不要使用keySet,得到value时需要get操作，使用map.foreach()/map.entrySet().forEach()
- java8中的流操作reduce, 第一个参数为上次调用返回的值, 第二个参数为当前遍历到的值, 即a, b两个参数一个为旧值一个为新值
- Boolean值进行逻辑判断时候：Boolean.FALSE.equals(), 基本数据类型boolean前面使用！即可
- 回调函数就是编写的时候不确定的函数行为, 作为参数在调用的时候显示传参, 调用行为执行相应的操作, 回调就是个高大上的词罢了
- String属于不可变类型, 字符串连接的时候使用StringBuilder.append().append...append(); String还有join方法内部就是使用的StringBuilder
- 某个类中, 需要其他组合类：声明时即创建(独立功能), 构造函数中赋值(必要信息), 方法中初始化(加工信息)
- 需要容器时, 只定义声明, 然后在一个函数中创建并填充, 然后将该容器返回给刚才的声明。例如：List<String> list = getList();
- javaBean:1.所有属性为private; 2.提供默认的构造方法; 3.提供getter和setter; 4.实现serializable接口
- 序列化一般都是深拷贝,先去调用类的无参构造方法然后逐个地去调用set方法去填充实例的字段值所以无参构造方法是一定要显式地写在类里避免出现反序列化失败的情况
- 对于一些边界情况, 使用if语句在复杂逻辑开始之前进行单独处理, 避免所有情况全部进入复杂逻辑体，即卫语句
- 在代码中可以多加非null判断：if((xxx = yyy.get()) != null)会增强代码的鲁棒性；
- 在if语句中只对确定性的条件进行判断哪怕代码比较长，否则将来扩展会出问题；同样else后尽量加上if条件判断多一重保障
- 集合在初始化时机: 在声明的时候就初始化(**避免空指针**), 构造函数中构造一些创建类必要的属性, 不要做new集合这么无聊的事情
- 熟练使用Stream流和collect()方法，常见的toList、toSet、toMap、groupingBy; 例: orderList.stream().collect(Collectors.groupingBy(Order::getOrderNo)); list与map互相转换
- 数组转集合使用Arrays.stream(xxx).collect(to) / Stream.of(x, x, x).collect(to) / new ArrayList<>(Arrays.asList(x, x, x));
- 集合转数组xxx.toArray(new T[]); Arrays.asList()与List.subList()返回的List均为李鬼而非李逵, subList()、subMap()、subSet()均为映射
- 常见集合(List, Set, Map)比较相等时, 比较的是集合内的元素, 若元素相等则集合相等; Set在添加重复元素的时候并不会更新, 而Map在添加重复key的时候会更新value的值
- 浮点数在比较的时候不能用`>=,<=,==,!=` 需要一个eps来处理精度问题，等于与不等于特么的一定要用Math.abs(), `>, <`可以直接比较的(16位精度内)
- 浮点数使用Double.compareTo()与BigDecimal.compareTo()进行比较
- split(" ")在分割的时候如果边界处能匹配上, 如果是前边界会有一个null, 结果数组中的第一个元素为null, 后边界没有这个问题
- 判断相等时, 字符常量、枚举类写在前面, 可以避免空指针异常
- 根据不同情况的传值可以使用三元运算符, 或者直接传递, 如果用if-else判断然后传值会显得不专业
- Optional 通常使用 ofNullable() 方法，配合 orElse()、orElseGet()、orElseThrow() 方法使用
- 事务场景中，抛出异常被 catch 后，如果需要回滚，一定要注意手动回滚事务。`TransactionAspectSupport.currentTransactionStatus().setRollbackOnly();`
                                                                                          
### 重构代码
- 需要对当前接口添加新功能, 在接口中添加默认方法(default), 或者定义上层接口由该接口继承
- 对A、B接口进行统一添加一组新功能, 将新功能放在接口C中, 并由A、B接口继承, 新功能只对C接口进行操作即可
- 抽象类、接口中可以没有抽象方法, 编译没问题应该是业务抽象的不够好, 即使没有抽象方法也不能直接实例化
- 继承时尽量不去复写父类已经存在的方法, 继承是为了复用父类的方法, 复写父类的方法不符合里氏替换原则
- Collection接口toArray()默认返回的是Object类型的数组，且无法强转，toArray(T[] a)该接口返回的是指定类型的数组，例如传入new String[0]入参返回String数组;
- 被强制类型转换的实例需要是强制类型转换类型的类或者其子类。String是Object的子类，String[]是Object[]的子类(协变), List\<String\>不是List\<Object\>的子类(可变), 所以会强转失败; Class.cast(obj)方法用于强转
- intValue()是Number类的方法， Number是一个抽象类。Java中所有的数值类都继承它。也就是说，不单是Integer有intValue方法，Double，Long等都有此方法。Number对于6种数值类型的都有对应的抽象方法；在强转过程中浮点数转整型会有精度丢失, 表示范围大的数值转范围小的类型高位丢失, 数值类型往下强转(long -> int)会丢失高位数据
![基本数据类型转换](https://github.com/CyS2020/Notebook/blob/master/images/%E5%9F%BA%E6%9C%AC%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2.png?raw=true)
- 枚举类中的valueOf()、name()、toString()常用方法与枚举类型的字面量有关，与括号内的属性无关，toString()可以被重写，同时记得区分枚举类的静态方法与实例方法
- 三个潜在的取值及以上, 使用switch来控制代码执行流程, 变量-取值-行为-break
- Jdk或其他框架会提供很多工具类：String、Files、Collectors、Collections、Arrays、Comparator / ObjectUtils、BeanUtils、StringUtils、CollectionUtils
- 使用IDEA查找get,set的调用地方, 然后一一修改, 并保证单元测试通过
- 在修改逻辑的时候, 越往里挖需要改动的地方则越少, 所以尽量在深层次源头地方进行修改
- 在修改代码逻辑的时候, 如果多处代码修改逻辑一致, 那就先修改一处并测试, 然后copy即可
- 修改源码涉及两个问题, 一如何根据之前的值确定当前的值, 二当前值修改之后会对原来逻辑流向和现在逻辑流向产生的影响
- 修改多处源码后进行测试的时候进行最小粒度的测试正确性, 与最大粒度的测试性能, 中间排列组合测试没有必要
- 变量命名接口命名以I开头, 实现类以Impl结尾, 抽象类以Abstract开头, 枚举类以Enum结尾
- 代码中根据枚举值返回枚举类型可以通过设置Map返回, 也可以使用for循环来返回(推荐使用)
- 代码中不要出现硬编码, 改变硬编码的方式有两种如果是有分类的可以使用枚举类, 或者创建常量类存贮
- 常量格式public static final 类型 大写_下划线; 枚举类存放的信息多一些
- lambda + stream形式搭配时, 将forEach/map/filter复杂的化抽为函数, 业务复杂不建议使用这种搭配
- 使用if/else if, 里面不要偷懒, 尽量不用else, else的行为太宽泛了, 同理少用!equals()这个方法; 只对确定性的条件判断
- 根据类中某个属性进行比较, 可快速生成比较器, 使用Comparator.comparing(xxx::getXxx)方法, 提供Comparator.reverseOrder()反向排序
- 数组操作善于使用流操作, 使用IntFunction函数式接口来创建数组
  ```
  int[] arr = Arrays.stream(line.split(" ")).mapToInt(Integer::parseInt).toArray();
  String[] strs = Arrays.stream(arr).mapToObj(String::valueOf).toArray(String[]::new);
  ```
- 使用java8中Map的新增方法来优化代码：putIfAbsent、computeIfAbsent(及时加载)、getOrDefault、computeIfPresent方法
- 使用java8中Map的方法：put()、putIfAbsent()--返回旧值、compute()、computeIfAbsent()--返回新值
- jacoco在覆盖率红色表示未覆盖，黄色表示部分覆盖(if不止一个判断), 绿色表示全部覆盖; 异常, if, switch算分支; 不要使用@Data注解它会将每个属性都写入equals()与hashCode()产生大量分支而拉低覆盖率; 如果没有复写hashCode与equals则用的是内存地址进行哈希和判断相等
- 先赋值, 再判空, 再使用; kw检查中有时候会考虑多线程的情况, 即使你只是串行执行的场景
  ```
  if(obj.getField() != null){           f = obj.getField()
      f = obj.getField();        =>     if(f != null){
  }                                     }  
  ```
  
### think in ZTE
- java中默认线程池中的线程在空闲的时候的状态为WAITING状态, 主要是取决于线程池中的workQueue实现方式
- java中有五个生成随机数的类: Random、ThreadLocalRandom、SecureRandom、Math.random()、SplittableRandom
- Arrays.sort()无法自定义比较基本数据类型，只能使用默认的升序，可以说是非常垃圾了，对象的话可以传入比较器
- Java与其他语言编写的程序进行tcp/ip socket通讯时，通讯内容一般都转换成byte数组型，new String(byte[] bytes) <---> getBytes()
- 按照流是否直接与特定的地方 (如磁盘、内存、设备等) 相连，分为节点流和处理流两类。节点流可以从或向一个特定的地方（节点）读写数据；
处理流是对一个已存在的流的连接和封装，通过所封装的流的功能调用实现数据读写。处理流的构造方法总是以一个其他的流对象做参数。一个流对象经过其他流的多次包装，称为流的链接。
- 双向Dijkstra终止条件：从头出发最小值+从尾出发最小值≥目前已知最短路径
- ksp算法：最短路径列表中的边需要排除掉，计算新的路径
- java只有值传递, 没有引用传递, 即使是对象的传递也只是复制一份引用的地址, 传入函数中, 函数中对引用指向修改, 调用方感知不到
- 单例特征：两静态一私有, 静态变量+静态获取单例方法+私有构造函数
- 重写比较器：返回 `-1(<0)` 则参数1排在前面，返回 `1(>0)` 则参数1排在后面; 以下升序排列与降序排列
  ```
  升序                          降序
  return o1 < o2 ? -1 : 1;      return o1 < o2 ? 1 : -1;  (推荐)
  return o1 > o2 ? 1 : -1;      return o1 > o2 ? -1 : 1;
  return o2 > o1 ? -1 : 1;      return o2 > o1 ? 1 : -1;
  return o2 < o1 ? 1 : -1;      return o2 < o1 ? -1 : 1;
  ```
- 传入的参数是函数式接口时，编写的只是功能，并不带有具体的变量，在调用apply等方法时才传入具体的变量：形参非变量，入参可不用
  入参与返回值的类型均是根据上下文环境确定的, 直接编写方法体内的逻辑就好了, 就是简化了函数的定义
- 同步与异步--消息通知的机制--自己等还是别人通知，阻塞与非阻塞--程序在等待调用结果（消息，返回值）时的状态--等待过程中，等待者的操作
- 上述概念组合起来：同步阻塞、同步非阻塞、异步阻塞(一般没有这种情况，直接用同步阻塞就好了)、异步非阻塞
- 初始化：从父到子，从静态变量，静态代码块，变量，代码块，构造函数；变量的特殊情况Set set = new TreeSet<>(comparator)，此时comparator可以为空，在调用comparator之前赋值就可以, 但是不要这么写
- 泛型在类或者接口中定义<T>, extends关键字后面需要跟具体的类型了, 应该是`? extends xxx`而不是`? extends T`; extends后面的类型一定是声明过的
- 静态方法不能访问类中定义的泛型，如果静态方法中数据类型不确定，可以在方法上定义泛型。即下例中<T>就是声名一个泛型T，后面两个T是在使用泛型T。
```
    public static <T> ResponseBean<T> error(String msg,  T data) {
        return new ResponseBean<>(200, data, msg);
    }
```

### think in TrendMicro
- mysql不区分大小写, 主键id也不区分大小写, aws athena也不区分大小写
- `=: 变量名`的方式，在具体调用的时候传入参数，是防止sql注入的写法，也能提高性能
- elasticSearch查询也不区分大小写, 文档`_id`还是区分的
- web与servlet
  - 一般的web请求(Url + Payload)返回值主要为HttpStatus + Response, 若返回204(No Content)则Response为空
  - 返回的Response值在RestTemplate存在Body字段(序列化字符串), 在前端为data(js对象--response.data)
  - jquery使用accepts指定能接收的一组数据格式，dataType指定服务器返回数据类格式，contextType指定发送请求的请求体的数据格式
  - context-Type:
    - Request Headers -- 实体头部用于指示资源的MIME类型media type
    - Response Headers -- 告诉客户端服务端实际返回的内容的内容类型
  - 后端使用`@RequestMapping(consumes = "application/json", produces = "application/json")`进行内容协商
- sql中insert通常只能插入一行，而insert select组合可以插入多行
```
# 给读权限的角色也添加写权限
insert into role_permissions
select role_id, (select id from permissions tp where tp.minor_part = 'write') as permission_id
from role_permissions
where permission_id in (select id from permissions where minor_part = 'read');
```
- 如何学习新技术：配置部署 => 关键参数 => 交互API => 使用场景 => 核心原理
- 所参考的文档：快速入门，核心 rest/api，官方 client/sdk
- 学习一门新的语言：项目结构编译，基本类型，变常量函数类接口的声明，if判断，for循环，异常处理，类型断言，内置集合，同步异步，类的继承接口实现，泛型反射，独有特性等
- 添加或者修改REST接口时，要保证兼容旧接口的功能，在新版本稳定后再将旧接口删除
- 在修改代码的过程中需要考虑到当前的版本会对之前上游下游的兼容性，老逻辑不能使用新增的字段去判断的
- 除了代码的兼容性，还有数据的兼容性，新添加的字段判断业务逻辑但是老的数据并没有该字段该如何处理呢
- 在编码过程中，不推荐直接修改原有方法满足现在的功能，而是应该复制该方法然后稍加修改并在调用的位置使用新方法
- coding过程中需要知道逻辑边界在哪，例如 最大值，最小值，零值，null，异常 等情况需要考虑清楚
- 权限的验证不仅要在前端做，也要在后端做的，防止用户使用 postman 等工具进行访问
- 如果不同的服务有相同的业务链，考虑是否可以将一个服务转到另一个减少一条业务链，从而减轻系统的复杂度
- 当需求横跨多个微服务的时候，新需求部署的先后顺序问题，以及先后部署过程中的兼容性问题
- 使用别人的 API 的时候，一定有提供文档，尤其是大公司对外的开发平台，文档都很详细，先search一下
- 在修改定时任务频率，业务计算量发生变化的时候，需要考虑硬件配置是否能够满足业务要求，如何设置replication
- 不要相信任何网络 I/O 操作，考虑是否需要在失败的时候进行重试
- 预期的错误日志打印时使用 warn 级别，非预期的错误日志打印使用 error 级别
- 不同的语言序列化的逻辑：
  - java: null 不进行序列化
  - go: 零值不进行序列化
  - js: undefined 不进行序列化
- 开发做冒烟测试的时候，一定要调通 api 与业务流程，并更新最新的 api doc
- 接口的设计原则
  - 若返回值是数组不直接返回数组类型，返回体一般为：`{data: []}`; 而且若程序中出错最好不要返回 500，返回空数组即可
  - api 正常情况下返回 200或204，检查不通过的情况下返回 400，异常情况返回 500 (coder无法预料的情况)
  - 关于状态值得返回，如果是最终状态使用大驼峰式字符串，例如 Success, Failure；如果是中间状态使用下划线或者`-`连接, 例如 in-progress

### think in SHEIN
- restyPass 等基于 http 调用的 rpc 框架，都需要指定微服务的名字即访问连接为 `http://微服务名/接口名`
- 编写 controller 接口时，不携带请求体 == 携带空 `{}` 请求体 != 携带 null 请求体
- JSON.toJSONString() 序列化时，如果对象中的属性为 null，则**不会**序列化该属性，如果对象中的属性为 ""，则会序列化该属性
- Json.defaultMapper().toJson() 序列化时，如果对象中的属性为 null，则**会**序列化该属性，结果是 null
- Spring 容器管理工具还可以将一个接口的所有实现类放到 List<T> 和 Map<String, T> 中，有些代码 List，Map 没有显示初始化逻辑
- @Builder 注解在类上，会为该类生成一个内部静态类 Builder，该类中包含了该类的所有属性，以及一个 build() 方法，会抹掉所有初始值
- 编写代码的时候所有的接口、枚举类都需要编写 javadoc 文档注释
- 定义数据库时间对象时，使用 Date/LocalDateTime，数据库中的时间字段使用 datetime 类型，会自动帮我们转换为 "yyyy-MM-dd HH:mm:ss" 格式
- 如果代码自己需要转为特定的日期格式需要使用 `DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");`
- 常见包名：controller、service、entity、dto、vo、po、mapper、config、util、constant、enums、task、consumer、producer
- 不常见包名：filter、interceptor、aspect、annotation、test、exception、handler、listener、repository、client、rpc、api、impl
- 作为 api 提供方对于字段为 List 类型的通常返回空数组[]，而不是 null，但是作为 api 调用方我们需要对 List 类型的字段进行判空
- 对于 Spring 组件通常会注入自己的实例，用来调用带有 @Cache、@Transactional 的方法，否则代理类无法生效
- 有些业务的数据有明显的包含关系，例如 省-市-区，那么在保存数据的时候子结构保存父结构的Id。区的数据表含有市的Id，市的数据表含有省的Id
- 与前端进行交互的时候 Id 类型均为 Long 类型，虽然前端传入 String 框架也会转成 Long 类型，但是还是要避免这么做
- 理论上从数据库中查到的数据均要判空，那些可能为空的一定要判空，一定不为空的我觉得可以不判空；判空时使用 if 卫语句提前结束业务逻辑
- 同一类型的数据有父子关系且需要存储在一张表中，常见的三级分类中我们可以在分类的行记录中记录自己的父类的 parentId
- controller 返回值分为四种类型：无返回值、返回对象、返回列表、返回分页数据
  ```
  Response<Void>
  Response<Obj>
  Response<Data<List<Obj>>>
  Response<Result<Obj>>
  ```
- 通常情况下对于修改操作需要在 business 业务层设置 分布式锁 + 事务
- 设计模式：工厂模式、模板模式、责任链、策略模式
- 如果功能是锦上添花的功能执行失败也无所谓的，那么需要在内部进行 try-catch 捕获异常打印日志，不要影响主业务的逻辑
  - 消息提示类的功能
  - 日志记录、上传、入库等
- add、edit 接口最好分成两个，然后请求体可以使用继承关系，edit 的请求体多一个 Long id 字段
- List 不能直接作为 rest api 的入参和出参，需要在外层包装一层，将其作为请求体、返回体的一个字段
- 事务、锁、缓存，实践中都是在方法上添加注解，因此要注解生效则需要保证方法被代理：`class.method()` 的形式调用方法
- 数据库不允许有 null 值，使用默认空字符串，json 数据格式可以为 null
- mysql 中保存数组并检索数组：使用逗号分隔然后保存字符串，并使用 `find_in_set()` 语法进行检索
- spring 常用的入参校验注解：`@NotNull`，`@NotEmpty`，`@NotBlank`
- 同一级别不要相互依赖，否则启动的时候spring无法解决循环依赖问题；controller -> business -> service -> mapper
- Mybatis-plus 生成代码后调整：1. @TableLogic 来解决逻辑删除；2. @TableField 来解决更新时间
- 主页面的展示通常是使用宽表来存，通常存在 es 中查询效率高，进入详情页面的时候才需要查数据库的详细信息
- 影响较大的feature最好加上开关，防止线上出问题
- 公司@CacheSplit 框架的注解 requestKey 与 resultKey 是一一对应的，查找缓存先找 requestKey -> 再找 resultKey -> 缓存结果
如果 requestKey 找不到 resultKey 则说明没有缓存，如果找到了 resultKey，但是 resultKey 对应的值是 null；则说明缓存结果就是null
- 使用Optional 从一个很深的对象中取出某个字段值
  ```
  Optional<Long> optional = Optional.ofNullable(a).map(A::getB).map(B::getC).map(C::getD);
  if (optional.isEmpty()) {//后续的处理逻辑}
  ```
- @Size: Map, Array, Collection, String; @Range Number, BigDecimal; @Max String, Number; @Length: String
- 分布式处理幂等的标准动作：1锁2判3更新
- 公司添加索引脚本格式
  ```
   ALTER TABLE database.table ADD UNIQUE uk_business_id(business_id);
  ```
- @Data、@Builder、@AllArgsConstructor、@NoArgsConstructor 最好同时使用, 否则序列化可能会报错
- es 异步写入 qps 很高, 但是很容易造成缓存不一致的问题. 缓存已经删除但是 es 还未更新, 此时查询回写缓存即造成缓存不一致
- kafka 异步发送方法中既有 kafka 的发送操作又有更新DB操作，则要将更新 DB 写在 callback 里
- mysql 查询时：隐式转换不走索引：类型不匹配、条件列上使用函数
- 迁移数据过程中，涉及到单查，双查(灰度)，双写，单写，平滑切换；双查，双写要保证数据一致
- TransmittableThreadLocal 用于解决 父线程上下文数据在异步线程池或子线程中传递的问题
- 同一个流程中(接口或定时任务)主任务和子任务共用线程池，会造成经典的线程死锁问题
- lastUpdateTime 加上该注解时，在更新时忽略dto的值，始终为更新数据时数据库的 now() 时间
  ```
  @TableField(jdbcType = JdbcType.TIMESTAMP, update = "now()", updateStrategy = FieldStrategy.IGNORED)
  ```
- 面向注解编程
  ```
  // 定义注解及参数
  @Target(ElementType.METHOD)              // 作用在方法上
  @Retention(RetentionPolicy.RUNTIME)    // 运行时可读取
  public @interface DistributedLimit {
    ...
  }
  // 创建切面逻辑
  @Aspect
  @Component
  public class DistributedLimitAspect {

  @Around("@annotation(distributedLimit)")    //  拦截所有带有 @DistributedLimit 注解的目标方法
  public Object around(ProceedingJoinPoint point, DistributedLimit limit) throws Throwable {}
    ...
  }
  ```
  
### think in bug
- 编写的UT在IDEA中可以跑通, 但是在maven跑不通, 多半是因为不同的模块联动修改, 但是前面的模块没有编译造成的
- 代码使用 mvn clean install 可以编译通过, IDEA找不到类, 鼠标点击能进入该类, 是因为maven 与 IDEA的仓库地址不一致
- 还有一个项目编译完成，另一个项目重新导入但仍无法感知它的修改，也是上述问题。鼠标点击进入的类与实际类maven中的不一致，也是上述问题，记得删除之前编译好的文件夹
- 在项目编译完成之后其他项目无法感知到还有一个原因可能是你项目之间的版本号不一致造成的
- 多线程调试时，Junit在主线程运行结束后即退出JVM，子线程无法继续执行；在main函数执行结束也会退出，子线程无法执行
- mvn中使用jacoco统计代码覆盖率产生合成属性, 所以在使用反射时会产生问题, 单元测试可能在IDEA里能通过, 在mvn test中就通不过
- TreeSet和TreeMap是用Comparator和Comparable来去重且确定存放位置, 因此能比较的属性一定具有唯一性和可比较性
- 上述问题可以使用PriorityQueue来解决, PriorityQueue不会去重且只保证最值内部不排序, 需要手动进行操作boolean/continue来去重
- 在debug过程中遇见一些莫名其妙的bug的时候记得删除target重新编译
- getResource()方法在获取文件路径时URI不能识别`@`, `空格`, `中文`等特殊字符会被转义, mvn运行时会转义
- 当api接口不通的时候：1. client编写url地址不对，2. client授权验证未通过，3. client请求格式错误
- 查找故障的时候，第一资料就是log，第二资料是code，需要结合一起看，否则log如果打印不好容易误导别人
- 类型相同，名称类似的参数很容易传错，例如 page_size 与 page_num，定位问题的时候需要格外注意
- 同一个队列，多个集群在消费，代码却不一致可能会造成脏数据
- 当故障无法复现的时候，但是代码却是一致的，需要检查一下 apollo 与 cache 的环境是否一致
- 最好不要将枚举值直接存库，因为在分页的时候如果发现数据库中的枚举值在代码中不存在则会报错
- Spring通过动态代理来实现AOP，自调用切面不会生效；例如@Cache、@DistributedLock、@Transactional
- 分页的时候如果 orderby 的字段不唯一则有可能会跳页，一条数据有时候在第一页有时候在第二页；例如根据 lastUpdateTime 分页
- hase的查询如果查不到会返回一个空对象，hbase传两个相同的key则会返回两个相同的对象
- 定位问题时，查完所有的数据源均没问题，但是数据就是不太对，这个时候就要怀疑缓存。写入、删除、更新。以及他们之间的顺序

#### 关于文件路径
- Class.getResource(String path)与 <br/>
  Class.getResourceAsStream(String path) 
    path不以'/'开头时，默认是从此类所在的包下取资源；<br/>
    path以'/'开头时，则是从项目的ClassPath根下获取资源。在这里'/'表示ClassPath的根目录
- Class.getClassLoader().getResource(String path)与 <br/>
  Class.getClassLoader().getResourceAsStream(String path) <br/>
    默认则是从ClassPath根下获取，path不能以'/'开头，最终是由ClassLoader获取资源。<br/>
    实际上 class.getResource("/") == class.getClassLoader().getResource("")
- IDEA在调试过程中可以使用第一个和第二个拷贝路径，写文件时如果没有该文件则会创建文件
- 通常在开发过程中会碰到读取配置文件的问题，一般有上述两种方式进行读取，实际上打成jar包之后只能用流方式读取
- Linux、Windows中无法直接访问未经解压的文件，所以就会找不到文件，jar包本质上就是一个压缩包
- 读取jar里面的文件，我们只能用流去读取，也就是getResourceAsStream(String path)方法读取
- 关于配置文件的读取，需要挂在jar包外面，jar包内只能读不能写，所以滚去用Spring框架吧，少造轮子了
- ClassPath的根目录就是存放classes的那个文件夹的路径, 工程中java和resources文件的内容就在该根目录下, resources下的文件就从"/"开始写相对路径
  - IDEA中class.getResource("/"): /D:/Project/SpringBoot-Demo/target/classes/
  - IDEA中class.getResource(""): /D:/Project/SpringBoot-Demo/target/classes/com/example/demo/
  - jar包中class.getResource("/"): file:/D:/Project/SpringBoot-Demo/target/demo-0.0.1-SNAPSHOT.jar!/BOOT-INF/classes!/
  - jar包中class.getResource(""): file:/D:/Project/SpringBoot-Demo/target/demo-0.0.1-SNAPSHOT.jar!/BOOT-INF/classes!/com/example/demo/

#### ThreadLocal核心
- ThreadLocal为共享数据创建副本, 分别属于各自线程可以独立修改, 数据为基本数据类型和字符串类型
- ThreadLocal无法解决引用数据类型的更新问题, 引用数据类型如果想创建副本只能通过深拷贝的形式来处理

#### Objects中常用的方法
- Objects.equals()
- Objects.requireNonNull()

#### 数组的复制方法
- System.arraycopy()
- xxx.clone()
- Arrays.copyOf()
- 以上均为浅拷贝

#### forEach中的操作的外部变量必须是final的
- forEach在此处使用的是lambda 表达式, 可以简单的把lambda表达式理解为匿名内部类(lambda 表达式不仅仅是内部类这么简单)。而匿名内部类的变量必须用final修饰。
- 类的生命周期比方法的生命周期长, 同理匿名类的生命周期比方法的生命周期长。方法运行完了, 变量释放了, 但是匿名内部类还在。这时就要求匿名内部类引用的变量必须还在, 这样才能保持数据的一致性。
`https://blog.csdn.net/weixin_28753647/article/details/114517624`

#### jvm默认内存分配
- 堆内存的分配：
JVM初始分配的内存由-Xms指定，默认是物理内存的1/64；JVM最大分配的内存由-Xmx指定，默认是物理内存的1/4。默认空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制；空余堆内存大于70%时，JVM会减少堆直到-Xms的最小限制。因此服务器一般设置-Xms、-Xmx相等以避免在每次GC后调整堆的大小。
- 非堆内存分配：
JVM使用-XX:PermSize设置非堆内存初始值，默认是物理内存的1/64；由XX:MaxPermSize设置最大非堆内存的大小，默认是物理内存的1/4。</br>
`https://www.cnblogs.com/jack204/archive/2012/07/02/2572932.html`
- jvm诊断思路(jps): 先查看线程堆栈(jstack)信息, 再查看内存占用(jmap)信息, 查看垃圾回收情况(jstat)，最后看看启动参数(jinfo)
- 如果配置了新生代最大堆内存, 则老年代的最大内存 = 最大堆内存 - 新生代最大堆内存; 等于jmap中的OldSize与Old Generation
- 如果没有配置新生代最大堆内存, 按照默认比例进行划分; jmap中的OldSize为老年代初始堆内存, Old Generation为老年代最大堆内存
- Old Generation为老年代最大堆内存利用率达到100%的时候, 堆内存溢出; 通常这时候新生代的内存还有空间
- Major GC和Full GC的区别是什么？触发条件呢`https://www.zhihu.com/question/41922036`
![JVM内存占用](https://github.com/CyS2020/Notebook/blob/master/images/JVM%E5%86%85%E5%AD%98%E5%8D%A0%E7%94%A8.png)

#### PPT大师
1. 多花点时间寻找合适的模板，无需花里胡哨，简约大方即可
2. 多搞点图片，示意图，流程图，效果图，表格等，字不如表，表不如图
3. 排版尽量紧凑，不需要为了凑页数搞得每一页没什么信息
4. 说明性的文字大小尽可能的小12左右即可，太大了像作文，满屏文字
5. 写东西要注意条理性和逻辑性，突出重点，分条分面

#### 优化思路
1. 优化算法本身复杂度，最好能降低复杂度
2. 试试串行改成并行，利用多线程的技术提升性能
3. 优化内存，避免出现占用内存较大的类，拖累系统性能
4. JVM看看能不能进行调优，这部分可能受益不大
