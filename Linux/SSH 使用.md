## ssh 免密登录

1. 在本机先使用 `ssh-keygen -t rsa -c "xxxx@.com"` 生成公私钥

2. 使用 `ssh-copy-id -i "~/.ssh/xxx_id_rsa.pub" user@server_ip` 此时就将生成的公钥复制到了指定的 server_ip 上

3. 此时可以使用 `ssh -i ~/.ssh/xxx_id_rsa user@server_ip` 来免密登录服务器

4. 配置 ssh 登录别名 `vim ~/.ssh/config`

   ```
   Host alias # 此处指定登录别名
     HostName server_ip # 此处填写 server_ip
     Port 22 # 默认端口是 22
     User xxx # 填写登录用户名
     IdentityFile ~/.ssh/xxx_id_rsa # 指定私钥地址
   ```

   此时就可以使用 `ssh alias` 进行登录

5. 如果要禁用密码登录，**请先确定可以使用私钥登录**，修改 `vim /etc/ssh/sshd_config`

   ```
   PasswordAuthentication no
   ChallengeResponseAuthentication no
   UsePAM no
   ```

   修改完成，重启 ssh 服务

   - Ubuntu、Debian：`sudo systemctl restart ssh`
   - Centos、Fedora：`sudo systemctl restart sshd`

## ssh 

- 从服务器下载文件

  `scp server_user@ip:file_address local_file_address`

- 上传文件到服务器

  `scp local_file_address server_user@ip:remote_file_address`

**对于文件夹的操作加入参数 -r 即可**

### ssh 端口转发

```bash
ssh -L local_addr:local_port:remote_addr:remote_port middle_host

# 示例
ssh -g -L 2222:host2:80 host3 # 表示在本地指定一个由ssh监听的转发端口 2222, 将远程host2的端口80 映射为本地端口2222, 当主机连接本地 2222 端口时，ssh 就将此端口的数据包转发给中间主机 host3,然后 host3 再与远程主机的端口 host2:80 通信
# -g  表示允许外界主机连接本地转发端口，不指定 -g 则默认地址为环回地址
```



## rsync

> #  本地同步
>
> rsync /etc/test.conf /tmp 	# 在本地同步
>
> rsync -r /etc 192.168.12.3:/tmp	# 将本地 /etc 目录拷贝到远程主机 /tmp 下，以保证远程 /tmp 目录和本地 /etc 保持同步
>
> rsync -r 192.168.12.3:/etc /tmp	# 将远程主机 /etc 目录拷贝到本地 /tmp 下，以保证本地 /tmp 目录和远程 /etc 保持同步
>
> rsync /etc/	# 列出本地 /etc/ 目录下的文件列表
>
> rsync 192.168.12.3:/tmp/	# 列出远程主机上 /tmp/ 目录下的文件列表
>
> rsync -R -r /var/./log/anaconda /tmp	# 使用一个点代表相对路径的起始位置，该示例表示 /tmp/log/anaconda



**rsync 中 如果需要拷贝一个目录不需要 / ，如果仅是拷贝目录下的内容，则需要带上 /**
