错误原因：更新 Ubuntu 时有几个包因为网络原因没有更新成功，导致开机登入紫屏

修复损坏的系统

- 进入登录页面，ctrl+alt+f2 切换到 tty2
- 依次输入以下命令

```bash
sudo rm /var/lib/apt/lists/lock
sudo rm /var/lib/dpkg/lock
sudo rm /var/lib/dpkg/lock-frontend
sudo dpkg --configure -a
sudo apt clean
sudo apt update --fix-missing
sudo apt install -f
sudo dpkg --configure -a
sudo apt upgrade
sudo apt list-upgrade

sudo reboot
```

