## elasticsearch 是什么

elasticsearch 是一个基于 Apache Lucence 的开源搜索引擎，elasticsearch 使用Java 开发并使用 Lucence 作为其核心实现所有索引和搜索的功能

- 分布式的实时文件存储，每个字段都被索引并可被搜索
- 分布式的实时分析搜索引擎
- 可以扩展到上百台服务器，处理 PB 级结构化或非结构化数据，支持全文搜索

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