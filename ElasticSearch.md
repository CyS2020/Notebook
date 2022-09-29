### ElasticSearch全文索引
- `https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html`

#### ElasticSearch搜索和数据分析引擎
- 基本概念
  - Index索引: 万物皆索引, 类似于mysql中的Database
  - Type类型: 类似于mysql中的Table, 高版本已删除Type, 默认`_doc`
  - Document文档: 类似于mysql中的一条记录, json格式
  - Field字段: 类似于mysql中的列, 是数据项的字段
  - Mapping映射: 类似于mysql中表结构, 字段类型、默认值、分析器、是否被索引等字段的结构信息
  - Shards分片: 类似于mysql中水平分表, 一个索引的数据划分为多个片, 合在一起就是该索引全部数据
  - Replicas副本: 提供高可用, 提高吞吐量
  - Allocation分配: 分片分配给节点. 分配主分片或副本、副本还有复制数据任务
  - 倒排索引: 记录每个词条出现在哪些文档中, 检索时计算相关性得分
- 增删改查--存储功能
  - put请求/ip:port/索引/类型/id; post请求可以不带id自动生成
  - delete请求删除文档或删除索引, 注意没有删除类型这个接口
  - 使用put与post进行更新(同时新增); post/_update会对比原来的数据一样则什么也不做, put不能和_update组合在一起
  - get请求/ip:port/索引/类型/id;
- 信息检索方式
  - 一个是通过使用REST request url发送搜索参数(url+检索参数)
  - 一个是通过使用REST request body来发送它们(url+请求体)--常用
- 匹配查询--检索功能
  - match: 基本数据类型是精确匹配, 字符串类型是全文检索
  - match_phrase: 短语当成整个单词(不分词)进行检索
  - multi_match: 多个字段进行全文检索, 不管哪个字段包含了都算匹配上
  - term: 完全匹配常用于keyword类型, 全文检索字段用match, 其他非text字段匹配用term, 
  - bool: 用来做复合查询; 搭配: must, should, must_not, filter(后两个不贡献相关性得分)
  - Avoid using keyword fields for full-text search. Use the text field type instead
- 执行聚合--分析功能
  - 聚合提供了从数据中分组和提取数据的能力. 最简单的聚合方法大致等于SQL GROUP BY和SQL聚合函数
- 映射
  - Mapping 是用来定义一个文档document, 以及它所包含的属性(field)是如何存储和索引的
  - 注释: 在7.0及以后得版本不支持type了数据直接保存在索引下边
- 分词器
  - 一个 tokenizer(分词器)接收一个字符流, 将之分割为独立的tokens(词元, 通常是独立的单词), 然后输出tokens流
  - 使用中文分词器: 安装IK插件, 下载elasticsearch-analysis-ik.zip, 下载解压到elasticsearch/plugins/文件夹
  - 自定义词库(特定名词): 使用nginx服务器来存储自定义的字典, 然后修改/usr/share/elasticsearch/plugins/ik/config/中的 IKAnalyzer.cfg.xml中配置远程扩展字典地址
      ```
      <properties>
          <!--用户可以在这里配置远程扩展字典 -->
          <entry key="remote_ext_dict">http://192.168.0.102/es/fenci.txt</entry>
      </properties>
      ```

#### ElasticSearch客户端
- 在docker容器中安装ElasticSearch服务并启动, 并安装Kibana可视化服务
- 引入客户端依赖elasticsearch-client
```
<dependency>
    <groupId>org.elasticsearch.client</groupId>
    <artifactId>elasticsearch-rest-high-level-client</artifactId>
</dependency>
```
- 编写配置类能够访问远程的ElasticSearch服务器并向容器中注入RestHighLevelClient
- 创建mapping映射关系, 即创建表及表中字段类型等, 然后才能增删改查数据
- 使用RestHighLevelClient类参照API对ElasticSearch进行操作
- `https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high.html`

#### 基本操作
- put, delete, head, get -- 幂等; post -- 非幂等
- `GET _cat/indices?v`: 查看所有索引信息
- `GET _cat/templates`: 查看所有模板信息
- `GET _cat/nodes`: 查询节点
- `GET _cat/health`: 查询健康状况
- `GET _cat/master`: 查询主节点
- `PUT _template/[template]`: 添加模板
- `GET _template/[template]`: 查看模板
- `PUT [index]`: 添加索引
- `GET [index]/_mapping`: 查询
- `GET [index]/_search`: 查询索引所有数据项
- `GET [index]/_doc/itemId`: 查询索引数据项
- `DELETE [index]/_doc/itemId`: 删除索引数据项
- `POST [index]/_doc/itemId{...}`: 添加索引数据项
- `POST [index]/_update/itemId{"doc":...}`: 更新索引数据项

#### 条件查询
- `GET shopping/_search?q=key:value`: 使用url参数查询
  - eg: `http://192.168.0.102:9200/shopping/_search?q=category:小米`
- 使用body查询, 具体规则查阅文档
```
GET shopping/_search
{
  "query": { 
    "bool": { 
      "must": [         # must为与，should为或
        { "match": { "title":   "Search"        }},   # match 全文检索匹配
        { "match": { "content": "Elasticsearch" }}    # match_phrase完全匹配
      ],
      "filter": [       #
        { "term":  { "status": "published" }},
        { "range": { "publish_date": { "gte": "2015-01-01" }}}
      ]
    }
  },
  "from": pageNo,
  "size": pageSize
}
```

#### java API 基本查询
- RestHighLevelClient: 访问ES客户端工具
- CreateIndexRequest: 创建索引
- GetIndexRequest: 查询索引
- DeleteIndexRequest: 删除索引
- IndexRequest: 插入数据，json格式
- UpdateRequest: 更新数据，局部更新
- GetRequest: 查询数据
- DeleteRequest: 删除数据
- BulkRequest: 批量以上操作

#### java API 高级查询
- SearchRequest: 搜索数据
- SearchSourceBuilder: 构造搜索条件
  - query
    - boolQuery
      - must : AND
      - mustNot : NOT
      - filter: AND
      - should : OR
    - term-level
      - termQuery
      - fuzzyQuery
      - wildcardQuery
      - rangeQuery
    - full-text
      - matchQuery
      - matchPhraseQuery
      - multiMatchQuery
    - joiningQueries
      - nestedQuery
  - from 
  - size
  - sort
  - highlighter
  - aggregation
    - Bucket
      - terms
    - Metrics
      - max
      - min
      - sum
      - avg
    - Pipeline
  - fields option
    - fetchSource

#### 集群系统架构
- 副本数据不能原始数据分配在同一节点
- 存储数据进行路由计算: hash(id) % 主分片数
- 读取请求进行分片控制: 随机轮询读取数据 
![集群系统架构](https://github.com/CyS2020/SpringCloud-Mall/blob/main/resources/ES%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.PNG?raw=true)

#### 分词器
- 字段属性是keyword则不进行分词, text则进行分词
- 词条: 缩影中最小存储和查询单元
- 词典: 词条的集合, B+, HashMap
- 倒排表: 词条为key, 文档id列表为value
- 查询顺序: 先词典, 无则返回; 通过倒排表获取文档id列表, 然后根据id查数据

#### 文档搜索
- 倒排索引写入磁盘后是不可改变的, 读入内存后也是不变的
- 倒排索引更新时, 添加补充索引. 查询时每个倒排索引都查, 然后合并查询结果
- 删除数据时进行逻辑删除(添加标记), 返回结果前过滤掉标记的数据
- 当倒排索引合并的时候会真正删除数据, 物理删除
- Lucene全文检索引擎工具包

#### 近实时搜索
- es索引采用多分片的方式, 整体架构上采用一主多副的架构
- 写入数据时, 根据协调节点路由到某个主分片节点进行写入, 同时在多副本分片节点进行写入
  - 时延: 主分片延时 + 并行写入副本的最大延时
  - 大体上先写入内存, 再写translog, 再将内存数据flush进磁盘, 返回请求, 清空translog
  - 每一秒将内存分片数据reflush到OS cache, 数据存到OS cache即可对外提供查询服务
  - 每半个小时将OS cache分片数据flush到磁盘; 每5秒将translog数据flush到磁盘
![es写入数据](https://github.com/CyS2020/Notebook/blob/master/images/es%E5%86%99%E5%85%A5%E6%95%B0%E6%8D%AE.jpg)

#### 文档分析
- 分析器
  - 字过滤器: 分词前整理字符串, 去掉html, 或者将 & 转化为 and
  - 分词器: 字符串被分词器分为单个词条
  - Token过滤器: 词条按顺序通过每个 token过滤器, 可能会改变词条, 删除词条, 增加词条
- 内置分析器
  - 标准分析器
  - 简单分析器
  - 空格分析器
  - 语言分析器
  - IK分词器: 中文分词器

#### 文档冲突
- 全量更新的时候会覆盖之前的数据, 一般没有问题
- 在进行局部更新的时候需要进行加锁处理; 悲观锁, 乐观锁
  - _version
  - _seq_no
  - _primary_term

#### Spring Data 集成

#### 优化
- 硬件优化: 依赖磁盘I/O, 使用SSD(固态硬盘)、RAID0(磁盘并行)
- 分片策略: 
- 路由选择: 
- 写入速度优化:
- 合理使用合并:
- 减少Refresh频率:
- 加大Flush容量:
- 减少副本数量:
- 内存设置

#### 常见面试题
- master选举流程
- 集群脑裂问题
- es索引文档的流程
- es更新和删除文档的流程
- es搜索流程
  - 搜索被执行成一个两阶段过程, 称之为Query Then Fetch
  - 初始阶段会将查询广播到索引中每一个分片拷贝(主分片或副本分片)
- 大数据量的聚合
- 如何保证数据一致性
- 如何监控es集群状态
- 是否了解字典树
- 集群、节点、索引、文档
- 倒排索引

#### 实战总结
- 索引模板可以在索引创建或者插入文档数据的时候自动匹配, es内置了一些模版; (mappings, settings, aliases)
- keyword fields are often used in sorting, aggregations, and term-level queries, such as term.
  - the settings from the create index request take precedence over settings specified in the index template and its component templates.
- Mapping parameters
  - index
    - The index option controls whether field values are indexed. It accepts true or false and defaults to true. Fields that are not indexed are typically not queryable.
  - doc_value
    - Should the field be stored on disk a column-oriented fashion, so that it can later be used for sorting, aggregations, or scripting
    - Doc values are supported on almost all field types, with the notable exception of text and annotated_text fields.
  - dynamic
    - The dynamic parameter controls whether new fields are added dynamically, and accepts the following parameters: <br/>true、runtime、false、strict
- Field data types
  - JSON documents are hierarchical in nature, Similar to common types of queries, e.g. used `manager.name.first` as key to query
  - The nested type is a specialised version of the object data type that allows arrays of objects to be indexed in a way that they can be queried independently of each other. (if want to query the field the "index parameter" = true)
