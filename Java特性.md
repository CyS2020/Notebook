### Java17新特性

#### Switch语句的更新
- 无需为每一个 case 编写 break 关键字了，省略即可
- switch 可以拥有返回值，使用 yield 关键字返回
- 多个匹配的时候可以使用 `case val1, val2` 来编写
- 不仅支持数值、字符串常量的匹配，对类型可可以匹配

#### 字符串拼接
- 使用 `"""` 三引号来表示不转义字符串(多行文本)，类似于golang中的反引号
- `\`: 置于行尾，用来将两行连接为一行
- `\s`: 单个空白字符串
- 关于字符串变量值，仍然需要 `%s` 占位符，然后通过 `String.format()` 来处理

#### 关于 instanceof
- `o instanceof Integer i`: 判断完成之后直接将 o 强转为 Integer 类型的 i 进行操作

#### 密封类(限制)
- `sealed`、 `permits` 用在接口或者父类来限制子类的类型
- 父类与子类必须放在同一包里面；子类必须直接继承密封类
- 子类必须添加 final(不允许被继承)、non-sealed(允许被继承)

#### Record类 -- 类似lombok的属性只读对象
- 创建类不在使用 `class` 关键字，而是用 `record` 关键字

#### ArrayList源码
- Object.clone()方法为浅拷贝
- ArrayList中的modCount记录数组修改的次数, 用于并发修改检查(ConcurrentModificationException)
- ArrayList中的subList()返回的是视图, 增删会在源数组上进行修改
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
- HashMap中由于负载因子默认为0.75, 用长度16的数组存12个元素, 绰绰有余, 而且随着size增大threshold也在增大, 因此链表大小超过8的概率很低(百万分之一)。

#### TreeMap源码
- TreeMap中使用红黑树数据结构, 比较的时候优先使用传入的Comparator, 为null时传入Comparable
- TreeMap中并没有使用hashCode和equals方法判断相等, 而是使用Comparator或者Comparable返回值为0判断元素唯一性
- 所以插入TreeMap中的元素一定具有可比性，要么传入的元素实现了Comparable接口，要么出入合适的比较器Comparator
- TreeMap的哈希一致性应用, TreeMap<hash, list>中保存hash了列表, 元素放入第一个大于该元素hash值的list列表, 使用tailMap()方法
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
- 通过复写removeEldestEntry()实现自定义LRU的缓存策略, 最近最少使用; 限制容器长度实现最近(复写size() > limit), 通过afterNodeAccess()来触发

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