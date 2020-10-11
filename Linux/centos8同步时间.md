# 	同步时间

1. centos8后改成了chrony同步时间

```
dnf install -y chrony
```

2. 修改配置文件 `/etc/chrony.conf`

```
# 将第一行修改
初始：pool 2.centos.pool.ntp.org iburst

使用国内的服务：pool time.pool.aliyun.com iburst
```

[国内的ntp服务](https://dns.icoa.cn/ntp/)

3. 使用systemctl 后台同步时间

   ```
   systemctl enable chronyd
   systemctl start chronyd
   ```

   



