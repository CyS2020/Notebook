### ElasticSearch全文索引
- `https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html`

#### 基本概念
- 万物皆索引
- Index索引: 类似于mysql中的Database
- Type类型: 类似于mysql中的Table, 高版本已删除Type
  - 默认Type是`_doc`
- Document文档: 类似于mysql中的一条记录, json格式

#### 基本操作
- put, delete, head, get -- 幂等; post -- 非幂等
- `GET _cat/indices?v`: 查看所有索引信息
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
        "match": {
            "key": "value"
        }
    },
    "from": pageNo,
    "size": pageSize
}
```