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

