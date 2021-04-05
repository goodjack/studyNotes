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

##### 泛查询与指定字段查询

```
GET /index/_search?q=2012  # 泛查询
GET /index/_search?q=2012&df=title  # 指定字段查询
```

##### Term 和 Phrase

```
GET /index/_search?q=title:"a b" // pharse 查询，表示要按照 a b 的顺序查询
GET /index/_search?q=title:(a b) // 分组查询，表示要包含 a 或 b
```

##### 查询操作符

```
AND、OR、NOT 或者 && 、||、!
必须大写
title:(a NOT b)

分组
+ 表示 must
- 表示 must_not
例：title:(+a -b) 表示必须包含a 不包含b

范围查询
区间：[]闭区间，{}开区间
year:{2019 TO 2018}
year:[* TI 2018]

算数符号
year:>2010
year:(>2010 && <=2018)
year:(+>2010 +<=2018)

通配符查询（通配符查询效率低，占用内存大）
？ 代表1个字符 * 代表 0 或多个字符
title:mi?d
title:be*

正则表达
title: [bt]oy

模糊匹配与近似查询
title:befutifl~1
title:"lord rings"~2
```



### Request Body Search

分页

```
POST /index/_search
{
"from":1,
"size":10,
"query": {
	"match_all": {
	}
}
}
```

排序

```
GET /index/_search
{
	"sort": [{"order_date":"desc"}]
	"query":{
	"match_all":{}
	}
}
```

_source 过滤

- _source  支持通配符
- 如果 _source 没有存储，就只返回匹配文档的元数据

```
GET index/_search
{
	"_source": ["order_date","category.keyword"]
	"query":{
		"match_all": {}
	}
}
```

脚本字段

```
GET index/_search
{
	"script_fields": {
		"new_field": {
			"script": {
				"lang": "painless",
				"source": "doc['order_date'].value + 'hello'"
			}
		}
	},
	"query": {
		"match_all": {}
	}
}
```

查询表达式

```
GET index/_search
{
	"query": {
		"match": {
			"comment": "last christmas" # 默认是 or
			"operator": "AND"
		}
	}
}
```

短语搜索

```
GET index/_search
{
	"query": {
		"match_phrase": {
			"comment": {
				"query": "a b c", # 表示必须按照顺序出现
				"slop": 1	# 设置这个值，可以使得顺序中间出现其它字段，这里允许出现一个字段
			}
		}
	}
}
```

Query string 

```
GET index/_search
{
	"query": {
		"query_string":{
			"default_field": "name",	# 指定查询字段，等于 df，多个字段 ["a","b"]
			"query": "a AND b" 
		}
	}
}
```

simple query string

- 类似 query string，会忽略错误的语法，同时只支持部分查询语法

- 不支持 AND OR NOT，会当做字符串处理
- Term之间的默认关系是 OR，可以指定operator
- 支持部分逻辑：
  - \+ 替代 AND
  - | 替代 OR
  - \- 替代 NOT

```
GET index/_search
{
	"query":{
		"simple_query_string": {
			"query": "a b",
			"fields": ["a"]
			"default_operator": "AND"
		}
	}
}
```

### Mapping & Daynamic Mapping

mapping 类似数据库的 schema，作用：

- 定义索引中的字段名称
- 定义字段的数据类型，例如字符串，数字，布尔
- 字段，倒排索引的相关配置

mapping 会把 json 文档映射成 Lucene 所需要的扁平格式

一个 mapping 属于一个索引的 Type

- 每个文档都属于一个 Type
- 一个 Type 有一个 mapping 定义
- 7.0 开始 不需要在 mapping 中指定 Type 信息

字段的数据类型：

- 简单类型
  - Text、keyword
  - Date
  - Integer、float
  - Boolean
  - IPv4 & IPv6
- 复杂类型 对象和嵌套对象
  - 对象类型、嵌套类型
- 特殊类型
  - geo_point & geo_shape 、percolator

#### dynamic mapping 类型自动识别

| JSON类型 | es类型                                                       |
| -------- | ------------------------------------------------------------ |
| 字符串   | 匹配日期格式，设置成date<br />配置数字设置为 float 或 long，该选项默认关闭<br />设置为text，并且增加keyword字段 |
| 布尔值   | boolean                                                      |
| 浮点数   | float                                                        |
| 整数     | long                                                         |
| 对象     | object                                                       |
| 数组     | 由第一个非空数值类型所决定                                   |
| 空值     | 忽略                                                         |

##### 更改mapping类型

1. 新增字段
   - dynamic 设为 true，一旦有新增字段写入，mapping 同时也被更新
   - dynamic 设为 false，mapping 不会被更新，新增字段的数据无法被索引，但是信息会出现在 _source 中
   - dynamic 设为 strict，文档写入失败
2. 对已有字段，一旦已有数据写入，就不再支持修改字段定义。如果希望改变字段类型，必须 reindex api，重建索引

#### Multi Match Query

单字符串和多字段搜索

```http
POST index/_search
{
	"query": {
		"multi_match": {
			"type": "best_fields",	// 默认的查询类型
			"query": "Quick pets",
			"fields": ["title","body"],	// 会依据这个数组内的字段，去最高分的数据
			"tie_breaker": 0.2,
			"minimum_should_match": "20%",
		}
	}
}
```

#### Function Score Query

优化算分

```http
POST index/_search
{
	"query": {
		"function_score": {
			"query": {
				"multi_match": {
					"query": "popularity",
					"fields": ["title","content"]
				}
			},
			"field_value_factor": {	// 指定算分的函数和因子
				"field": "votes",
				"modifier": "log1p",
				"factor": 0.1
			},
			"boost_mode": "sum",
			"max_boost": 3
		}
	}
}
```

#### 分页与遍历

scroll

> 需要全部文档，例如导出全部数据

```http
POST index/_search?scroll=5m # scroll 表示创建一个 5m 的快照，但有新数据写入以后无法被查到
{
	"size": 1,				# 创建成功后，会返回一个 scroll_id
	"query": {
		"match_all": {}
	},
}

POST  _search/scroll
{
	"scroll": "1m",
	"scroll_id": "_scroll_id"
}
```




pagination

> from 和 size
>
> 深度分页，则使用 search after

```http
POST index/_search
{
	"size": 1,
	"query": {
		"match_all": {}
	},
	"search_after": [	# 使用 search after 必须要确保有一个唯一排序id，如 _id， 每次使用结果集返回的最后一个 id
		12,
		"fasffhfsdf"
	],
	"sort": [
		{"age": "desc"},
		{"_id": "asc"},
	]
}
```

### 聚合查询 aggregation

#### Metric Aggregation

- 单值分析：只输出一个分析结果
  - min，max，avg，sum
  - Cardinality （类似 distinct count）
- 多值分析：输出多个分析结果
  - stats，extended stats
  - percentile，percentile rank
  - top hits

#### Bucket Aggregation

按照一定规则，将文档分类到不同的bucket中。

#### pipeline Aggregation

支持对聚合分析的结果，再次进行聚合分析

pipeline 的分析结果会输出到原结果中，根据位置不同，分为两类：

- sibling：结果和现有分析结果同级
  - max，min，avg，sum bucket
  - stats，extended status bucket
  - percentiles bucket
- parent：结果内嵌到现有聚合分析结果之中
  - derivative 求导
  - cumulative sum （累计求和）
  - moving function （滑动窗口）