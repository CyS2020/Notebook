### Code规约
- 命名规范(包名.类名.函数名.变量名)、函数40行类400行、严格使用访问限制符(private、protected、public)、代码格式化类引入优化
- 捕获异常需要打印描述信息还需要打印堆栈信息：logger.error(e.getMessage, e);
- 一般情况下从map中get某个值都需要进行判空处理, 或者使用getOrDefault()方法; map并行流不要使用keySet,得到value时需要get操作，使用entrySet().parallelStream().forEach()
- java8中的流操作reduce, 第一个参数为上次调用返回的值, 第二个参数为当前遍历到的值, 即a, b两个参数一个为旧值一个为新值
- Boolean值进行逻辑判断时候：Boolean.FALSE.equals(), 基本数据类型boolean前面使用！即可
- 回调函数就是编写的时候不确定的函数行为, 作为参数在调用的时候显示传参, 调用行为执行相应的操作, 回调就是个高大上的词罢了
- String属于不可变类型, 字符串连接的时候使用StringBuilder.append().append...append(); String还有join方法内部就是使用的StringBuilder
- 某个类中, 需要其他组合类：声明时即创建(独立功能), 构造函数中赋值(必要信息), 方法中初始化(加工信息)
- 需要容器时, 只定义声明, 然后在一个函数中创建并填充, 然后将该容器返回给刚才的声明。例如：List<String> list = getList();
- javaBean:1.所有属性为private; 2.提供默认的构造方法; 3.提供getter和setter; 4.实现serializable接口
- 序列化一般都是深拷贝先去调用类的无参构造方法然后逐个地去调用set方法去填充实例的字段值所以无参构造方法是一定要显式地写在类里避免出现反序列化失败的情况
- 类中成员变量再方法使用的时候, 先进行局部变量赋值, 然后对局部变量进行操作
- 对于一些边界情况, 使用if语句再复杂逻辑开始之前进行单独处理, 避免所有情况全部进入复杂逻辑体
- 在代码中可以多加非null判断：if((xxx = yyy.get()) != null)会增强代码的鲁棒性；
- 在if语句中只对确定性的条件进行判断哪怕代码比较长，否则将来扩展会出问题；同样else后尽量加上if条件判断多一重保障
- 集合在初始化时机: 在声明的时候就初始化(**避免空指针**), 构造函数中构造一些创建类必要的属性, 不要做new集合这么无聊的事情
- 熟练使用Stream流和collect()方法，常见的toList、toSet、toMap、groupingBy; 例: orderList.stream().collect(Collectors.groupingBy(Order::getOrderNo)); list与map互相转换
- 数组转集合使用Stream.of().collect(xxx); 集合转数组xxx.toArray()
- 常见集合(List, Set, Map)比较相等时, 比较的是集合内的元素, 若元素相等则集合相等
- 浮点数在比较的时候不能用`>=,<=,==,!=` 需要一个eps来处理精度问题，等于与不等于特么的一定要用Math.abs(), `>, <`可以直接比较的(16位精度内)
- 浮点数使用Double.compareTo()与BigDecimal.compareTo()进行比较
- split(" ")在分割的时候如果边界处能匹配上, 如果是前边界会有一个null, 结果数组中的第一个元素为null, 后边界没有这个问题
                                                                                          
### 重构代码
- Collection接口toArray()默认返回的是Object类型的数组，且无法强转，toArray(T[] a)该接口返回的是指定类型的数组，例如传入new String[0]入参返回String数组;
- 被强制类型转换的实例需要是强制类型转换类型的类或者其子类。String是Object的子类，但是String[]并不是Object[]的子类，所以会强转失败
- intValue()是Number类的方法， Number是一个抽象类。Java中所有的数值类都继承它。也就是说，不单是Integer有intValue方法，Double，Long等都有此方法。Number对于6中数值类型的都有对应的抽象方法；在强转过程中 浮点数转整型会有精度丢失, 表示范围大的数值转范围小的类型高位丢失
浮点数转整型会有精度丢失, 数值类型往下强转(long -> int)会丢失高位数据
![基本数据类型转换](https://github.com/CyS2020/Notebook/blob/master/images/%E5%9F%BA%E6%9C%AC%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B%E8%BD%AC%E6%8D%A2.png?raw=true)
- 枚举类中的valueOf()、name()、toString()常用方法与枚举类型的字符串有关，与括号内的属性无关，toString()可以被重写，同时记得区分枚举类的静态方法与实例方法
- 三个潜在的取值及以上, 使用switch来控制代码执行流程, 变量-取值-行为-break
- Jdk或其他框架会提供很多工具类：ObjectUtils、BeanUtils、StringUtils、CollectionUtils
- 使用IDEA查找get,set的调用地方, 然后一一修改, 并保证单元测试通过
- 在修改逻辑的时候, 越往里挖需要改动的地方则越少, 所以尽量在深层次源头地方进行修改
- 变量命名接口命名以I开头, 实现类以Impl结尾, 抽象类以Abstract开头, 枚举类以Enum结尾
- 类中属性为容器的时候直接new然后设置为final即可, 如果属性只声明每次用都重新new消耗内存
- 代码中根据枚举值返回枚举类型可以通过设置Map返回, 也可以使用for循环来返回(推荐使用)
- 代码中不要出现硬编码, 改变硬编码的方式有两种如果是有分类的可以使用枚举类, 或者创建常量类存贮
- 常量格式public static final 类型 大写_下划线; 枚举类存放的信息多一些
- lambda + stream形式搭配时, 将forEach/map/filter复杂的化抽为函数, 业务复杂不建议使用这种搭配
- 使用if/else if, 里面不要偷懒, 尽量不用else, else的行为太宽泛了, 同理少用!equals()这个方法; 只对确定性的条件判断
- 将某个方法改造成比较器可以使用Comparator.comparing(xxx::xxx)方法
- 数组操作善于使用流操作int[] arr = Arrays.stream(line.split(" ")).mapToInt(Integer::parseInt).toArray();
                        String[] strs = Arrays.stream(arr).mapToObj(String::valueOf).toArray(String[]::new);
- 使用流的过程中不要在显示的在流的操作中写复杂的操作，超过两行提取成函数
- 使用java8中Map的新增方法来优化代码：putIfAbsent、computeIfAbsent(及时加载)、getOrDefault、computeIfPresent方法
- 使用java8中Map的方法：put()、putIfAbsent()--返回旧值、compute()、computeIfAbsent()--返回新值
- jacoco在覆盖率红色表示未覆盖，黄色表示部分覆盖(if不知一个判断), 绿色表示全部覆盖; 异常, if, switch算分支; 不要使用@Data注解它会将每个属性都写入equals()与hashCode()产生大量分支而拉低覆盖率
- 先赋值, 再判空, 再使用; kw检查中有时候会考虑多线程的情况, 即使你只是串行执行的场景
  ```
  if(obj.getField() != null){           f = obj.getField()
      f = obj.getField();        =>     if(f != null){
  }                                     }  
  ```
### 思考
- java中有四个生成随机数的类: Random、ThreadLocalRandom、SecureRandom、Math.random()
- Arrays.sort()无法自定义比较基本数据类型，只能使用默认的升序，可以说是非常垃圾了，对象的话可以传入比较器
- Java与其他语言编写的程序进行tcp/ip socket通讯时，通讯内容一般都转换成byte数组型，new String(byte[] bytes) <---> getBytes()
- 按照流是否直接与特定的地方 (如磁盘、内存、设备等) 相连，分为节点流和处理流两类。节点流可以从或向一个特定的地方（节点）读写数据；
处理流是对一个已存在的流的连接和封装，通过所封装的流的功能调用实现数据读写。处理流的构造方法总是以一个其他的流对象做参数。一个流对象经过其他流的多次包装，称为流的链接。
- 双向Dijkstra终止条件：从头出发最小值+从尾出发最小值≥目前已知最短路径
- ksp算法：最短路径列表中的边需要排除掉，计算新的路径
- java只有值传递, 没有引用传递, 即使是对象的传递也只是复制一份引用的地址, 传入函数中, 函数中对引用指向修改, 调用方感知不到
- 单例特征：两静态一私有, 静态变量+静态获取单例方法+私有构造函数
- 重写比较器：< -1为升序, < +1为降序; 使用比较器：默认比较为升序, 判优遵循比较器, 方向一致 <0 更优, 方向相反 >0 更优
- 传入的参数是函数式接口时，编写的只是功能，并不带有具体的变量，在调用apply等方法时才传入具体的变量：形参非变量，入参可不用
  入参与返回值的类型均是确定的, 直接编写方法体内的逻辑就好了, 就是简化了函数的定义
- 同步与异步--消息通知的机制--自己等还是别人通知，阻塞与非阻塞--程序在等待调用结果（消息，返回值）时的状态--等待过程中，等待者的操作
- 上述概念组合起来：同步阻塞、同步非阻塞、异步阻塞(一般没有这种情况，直接用同步阻塞就好了)、异步非阻塞
- 初始化：从父到子，从静态变量，静态代码块，变量，代码块，构造函数；变量的特殊情况Set set = new TreeSet<>(comparator)，此时comparator可以为空，在调用comparator之前赋值就可以, 但是不要这么写
- double类型的比较，如果是大小比较则用“>”和“<”, 相等比较千万!!!别用==, 如果是相等则用Math.abs() < 1e-5, 另外比较器中返回int值-1、0、1
- 静态方法不能访问类中定义的泛型，如果静态方法中数据类型不确定，可以在方法上定义泛型。即下例中<T>就是声名一个泛型T，之后两个T是在使用泛型T。
```
    public static <T> ResponseBean<T> error(String msg,  T data) {
        return new ResponseBean<>(200, data, msg);
    }
```
### 莫名Bug
- 代码使用 mvn clean install 可以编译通过, IDEA找不到类, 鼠标点击能进入该类, 是因为maven 与 IDEA的仓库地址不一致
- 还有一个项目编译完成，另一个项目重新导入但仍无法感知它的修改，也是上述问题。鼠标点击进入的类与实际类maven中的不一致，也是上述问题，记得删除之前编译好的文件夹
- 在项目编译完成之后其他项目无法感知到还有一个原因可能是你项目之间的版本号不一致造成的
- 多线程调试时，Junit在主线程运行结束后即退出JVM，子线程无法继续执行，在main函数不会退出，同时main函数是非守护线程
- mvn中使用jacoco统计代码覆盖率产生合成属性, 所以在使用反射时会产生问题, 单元测试可能在IDEA里能通过, 在mvn test中就通不过
- TreeSet和TreeMap是用Comparator和Comparable来去重且确定存放位置, 因此能比较的属性一定具有唯一性和可比较性
- 上述问题可以使用PriorityQueue来解决, PriorityQueue不会去重且只保证最值内部不排序, 需要手动进行操作boolean/continue来去重
- 在debug过程中遇见一些莫名其妙的bug的时候记得删除target重新编译
- getResource()方法在获取文件路径时URI不能识别`@`, `空格`, `中文`等特殊字符会被转义, mvn运行时会转义

### 关于文件路径
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
- 关于配置文件的读取，需要挂在jar包外面，jar包内只能读不能写，所以滚去用Spring框架吧，少造轮子了关键你也造不出来
- ClassPath的根目录就是存放classes的那个文件夹的路径, resources目录下文件就在classes目录下, resources下的文件就从"/"开始写相对路径
  - IDEA中class.getResource("/"): /D:/Project/SpringBoot-Demo/target/classes/
  - IDEA中class.getResource(""): /D:/Project/SpringBoot-Demo/target/classes/com/example/demo/
  - jar包中class.getResource("/"): file:/D:/Project/SpringBoot-Demo/target/demo-0.0.1-SNAPSHOT.jar!/BOOT-INF/classes!/
  - jar包中class.getResource(""): file:/D:/Project/SpringBoot-Demo/target/demo-0.0.1-SNAPSHOT.jar!/BOOT-INF/classes!/com/example/demo/

#### ArrayList源码
- Object.clone()方法为浅拷贝
- ArrayList中的modCount记录数组修改的次数, 用于并发修改检查(ConcurrentModificationException)
- ArrayList中的SubList返回的是视图, 增删会在源数组上进行修改
- ArrayList中的spliterator是在stream中进行并发遍历时用到的
- ArrayList中删除时除了使用iterator(并发时需加锁)还可以使用removeIf(Predicate<? super E> filter)
- ArrayList中普通for循环、增强for循环中执行add和remove操作会引发什么问题？一致性修改问题，fori会数据错乱，forT会出现异常，使用removeIf
- ArrayList中的排序算法是调用的是Arrays.sort()而LinkedList中没有排序方法

#### LinkedList源码
- LinkedList中增删改查的逻辑都是由以下几个方法实现的：linkFirst、linkLast、unlinkFirst、unlinkLast、unlink、linkBefore;
- LinkedList中toArray方法中用到Array.newInstance(a.getClass.getComponentType(), size)创建泛型数组, getComponentType返回数组中元素的Class对象
- LinkedList实现了Deque接口,里面有个descendingIterator逆向迭代器, 虽然不知道使用场景第一次见到就记录一下吧
- LinkedList和ArrayList中有关Collection接口作为入参, 用的最多的方法是c.contains()用于批量删除和c.toArray()用于批量添加

#### HashMap源码
- HashMap中tableSizeFor方法中的位运算来寻找大于该数的最小2的整次幂：1 -> 1; 2 -> 2; 3 -> 4; 5 -> 8; 11 -> 16; 33 -> 64等
- HashMap中通过 (n - 1) & hash 来确定key在table数组中的位置(哈希槽)
- HashMap中通过 (e.hash & oldCap) 来确定key在容器中扩张中的槽位是否变动, 如果结果为0则无需变动, 如果为1则：原位置 + oldCap
- HashMap中resize()函数进行扩容操作, 对于size < 6的红黑树进行untreeify操作, resize()只负责数组扩容和链化
- HashMap中put操作时, 如果槽位中的某个链表长度大于8, 则判断table容量大于等于64则进行树化, 否则只进行扩容
- HashMap中的扩容时机：1. size > threshold的时候 2. 槽位中的链表长度大于8而table容量小于64
- HashMap中由于负载因子默认为0.75, 用长度16的数组存12个元素, 绰绰有余, 而且随着size增大threshold也在增大, 因此链表大小超过8的概率很低。

#### TreeMap源码
- TreeMap中使用红黑树数据结构, 比较的时候优先使用传入的Comparator, 为null时传入Comparable
- TreeMap中并没有使用hashCode和equals方法判断相等, 而是使用Comparator或者Comparable返回值为0判断元素唯一性
- 所以插入TreeMap中的元素一定具有可比性，要么传入的元素实现了Comparable接口，要么出入合适的比较器Comparator
- TreeMap的哈希一致性应用, TreeMap<hash, list>中保存hash了列表, 放入的元素放入第一个大于该元素hash值的第一个hash列表
- 哈希一致性的容错性和扩展性：只对受影响的数据进行转移而不影响其它的数据; 通过虚拟节点解决数据倾斜的问题
- 哈希一致性：在使用一致哈希算法后，哈希表槽位数（大小）的改变平均只需要对 K/n 个关键字重新映射，其中K是关键字的数量， 
n是槽位数量。然而在传统的哈希表中，添加或删除一个槽位的几乎需要对所有关键字进行重新映射。一致性体现在，当服务器数量变化时，
影响到的，数据—服务器，对应关系变化不是全量的。（对于绝大多数数据来说，是前后一致的）
- 一致性哈希算法应该满足几个条件：平衡性、单调性、分散性、负载

#### LinkedHashMap源码
- LinkedHashMap中的获取槽位与HashMap相同, 先重新求的hash值 h = key.hashCode() ^ h >>> 16舍弃低位, 减少哈希碰撞
- LinkedHashMap在put()中创建节点的时候newNode或者newTreeNode的时候通过即linkNodeLast()来插入队尾, 维护双向链表
- LinkedHashMap中核心参数accessOrder：该字段用来控制是否需要将被访问的节点移动到双向链表的末尾。默认值为 false
- LinkedHashMap中afterNodeAccess()在访问的时候调用即get()方法, 或者put()已经存在的key, 会将已经访问过的元素放到双向链表的队尾
- LinkedHashMap中afterNodeRemoval()在移除节点的时候调用remove()方法, 在双向链表中移除该节点
- LinkedHashMap中afterNodeInsertion()在插入的时候调用即put()时, 会通过条件判断删除某些节点(默认不会删除), 条件为true时删除首节点
- 通过复写removeEldestEntry()实现自定义LRU的缓存策略, 最近最少使用; 限制容器长度实现最近(复写size() > limit), 通过afterNodeAccess()来实现最少使用

#### PriorityQueue源码
- 内部使用数组实现小根堆这种数据结构, 容器默认初始值为11, 元素个数大于64进行1.5倍扩容, 否则进行2倍+2扩容
- 执行offer()操作是添加到队尾, 然后上浮到合适的位置, poll()将最后一个元素放在队头, 然后下潜到合适的位置
- 删除指定位置的元素的时候需要先执行下潜操作, 如果元素位置未发生改变, 则执行上浮操作(该操作不可省略, 更新同理)
- 如下图所示当左子树右子树层数一致时, 6为最后一个元素; 此时如果要删除13的时候, 用将6覆盖掉13并且6需要执行上浮操作
```
            1
         /      \
      12          2
    /   \        / \
  13     14     3   4
 /  \   /  \   / \
15  16 17  18 5   6
```

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

### 常见面试题
#### 垃圾回收
1. 排定对象是否为垃圾
- 引用计数算法
- 可达性分析

2. 垃圾回收算法
- 标记-清除算法
- 复制算法
- 标记-整理算法
- 分代收集算法：Eden + from + to + old

#### jdk, jre, jvm
- JDK：Java Development Kit  Java开发工具包，核心类库
- JRE：Java runtime environment  Java运行环境，运行类库
- JVM：Java Virtual Machine  Java虚拟机

#### 类加载机制
1. 加载
2. 链接
  - 验证：确保加载的类信息符合JVM规范，没有安全方面的问题
  - 准备：为static变量分配内存，设置默认初始值
  - 解析：虚拟机常量池内的符号引用替换为直接引用
3. 初始化
- ClassLoader
1. BootStrapClassLoader: c++编写，加载核心类库java.*
2. ExtClassLoader: Java编写，加载扩展库javax.*
3. AppClassLoader: Java编写，加载程序所在目录
4. 自定义ClassLoader: Java编写，定制化加载，findClass, defineClass
- 双亲委派机制
1. 自底向上检查类是否已经加载过，自顶向下尝试加载类
2. 避免多份同样字节码的加载

#### jvm默认内存分配
- 堆内存的分配：
JVM初始分配的内存由-Xms指定，默认是物理内存的1/64；JVM最大分配的内存由-Xmx指定，默认是物理内存的1/4。默认空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制；空余堆内存大于70%时，JVM会减少堆直到-Xms的最小限制。因此服务器一般设置-Xms、-Xmx相等以避免在每次GC后调整堆的大小。
- 非堆内存分配：
JVM使用-XX:PermSize设置非堆内存初始值，默认是物理内存的1/64；由XX:MaxPermSize设置最大非堆内存的大小，默认是物理内存的1/4。</br>
`https://www.cnblogs.com/jack204/archive/2012/07/02/2572932.html`
- jvm诊断思路: 先查看线程堆栈信息, 在查看内存占用信息, 最后看看启动参数
- 如果配置了新生代最大堆内存, 则老年代的最大内存 = 最大堆内存 - 新生代最大堆内存; 等于jmap中的OldSize与Old Generation
- 如果没有配置新生代最大堆内存, 按照默认比例进行划分; jmap中的OldSize为老年代初始堆内存, Old Generation为老年代最大堆内存
- Old Generation为老年代最大堆内存利用率达到100%的时候, 堆内存溢出; 通常这时候新生代的内存还有空间
- Major GC和Full GC的区别是什么？触发条件呢`https://www.zhihu.com/question/41922036`
### PPT大师
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
