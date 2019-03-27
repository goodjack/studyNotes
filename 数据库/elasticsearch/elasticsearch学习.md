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