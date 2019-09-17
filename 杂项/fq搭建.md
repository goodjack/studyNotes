## 服务器测速脚本

### ZBench 脚本

```bash
# 中文版
wget -N --no-check-certificate https://raw.githubusercontent.com/FunctionClub/ZBench.master/ZBench-CN.sh && bash ZBench-CN.sh

```

## 服务器被墙检测工具

#### 端口被墙

表现：

- SSH 端口连不上，其它端口正常，也可以 ping 通
- SSR/Shadowsocks 无法正常连接，其它端口正常，可以 ping 通

#### TCP 阻断

表现：

- vps 服务器可以 ping 通
- SSH 端口无法连接

### 检测工具

[http://ping.pe](http://ping.pe)

[https://ipcheck.need.sh](https://ipcheck.need.sh)





## V2ray

提高服务器连接速度，建议先为服务器安装 BBR

系统要求：Centos、Debian、Ubuntu

BBR 一键安装脚本：

```bash
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```

验证 BBR 是否安装成功：

```bash
sysctl net.ipv4.tcp_congestion_control

# 如果得到如下结果则表示 BBR 安装成功
net.ipv4.tcp_congestion_control = bbr
```



### v2ray 一键安装环境

v2ray 连接对时间准确度要求较高，服务端与客户端时间误差不能超过2分钟

系统要求：Debian8、Debian9、Ubuntu14、Ubuntu16、centos7

v2ray 一键安装脚本：

```bash
wget -N --no-check-certificate https://raw.githubusercontent.com/FunctionClub/V2ray.Fun/master/install.sh && bash install.sh
```

v2ray 安装完成，需要输入用户名、密码、控制面板端口，如遇到无法打开控制面板，运行一下命令：

`pip install Flask-BasicAuth`
