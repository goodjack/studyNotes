# etcd

|            | etcd               | zookeeper          |
| ---------- | ------------------ | ------------------ |
| 一致性算法 | raft               | zab                |
| 数据模型   | 基于目录的层次模式 | 基于目录的层次模式 |
| kv存储引擎 | 简单内存树         | concurrent hashMap |
| 部署       | 简便               | 复杂               |

#### etcd 与 redis 区别

数据复制：

- redis：主备异步复制，可能会丢失数据
- etcd：raft，为了读写一致性，读写性能比redis差

数据存储：

- redis：存储用户数据，可以承载上T数据
- etcd：低容量的关键元数据，db大小一般不会超过8g

数据结构：

- redis：丰富的数据结构
- etcd：仅 kv，持久化存储使用 boltdb

#### etcd 执行流程图

![](etcd.assets/457db2c506135d5d29a93ef0bd97e4bb.png)

### 搭建etcd集群

使用进程管理工具 [goreman](https://github.com/mattn/goreman) 可以快速创建、停止本地的多节点etcd集群。

下载 etcd 的二进制文件，从 [etcd 源码](https://github.com/etcd-io/etcd/blob/v3.4.9/Procfile) 中下载  goreman Profile 文件，它描述了etcd进程名、节点数、参数等信息。

使用 `goreman -f Profile start` 就可以快速启动etcd集群