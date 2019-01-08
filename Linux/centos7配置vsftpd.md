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

