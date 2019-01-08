# Linux账号管理

```
#/etc/passwd
root: x:  0:  0:   root:  /root:  /bin/bash
账号:密码:UID:GID:用户信息:主文件夹:shell
```

```
#/etc/shadow
1			2
root:$6$o2NsTLbx$QCC2G3/TS4FslliHsy/RNmcbKBp2onc0cL06NQo344BETxgPsk25I1chXqTZbiRT2c5TYc4Q6ixLzIXlyWFWd1:17674:0:99999:7: 5: 14419:
		3	 4	 5	 6 	7	8	 9
```

> 1. 账号名称与/etc/passwd对应
> 2. 密码
> 3. 最近更改密码日期（linux计算时间以1970-1-1作为1累加，86400为每一天的秒数）`date -d "1970-1-1 UTC $((17674 * 86400)) seconds"`，可以得出更改日期的时间
> 4. 密码不可被更动天数（与第3个字段相比）如果设为0，表示密码可以随时改动
> 5. 密码需要重新更改的天数（与第3个字段相比）如果设为99999，则相当于密码的更改没有强制性
> 6. 密码需要更改期限前的警告天数（与第5个字段相比）
> 7. 密码过期后的账号宽限时间（与第5个字段相比）密码有效期为“（更新日期）3 + （重改日期）5”
> 8. 账号失效日期
> 9. 保留最后一个字段，看有没有新功能加入

```
#/etc/group
root:x:0:root
  1	 2 3  4
```

> 1. 用户组名称
> 2. 用户组密码，因为密码已经移动到/etc/shadow，所以该字段只会存在x
> 3. GID
> 4. 此用户组支持的账号名称

**UID只有0与非0两种，非0则为一般账号。一般账号又分为系统账号（1-499）及可登陆者账号**

useradd：创建账号	

passwd：设置密码，单独使用passwd是更改root密码

chage：显示更加详细的的密码说明，并且也可以修改，`chage -d 0 shea：让用户登录时就被立刻要跟更改密码`

usermod：修改账号参数

userdel：删除账号

#### 一般用户使用的命令

finger：查询用户信息

chfn：修改个人属性，比如：name，phone等

chsh：修改shell

#### 新增与删除用户组

groupadd：新增用户组

groupmod：修改用户组

groupdel：删除用户组，如果无法删除某个用户组，有可能表示有某个账号（/etc/passwd）的初始用户组使用该用户组

gpasswd：用户组管理员

#### ACL

ACL针对单一用户、单一文件或目录来进行r、w、x的权限设置

setfacl：设置某个目录或文件的ACL规定

getfacl：取得某个文件或目录的ACL设置项目

```
setfacl -m u:visit:rx acl_test	#针对单一用户对单一文件（单一目录）的权限
setfacl -m g:mygroup1:rx acl_test	#针对特定用户组对单一文件（单一目录）的权限
setfacl -m d:u:mygroup1:rx ~/project	#让mygroup1在project目录下默认有rx权限
```

#### sudo

visudo：

```
root		ALL=(ALL)		ALL
  1			 2	 3			4
%mygroup1	ALL=(ALL)		NOPASSWD: ALL		#mygroup1用户组内的成员可以使用sudo
```

1. 用户账号：系统的哪个账号可以使用sudo命令，默认root
2. 登录者的来源主机名：可以指定客户端计算机，默认root可以来自任何一台计算机
3. 可切换身份：这个账号可以切换成什么身份来执行后续的命令，默认root可以切换任何人
4. 可执行命令：必须绝对路径，默认root可以使用任何命令

- ALL特殊关键字，代表任何身份、主机或命令

- %，代表用户组

- NOPASSWD，表示不使用密码直接使用sudo

- ！表示不可执行

  `visit		ALL=(root)		!/usr/bin/passwd,/usr/bin/passwd [A-Za-z]*,!/usr/bin/passwd root`

  代表visit这个用户可以切换为root使用passwd命令修改其他用户的密码，但是不能修改root用户密码

#### PAM模块

- 验证类型
  - auth（authentication）：主要用来检验用户身份
  - account：大部分是在进行authorization（授权），主要检验用户是否具有正确的权限
  - session：管理用户在这次登录（或使用这个命令）期间PAM所给予的环境设置，通常用于记录用户登录与注销时的信息
  - password：主要用于提供验证的修订工作

这四个类型通常是有顺序的，也有例外，有顺序的原因：我们总是先验证auth，系统通过用户的身份给予适当的授权与权限设置（account），而且登录与注销期间的环境才需要设置，也才需要记录登录与注销的信息（session），如果在运行期间需要修改密码，才给予password

- 验证的控制标志
  - required：验证若成功则带有success，失败则带有failure，但不论成功或失败都会继续后续的验证流程，因为后续的验证流程可以继续进行，因此相当有利于数据的登录日志
  - requisite：验证失败立刻返回failure，并终止后续的验证流程
  - sufficient：验证成功立刻回传success给原程序，并终止后续的验证流程；若验证失败failure则继续后续的验证流程
  - optional：大多是在显示信息
- 常用模块
  - pam_securetty.so：限制系统管理员（root）只能够从安全的（secure）终端机登录，安全终端机设置在/etc/securetty这个文件中
  - pam_nologin.so：限制一般用户是否能够登录主机，当/etc/nologin这个文件存在，则所有一般用户均无法再登录系统
  - pam_selinux.so：针对程序进行详细管理权限的功能
  - pam_console.so：当系统出现问题，或者某些时刻需要使用特殊的终端接口登录主机时，这个模块可以帮助处理一些文件权限，让用户可以通过特殊终端顺利登录接口
  - pam_loginuid.so：验证UID
  - pam_env.so：设置环境变量
  - pam_UNIX.so：可以用于验证阶段的认证功能，可以用于授权阶段的账号许可证管理，可以用于会议阶段的日志文件记录，也可以用于密码更新阶段的检验 
  - pam_cracklib.so：检验密码强度，包括密码是否在字典中，密码输入几次都失败就断掉此次连接等
  - pam_limits.so