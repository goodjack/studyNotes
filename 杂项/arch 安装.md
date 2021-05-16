# arch 安装

## 连接无线网络

```bash
iwctl #进入交互界面

device list # 查看有几个网卡

station 网卡 scan # 扫描网络
station 网卡 get-networks # 查看网络
station 网卡 connect wifi名  # 连接网络
```

## 分区

```bash
lsblk # 查看硬盘

parted /dev/sdb	# 变更该磁盘类型
# 以下是 parted 的交互界面
(parted)mktable
New disk lable type？gpt	# 磁盘类型转为 gpt 格式
quit # 推出交互


cfdisk /dev/sdb # 执行分区操作

```

