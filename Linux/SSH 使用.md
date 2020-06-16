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

## ssh 传输文件

- 从服务器下载文件

  `scp server_user@ip:file_address local_file_address`

- 上传文件到服务器

  `scp local_file_address server_user@ip:remote_file_address`

**对于文件夹的操作加入参数 -r 即可**