### Hbase大数据

- 四维坐标系统：行键、列族、列限定符、时间版本
- 五个基本命令访问Hbase中的数据：Get、Put、Delete、Scan、Increment
- 一个列族可以有很多 HFile，但是一个 HFile 不能存储多个列族
- 一行中列族数据不一定存放在一个 HFile 里，一行中的列族数据需要物理存放在一起
- Hbase.client.scanner.caching 来设置客户端扫描时 rpc 返回的行数
- Hbase 中的过滤器可以应用到 行键、列限定符或者数据值
- RegionServer本质上是 HDFS (Hadoop 分布式文件系统)的客户端，托管多个 region
- 一个Region 由多个 HFile 组成，每个Region包含多个列族
- Hadoop MapReduce 分布式计算框架，使用 TableMapper 和 TableReducer 实现

- Hbase 的宽表设计与高表设计、行键列与限定符的MD5
- Hbase 表的设计唯一重要的是行键
- region 基于行键为一个区间的行提供服务，并负责区间的每一行；
- HFile 在硬盘上存储有序的行

#### 为写优化
- 散列：散列后相加 MD5(xxx) + MD5(xxx)
- salting: 散列码取模+行键，分散存储

#### 为读优化
- 前缀：使用ID作为前缀，然后拼接其他索引
- 拼接：关系类型放置在两个 id 中间

####  性能优化
- 过滤器：行过滤器、前缀过滤器、限定符过滤器、值过滤器、时间戳过滤器
- 单列族设计：缓存的数据比较多；磁盘加载数据多浪费IO；查询整行时节省网络IO
- 多列族设计：缓存的数据比较少；磁盘加载数据少节省IO；查询整行时浪费网络IO