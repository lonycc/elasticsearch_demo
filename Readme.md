## 基础概念

> ES是一个基于Lucene并采用Restful API 标准的高可扩展性和高可用性的实时数据分析的全文检索工具.

- Node(节点)：单个的装有ES服务并提供故障转移和扩展的服务器。

- Cluster(集群)：一个集群就是由一个或多个Node组织在一起，公共分享整个数据具有负载均衡功能。集群名称是唯一标识，Node只能通过指定集群名来加入集群。
 
- Document(文档)：一个文档是一个可以被索引的基础信息单位
 
- Index(索引)：索引是具有相似特征的文档的集合
 
- Type(类型)：一个索引中, 可以定义一种或多种类型

- Field(列)：相当于文档的一个字段

- Shards(分片)：一个索引可以存储超出单个结点硬件限制的大量数据。比如，一个具有10亿文档的索引占据1TB的磁盘空间，而任一节点都没有这样大的磁盘空间；或者单个节点处理搜索请求，响应太慢。因此ElasticSearch将索引分成若干份，每个部分就是一个shard ，当你创建一个索引的时候，你可以指定你想要的分片的数量。每个分片本身也是一个功能完善并且独立的“索引”，这个“索引”可以被放置到集群中的任何节点上。 分片好处就是可以对数据进行水平分割，扩展内容容量，提高查询性能和吞吐量

- Replicas(复制)：Replicas是索引的一份或者多份拷贝 提供高可用

<br/>

**分片和复制的数量可以在索引创建的时候指定。在索引创建之后，你可以在任何时候动态地改变复制数量，但是不能改变分片的数量。**

<br/>

**ES与关系型数据库对照**

```
数据库  ---   Index
表      ---   Type
数据行  ---   Document
数据列  ---   Field
```

## 剖析mapping

> 一个mapping由一个或多个analyzer组成， 一个analyzer又由一个或多个filter组成的。当ES索引文档的时候，它把字段中的内容传递给相应的analyzer，analyzer再传递给各自的filter。

> filter的功能很容易理解：一个filter就是一个转换数据的方法， 输入一个字符串，这个方法返回另一个字符串，比如一个将字符串转为小写的方法就是一个filter很好的例子。

> 一个analyzer由一组顺序排列的filter组成，执行分析的过程就是按顺序一个filter一个filter依次调用， ES存储和索引最后得到的结果。

> 总结来说， mapping的作用就是执行一系列的指令将输入的数据转成可搜索的索引项。

## 默认analyzer

> 默认的analyzer是标准analyzer, 这个标准analyzer有三个filter：`token filter`, `lowercase filter`和`stop token filter`。

> 做全文搜索就需要对文档分析、建索引。从文档中提取词元（Token）的算法称为分词器（Tokenizer），在分词前预处理的算法称为字符过滤器（Character Filter），进一步处理词元的算法称为词元过滤器（Token Filter），最后得到词（Term）。这整个分析算法称为分析器（Analyzer）。

> 文档包含词的数量称为词频（Frequency）。搜索引擎会建立词与文档的索引，称为倒排索引（Inverted Index）。

> analyzer按顺序做三件事：1. 使用`CharacterFilter`过滤字符; 2. 使用`Tokenizer`分词; 3. 使用`TokenFilter`过滤词.

## Mapping

> 一个完整的`Mapping`，大致分成`settings`和`mappings`两个部分: `settings`主要作用于`index`的一些相关配置信息，如分片数`number_of_shards`、副本数`number_of_replicas`、`tranlog`同步条件、`refresh`条件等。`mappings`部分主要是搜索结构说明，`mappings`又分成`_all`、`_source`、`properties`三部分。

### index_options

该参数控制增加到倒排索引的信息, 为了搜索和高亮.
 
`docs`: 只索引文档号, freqs:文档号和词频都会被存储

`positions`: 文档号/词项/词的位置会被索引

`offsets`: 文档号/词项/词的位置/字符偏移都会被索引

分析字符串默认会使用positions, 其他默认docs

### include_in_all

field包含在_all域, 默认为true.


### term_vector

`no|yes|with_positions|with_offsets|with_positions_offsets`

如果你需要做高亮, 那就选择with_positions_offsets, 否则选择no


### _source域

_source域包含索引时原始的json文档, _source域不被索引, 但被存储.执行get/search请求会被返回(如果你只搜指定的字段,那么可以禁用_source域,否则不要), 如果你不使用_source 域, 你就必须设置_stored你要存储的域


### _all域

拥有所有域, 使用空格作为分隔符将所有字段拼接在一起的大字符串. 它可以用于分析和索引, 但不存储.

**索引demo**

```
PUT /demo
{
    "settings:" {
        "number_of_shards": 5,
        "number_of_replicas": 1,
        "index.tranlog.flush_threshold_ops": "100000",
        "index.refresh_interval": "-1"
    },
    "mappings": {
        "demo": {  //索引名称
            "dynamic": true,
            "_all": {  //all Field
                "analyzer": "like",
                "enabled": false
            },

            "_source": {  //sorce Filed, 可理解为索引文件以外的一份源数据
                "enabled": true,
                "compress": true,
                "includes": ["title", "desc"],  //包含
                "excludes" ["origin"],   //不包含
            }

            "properties": {
                "id": {
                    "type": "text|keyword|date",
                    "index": false,   //该字段不索引
                    "analyzer": "whitespace",   //采用的分析器
                    "format": "",
                    "store": true|false,  //否存储到倒索索引中去
                    "include_in_all": true|false,  //是否包含到_all field, 默认为true, 除非index被设为false
                },
                "manager": {  //这个filed是个object
                    "properties": {
                        "age":  { "type": "integer" },
                        "name": { "type": "text"  }
                    }
                },
                "employees": { 
                    "type": "nested",  //nested或者objest类型可以包含子filed
                    "properties": {
                        "age":  { "type": "integer" },
                        "name": { "type": "text"  }
                    }
                },

            }
        }
    }
}
```

**指定_id字段创建索引**
```
curl -XPUT 172.23.130.202:9200/my-index/my-type/url -d '
{
    "mappings": {
        "my-type": {
            "_id": {"path": "url"},
            "properties": {
                "url": {
                    "type": "keyword",
                }
            }
        }
    }
}'
```

**MongoDB同步到ES**

`pip install mongo-connector`

`pip install elastic2_doc_manager`

`mongo-connector --auto-commit-interval=0 -m mongo:27017 -t elasticsearch:9200 -d elastic2_doc_manager -n db.tb_1,db.tb_2`

**ES常用接口**

`GET /_cat` # 获得所有_cat接口

`GET /_cluster/health?pretty=true` # _cluster接口

`GET /_cluster/stats?pretty=true`  # 显示集群系统信息

`GET /_cluster/state?pretty=true`  # 显示集群详细信息

`GET /_cluster/pending_tasks?pretty=true`  # 获取集群堆积任务

`POST /_cluster/nodes/_shutdown` # 关闭集群

`POST /_cluster/nodes/_all/_shutdown`

`POST /_shutdown?delay=10s`

`GET /_cat/health?v` # 检查集群健康

`GET /_cat/nodes?v` # 获取集群中节点列表

`GET /_cat/indices?v` # 列出所有索引

`PUT /demo`    # 创建索引

`DELETE /demo` # 删除索引
