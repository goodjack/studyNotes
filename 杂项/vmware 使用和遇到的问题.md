安装 open-vm-tools 和 open-vm-tools-desktop，这两个插件是可以支持虚拟机和宿主机互相拖拽文件，复制粘贴，和共享文件夹。

指定虚拟机的文件夹挂载到共享文件夹

```bash
# 此命令可以查看有几个共享的文件夹
vmware-hgfsclient
# 修改配置文件，将里面的 allow_other 注释去掉
vim /etc/fuse.conf 
# 手动挂载，nonempty 选项是挂载在一个非空目录下，会让原来目录下的文件“消失“，取消挂载的时候，原目录下的文件会出现
vmghfs-fuse .host:/{指定 vmware-hgfsclient 下的任一目录} /{此目录是虚拟机下的任一目录} -o allow_other -o nonempty
# 开机自动挂载，修改 /etc/fstab
.host:/{share} /{指定虚拟机目录} fuse.vmhgfs-fuse allow_other,nonempty 0 0
```



## 配置 samba

[ubuntu-samba 配置](  https://pomelojiang.github.io/linux_deploy_samba.html )

#### win10 + ubuntu 环境

```bash
sudo apt update
sudo apt install samba # samba 服务
sudo apt install smbclient  # samba 客户端测试连接
# 修改配置文件
sudo vim /etc/samba/smb.conf

### 在 smb.conf 底部添加一块 [share]
[share]
   path = 此处填写要共享的目录
   browseable = yes
   writable = yes
   comment = smb share www
```

配置完成后，创建 samba 用户，必须确保有一个同名用户

```bash
sudo smbpasswd -a $USER
```

重启 samba 服务

```bash
sudo service smbd restart
```

##### 问题：

phpstorm 无法直接访问 samba 的文件，需要使用软连接

**win10 软连接 mklink**

[mklink 使用](https://liam.page/2018/12/10/mklink-in-Windows/)

mklink 使用：

- /D 符号链接，作用：目录
- /H 硬链接，作用：文件
- /J   联接，作用：目录
- 不带参数  符号链接，作用：文件

```powershell
mklink /D 链接地址 源地址
```

