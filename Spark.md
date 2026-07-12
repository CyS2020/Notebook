# Spark 快速入门

官方文档：https://spark.apache.ac.cn/docs/latest/


### 集群模式
- 主程序 driver
- 工作节点 executor

### 读取数据
- 创建会话 SparkSession
- 确定数据源
- 读取数据 spark.read()

### 访问集群
- 创建 SparkContext

### 分布式数据集 (RDD)
- 并行集合
  - 并行集合是通过在驱动程序的现有 Collection 上调用 JavaSparkContext 的 parallelize 方法创建的
  - 并行集合的一个重要参数是将数据集切分成的分区数量。例如 sc.parallelize(data, 10)
- 外部数据集
  - 文件系统(HDFS、S3、本地)
  - 数据库(JDBC、NoSQL)
  - 消息队列(Kafka、RabbitMQ)
  - 数据湖(Hive、Paimon)

### 数据集转换
- 转换 (transformations)
  - 转换是惰性的
- 动作 (actions)
  - 动作是立即执行的
  - 每个转换后的 RDD 在您每次对其运行动作时都可能被重新计算
  - 可以使用 persist(或 cache)方法在内存中持久化 RDD

### 向Spark传递函数
- 在您自己的类中实现 Function 接口
- 使用 Lambda 表达式 简洁地定义实现
- Spark 不定义或保证对闭包外部引用的对象进行修改的行为

### Shuffle 操作
- Shuffle 是 Spark 用于重新分发数据的机制，以便它在分区之间以不同的方式进行分组
- 这通常涉及跨执行器和机器复制数据，使 shuffle 成为一个复杂且昂贵的操作
- 导致 shuffle 的操作包括
  - repartition 和 coalesce
  - ByKey 操作(除了计数之外)，如 groupByKey 和 reduceByKey
  - join 操作，如 cogroup 和 join。