### Code规约
- 命名规范(包名.类名.函数名.变量名)、函数40行类400行、严格使用访问限制符(private、protected、public)、代码格式化类引入优化
- 捕获异常需要打印描述信息还需要打印堆栈信息：logger.error(e.getMessage, e);
- 一般情况下从map中get某个值都需要进行判空处理, 或者使用getOrDefault()方法
- Boolean值进行逻辑判断时候：Boolean.FALSE.equals(), 基本数据类型boolean前面使用！即可
- 回调函数就是编写的时候不确定的函数行为, 作为参数在调用的时候显示传参, 调用行为执行相应的操作, 回调就是个高大上的词罢了
- String属于不可变类型, 字符串连接的时候使用StringBuilder.append().append...append();
- 某个类中, 需要其他组合类：声明时即创建(独立功能), 构造函数中赋值(必要信息), 方法中初始化(加工信息)
- 需要容器时, 只定义声明, 然后在一个函数中创建并填充, 然后将该容器返回给刚才的声明。例如：List<String> list = getList();
- javaBean:1.所有属性为private; 2.提供默认的构造方法; 3.提供getter和setter; 4.实现serializable接口
- 序列化一般都是深拷贝先去调用类的无参构造方法然后逐个地去调用set方法去填充实例的字段值所以无参构造方法是一定要显式地写在类里避免出现反序列化失败的情况
- 类中成员变量再方法使用的时候, 先进行局部变量赋值, 然后对局部变量进行操作
- 对于一些边界情况, 使用if语句再复杂逻辑开始之前进行单独处理, 避免所有情况全部进入复杂逻辑体
- 在代码中可以多加非null判断：if((xxx = yyy.get()) != null)会增强代码的鲁棒性
- 集合在初始化时机: 在声明的时候就初始化(很大程度避免空指针), 构造函数中构造一些创建类必要的属性, 不要做new集合这么无聊的事情
- 熟练使用Stream流和collect()方法，常见的toList、toSet、toMap、groupingBy; 例: orderList.stream().collect(Collectors.groupingBy(Order::getOrderNo));
- 数组转集合使用Stream.of().collect(xxx); 集合转数组xxx.toArray()

### 重构代码
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
- 数组操作善于使用流操作Integer[] integers = Arrays.stream(value.split("\\.")).map(Integer::parseInt).toArray(Integer[]::new)
- 使用流的过程中不要在显示的在流的操作中写复杂的操作，超过两行提取成函数
- 使用java8中Map的新增方法来优化代码：putIfAbsent、computeIfAbsent(及时加载)、getOrDefault、computeIfPresent方法

### 思考
- 传入的参数是函数式接口时，编写的只是功能，并不带有具体的变量，在调用apply等方法时才传入具体的变量：形参非变量，入参可不用
- 同步与异步--消息通知的机制--自己等还是别人通知，阻塞与非阻塞--程序在等待调用结果（消息，返回值）时的状态--等待过程中，等待者的操作
- 上述概念组合起来：同步阻塞、同步非阻塞、异步阻塞(一般没有这种情况，直接用同步阻塞就好了)、异步非阻塞
- 初始化：从父到子，从静态变量，静态代码块，变量，代码块，构造函数；变量的特殊情况Set set = new TreeSet<>(comparator)，此时comparator可以为空，在调用comparator赋值就可以

### 莫名Bug
- mvn中使用jacoco统计代码覆盖率产生合成属性, 所以在使用反射时会产生问题, 单元测试可能在IDEA里能通过, 在mvn test中就通不过
- TreeSet和TreeMap是用Comparator和Comparable来去重且确定存放位置, 因此能比较的属性一定具有唯一性和可比较性
- 上述问题可以使用PriorityQueue来解决, PriorityQueue不会去重且只保证最值内部不排序, 需要手动进行操作boolean/continue来去重
- 在debug过程中遇见一些莫名其妙的bug的时候记得删除target重新编译
- 代码中读取文件时候使用Class.getResourceAsStream(/../..)方法, 避免在IDEA中能找到路径而在maven打包后找不到文件路径


### 刷题心得
- 区间都是遵循左闭又开原则
- 长度是可以在索引的基础上加减的index ± len
- 判断没有小数：expr % 1 == 0

#### 语法细节
- java的接口是可以多继承的, 一个接口可以继承多个接口
- 泛型中的<? extend Class>不仅适用于类的继承，也适用于接口的实现

#### ArrayList源码
- Object.clone()方法为浅拷贝
- ArrayList中的modCount记录数组修改的次数, 用于并发修改检查(ConcurrentModificationException)
- ArrayList中的SubList返回的是视图, 增删会在源数组上进行修改
- ArrayList中的spliterator是在stream中进行并发遍历时用到的
- ArrayList中删除时除了使用iterator(并发时需加锁)还可以使用removeIf(Predicate<? super E> filter)
- ArrayList中普通for循环、增强for循环中执行add和remove操作会引发什么问题？
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
- 删除指定位置的元素的时候需要先执行下潜操作, 如果元素位置未发生改变, 则执行上浮操作(该操作不可省略)
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
