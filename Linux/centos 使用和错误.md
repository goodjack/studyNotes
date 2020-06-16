Centos 7

安装：`yum install -y vsftpd`

将 ftp 加入防火墙

```bash
firewall-cmd --permanent --zone=public --add-service=ftp
firewall-cmd --reload
```

修改 `/etc/vsftpd/vsftpd.conf` 文件：

```bash
anonymous_enable=NO		# 不允许匿名访问、禁用匿名登录
chroot_local_user=YES	# 启用限定用户在其主目录下
use_localtime=YES		# 使用本地时间
chroot_list_enable=YES	# 启动限制用户名单
local_enable=YES		# 允许使用本地账户进行 FTP 用户登录验证
allow_writeable_chroot=YES # 如果启用了限定用户在其主目录下需要添加这个配置，解决报错 500 OOPS：vsftpd: refusing to run with writable root inside chroot()
xferlog_enable=YES		# 启用上传和下载的日志功能，默认开启
local_umask=022			# 设置本地用户默认文件掩码022
```

详细配置参见：[CentOS7安装配置vsftp搭建FTP](https://segmentfault.com/a/1190000008161400)



## Centos 8

遇到的问题：最小安装的时候，没有无线网卡的插件，需要手动从 USB 介质中安装 rpm 包。

```shell
# 首先先挂载 USB 设备
mkdir /media/usb
mount -t vfat /dev/sdc1 /media/usb # /dev/sdc1 此设备不一定，通过 df -hT 可以查看
# 挂载完成后进入，挂载目录/BaseOS/Packages/
rpm -ivh NetworkManage-wifi_xxxx.rpm  # 根据提示安装缺少的依赖
# 安装完成重启服务
systemctl restart NetworkManage
nmcli c up {wifi名} # 连接 WIFI，可以使用 mtr www.baidu.com 测试是否连接成功
```



## 常用的命令

**nmcli：用于管理网络连接**

| 命令                                                     | 说明                                         |
| -------------------------------------------------------- | -------------------------------------------- |
| nmcli con (connection) show                              | 显示所有连接                                 |
| nmcli con show -active                                   | 显示所有的活动连接状态                       |
| nmcli con show "ens33"                                   | 显示网络连接配置                             |
| nmcli con reload                                         | 重新加载配置                                 |
| nmcli con up {ens33}                                     | 启用 ens33 的配置                            |
| nmcli con down {ens33}                                   | 禁用 ens33 的配置                            |
| nmcli dev (device) status                                | 显示设备状态                                 |
| nmcli dev show ens33                                     | 显示网络接口属性                             |
| nmcli con add con-name default type Ethernet ifname eth0 | 创建新连接配置default，ip 通过 dhcp 自动获取 |
| nmcli con delete default                                 | 删除连接                                     |

- nmcli con add con-name test2 ipv4.method manual ifname ens33 autoconnect no type Ethernet ipv4.addresses 192.168.1.12/24 gw4 192.168.1.1

  参数说明：

  - con-name：指定连接名字
  - ipv4.method：指定获取IP地址方式
  - ifname：指定网卡设备名
  - autoconnect：指定是否自动启动
  - ipv4.addresses：指定ipv4地址
  - gw4：指定网关

- nmcli con modify test2 connection.autoconnect yes：修改 test2 为自动启动

- nmcli con modify test2 ipv4.dns 114.114.114.114：修改 DNS

- nmcli con modify test2 +ipv4.dns 114.114.114.114：添加 DNS，删除则是将 + 改为 - 

**systemcl：启动服务**

- systemctl start | stop | restart  {服务名}
- systemctl enable {服务名} 将服务加入开机启动
- systemctl status {服务名}  查看服务状态

#### nftables：替换 iptables 作为默认的网络包过滤工具

nfttables 和 iptables 一样，由表（table）、链（chain）、规则（rule）组成，其中表包含链，链包含规则。

nftables 每个表只有一个地址簇，并且只适用于该簇的数据包。表可以指定五个簇中的一个。

- ip
- ipv6
- inet：适用于 ipv4 和 ipv6 的数据包
- arp
- bridge

##### 创建表

创建表：`nft add table inet my_table`

列出所有规则：`nft list ruleset`

##### 创建链

链是用来保存规则的，和表一样，链需要被显示创建，链有两种类型：

- 常规链：不需要指定钩子类型和优先级，可以用来做跳转，从逻辑上对规则分类
- 基本链：数据包的入口点，需要指定钩子类型和优先级

创建常规链：`nft add chain inet my_table my_utility_chain`

创建基本链：

```bash
nft add chain inet my_table my_filter_chain { 
	type filter hook input priority 0 \; # 此处\ 转义，priority 定义优先级，值越小越优先
}


# 列出链中的所有规则
nft list chain inet my_table my_utility_chain
nft list chain inet my_table my_filter_chain
```

##### 创建规则

规则由语句或表达式构成，包含在链中

`nft add rule inet my_table my_filter_chain tcp dport ssh accept`  表示允许 ssh 登录

**add** 将规则添加到链尾，**insert**将规则添加到链头

`nft insert rule inet my_table my_filter_chain tcp dport http accept`

使用 **index** 指定规则索引，**add**表示新规则添加在索引位置的规则后面，**insert** 表示新规则添加在索引位置的规则前面

##### 删除规则

单个规则只能通过句柄删除：`nft --handle list ruleset`  handle 参数列出句柄值

使用句柄值删除规则：`nft delete rule inet my_talbe my_filter_chain handle 8`

##### 列出规则

列出某个表中的所有规则：`nft list table inet my_table`

列出某个链中的所有规则：`nft list chain inet my_table my_other_chain`



#### 集合

集合分为 **匿名集合**与**命名集合**，匿名集合比较适用于将来不需要改变的规则。

```
nft add rule inet my_table my_filter_chain ip saddr {
	10.10.10.12, 10.10.10.231
} accept # 表示允许来自源 ip 处于 10.10.10.123 ~ 10.10.10.231 这个区间的主机流量
```

命名集合，创建集合需要指定元素类型，有如下类型：

- ipv4_addr：ipv4地址
- ipv6_addr：ipv6地址
- ether_addr：以太网（Ethernet）地址
- inet_proto：网络协议
- inet_service：网络服务
- mark：标记类型

创建一个空的命名集合：`nft add set inet my_table my_set { type ipve_addr \;}`

在添加规则时引用集合，可以使用 `@` 符号跟上集合的名字

`nft insert rule inet my_table my_filter_chain ip saddr @my_set drop` 表示将集合内的 ip 访问全部丢弃

向集合添加元素：`nft add element inet my_table my_set { 10.10.10.22, 10.10.10.33 }`



想在集合内添加一个区间，必须加上一个flag interval，内核必须提前确认该集合存储的数据类型

创建一个支持区间的命名集合：

```
nft add set inet my_table my_range_set { type ipv4_addr \; flags interval }
```



