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
  - term: 全文检索字段用match, 其他非text字段匹配用term
  - bool: 用来做复合查询; 搭配: must, should, must_not, filter(后两个不贡献相关性得分)
- 执行聚合--分析功能
  - 聚合提供了从数据中分组和提取数据的能力. 最简单的聚合方法大致等于SQL GROUP BY和SQL聚合函数
- 映射
  - Mapping 是用来定义一个文档document, 以及它所包含的属性(field)是如何存储和索引的
  - 注释: 在7.0及以后得版本不支持type了数据直接保存在索引下边
- 分词器
  - 一个 tokenizer(分词器)接收一个字符流, 将之分割为独立的tokens(词元, 通常是独立的单词), 然后输出tokens流
  - 使用中文分词器: 安装插件elasticsearch-analysis-ik, 下载解压到elasticsearch/plugins/ik文件夹下
  - 自定义词库: 使用nginx服务器来存储自定义的字典, 然后修改/usr/share/elasticsearch/plugins/ik/config/中的 IKAnalyzer.cfg.xml中配置远程扩展字典地址
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
- `GET _cat/nodes`: 查询节点
- `GET _cat/health`: 查询健康状况
- `GET _cat/master`: 查询主节点
- `PUT index`: 添加索引
- `GET index/_search`: 查询索引所有数据项
- `GET index/_doc/itemId`: 查询索引数据项
- `DELETE index/_doc/itemId`: 删除索引数据项
- `POST index/_doc/itemId{...}`: 添加索引数据项
- `POST index/_update/itemId{"doc":...}`: 更新索引数据项

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
      - must
      - mustNot
      - filter
      - should
    - matchQuery
    - termQuery
    - matchPhraseQuery
    - multiMatchQuery
    - rangeQuery
    - fuzzyQuery
  - from
  - size
  - sort
  - highlighter
  - aggregation
    - max
    - min
    - sum
    - avg
    - terms
    - subAggregation
  - fetchSource

#### 集群系统架构
![集群系统架构](https://github.com/CyS2020/SpringCloud-Mall/blob/main/resources/ES%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.PNG?raw=true)

