## elasticsearch 是什么

elasticsearch 是一个基于 Apache Lucence 的开源搜索引擎，elasticsearch 使用Java 开发并使用 Lucence 作为其核心实现所有索引和搜索的功能

- 分布式的实时文件存储，每个字段都被索引并可被搜索
- 分布式的实时分析搜索引擎
- 可以扩展到上百台服务器，处理 PB 级结构化或非结构化数据，支持全文搜索

### elastic 生态圈

kibana：可视化

elasticsearch：存储计算查询

logstash、Beat：数据抓取

### elastic 文件目录结构

| 目录    | 配置文件          | 描述                                         |
| ------- | ----------------- | -------------------------------------------- |
| bin     |                   | 脚本文件，启动es，安装插件。运行统计数据等。 |
| config  | elasticsearch.yml | 集群配置文件，user，role相关配置             |
| JDK     |                   | java运行环境                                 |
| data    | path.data         | 数据文件                                     |
| lib     |                   | java类库                                     |
| logs    | path.log          | 日志文件                                     |
| modules |                   | 包含所有ES模块                               |
| plugins |                   | 包含所有已安装插件                           |

## Document 文档

- es 是面向文档的，文档是所有可搜索数据的最小单位
- 文档会被序列化成 JSON 存在 es 中
- 每个文档都有一个 unique id

#### 文档的元数据

用于标注文档的相关信息

- _index 文档所属的索引名
- _type 文档所属的类型名
- _id 文档唯一ID
- _source 文档的原始 JSON 数据
- _version 文档的版本信息
- _score 相关性打分

index 索引是文档的容器，每个索引都有自己的 mapping 定义，用于定义包含文档的字段名和字段类型

shard 索引中的数据分散在 shard 上

索引的 mapping 和 settings

- mapping 定义文档字段的类型
- settings 定义不同的数据分布

### 集群

es 不同的集群通过不同的名字来区分，默认 elasticsearch ，也可以通过指定 cluster.name=xxx 来设定。

### 节点

节点就是一个es实例，每个节点都有一个名字，通过 node.name=xxx 指定

每个节点在启动之后，会分配一个UID，保存在data目录下

#### master-eligible 节点 和 master 节点

> 每个节点启动后，默认就是一个 master eligible 节点。可以通过 node.master: false 禁止
>
> master eligible 节点可以参加选主流程，成为master 节点
>
> 当第一个节点启动时，它会将自己选举成 master 节点
>
> 每个节点上都保存了集群的状态，且只有master节点才能修改集群状态信息
>
> - 所有的节点信息
> - 所有的索引和其相关的 mapping 与 setting 信息
> - 分片的路由信息
>
> 如果任意节点都能修改信息会导致数据的不一致性

#### data node & coordinating node

> **data node**
>
> 保存数据的节点，叫做data node。负责保存分片数据。在数据的扩张上起到关键作用
>
> **coordinating node**
>
> 负责接收client请求，将请求分发到合适的节点，最终把结果汇集到一起
>
> 每个节点默认起到 coordinating node 的职责

#### hot & warm node (冷热节点)

> hot 节点配置更高，io和cpu更高
>
> warm 节点，存储旧数据
>
> 通过hot & warm 可以降低集群部署成本

#### machine learning node

> 跑机器学习的job，用来做异常检测

### 配置节点类型

生产环境中，应该为每个节点设置单一的角色

| 节点类型          | 配置参数    | 默认值                                                    |
| ----------------- | ----------- | --------------------------------------------------------- |
| master eligible   | node.master | true                                                      |
| data              | node.data   | true                                                      |
| ingest            | node.ingest | true                                                      |
| coordinating only | -           | 每个节点默认都是coordinating，需要设置其他类型全部为false |
| machine learning  | node.ml     | true (需额外 enable x-pack)                               |

### 分片（Primary Shard & Replica Shard）

- 主分片，解决数据水平扩张的问题。通过主分片可以将数据分布到集群内的所有节点之上
  - 主分片在索引创建时指定，后续不允许修改，除非 reindex
- 副本，解决数据高可用的问题。分片是主分片的拷贝
  - 副本分片数，可以动态调整
  - 增加副本数，可以在一定程度上提高服务的可用性（读取的吞吐）

### 文档的基本crud与批量操作

Create 一个文档

```
PUT users/_create/1
POST users/_doc
两者的区别是：
- put指定文档ID，如果文档存在，则操作失败
- post系统自动生产ID
```

Get 一个文档

```
Get index/type/id
```

Index 文档

```
PUT index/_doc/1
index 和 create 区别：如果文档不存在，就索引新的文档。否则现有文档会被删除，新的文档被索引。版本信息+1
```

#### Bulk API

```
POST _bulk
```

支持在一次API调用中，对不同的索引进行操作

支持四种类型：index、create、update、delete

操作中单条失败不会影响其他操作

返回结果包括了每一条操作执行的结果

#### mget

```
GET _mget
```

批量操作，可以减少网络连接所产生的开销

#### msearch

批量查询

```
POST index/_msearch
```

### 倒排索引

- 单词词典（term dictionary）记录所有文档的单词，记录单词到倒排列表的关联关系
- 倒排列表（posting list）记录单词对应的文档结合，由倒排索引项组成
  - 倒排索引项（posting）
    - 文档ID
    - 词频TF，该单词在文档出现的次数，用于相关性评分
    - 位置Position，单词在文档中分词的位置。用于语句搜索
    - 偏移offset，记录单词的开始结束位置，实现高亮显示



### URI Search 通过 URI query 实现搜索

> GET /index/_search?q=2012&df=title&sort=year:desc&from=o&size=10&timeout=1s
>
> - q 指定查询语句
> - df 默认字段，不指定时会对所有字段进行查询
> - sort 排序，from 和 size 分页
> - profile 可以查看查询是如何被执行的

使用 RESTful api 对 elasticsearch 进行操作

| MySQL              | elasticsearch    |
| ------------------ | ---------------- |
| 数据库（Database） | 索引（index）    |
| 表（table）        | 类型（type）     |
| 行（row）          | 文档（document） |
| 列（column）       | 字段（fields）   |

**6.0 以后，一个 index 下，只允许有一个 type**

## 查询语句 Query DSL

### Boolean query

| 参数                 | 描述                                                         |
| -------------------- | ------------------------------------------------------------ |
| must                 | 必须匹配，并且会增加 score 的分数                            |
| filter               | 必须匹配，会忽略 score ，filter 会在 filter context 中执行，并且会缓存 |
| should               | 匹配                                                         |
| must_not             | 必须不匹配，忽略score，在 filter context 中执行，会缓存      |
| minimum_should_match | 最小匹配，可以指定一个数字或者是百分比，如果 bool query 中只包含 should 没有 must 和 filter ，那么默认值是 1，否则是 0 |

### Boosting query

- positive：查询必须匹配
- negative：匹配到此处的会减少 score
- negative_boost ：浮点数在 0 到 1.0 之间，用于 negative 去减少 score

### Constant score query

- filter：必须匹配，不会计算 relevance scores，会缓存
- boost：用于匹配每个 filter 查询的 relevance score，默认是 1.0

### Disjunction max query

- queries：匹配多个子查询，如果匹配到了多个子查询会提高 relevance score
- tie_breaker：浮点数在 0 至 1.0 之间，用于当匹配到时增长 relevance scores