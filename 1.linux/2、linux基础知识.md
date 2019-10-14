[linux定时任务crontab](https://mp.weixin.qq.com/s/ANm_BHJWRX6TLGWeiArxSg)





创建sock文件

nc -Ul sock

[TOC]



# 1.强化linux服务器

可参考：https://www.cnblogs.com/xiaogan/p/5902846.html

https://mp.weixin.qq.com/s/KJAS19EIm3dCiqoEnmykAw

```shell
#1. 更新本地存储库,dnf是新一代的RPM软件包管理器，参考：https://ipcmen.com/dnf
sudo dnf upgrade
#2.尽量不要用root用户

#3.强化ssh，
vim /etc/ssh/sshd_config	# 修改成
PasswordAuthentication no
PermitRootLogin no
#参考天翼云ssh配置
grep -v "^#" sshd_config|grep -v "^$"
Port 1433
HostKey /etc/ssh/ssh_host_rsa_key
HostKey /etc/ssh/ssh_host_ecdsa_key
HostKey /etc/ssh/ssh_host_ed25519_key
SyslogFacility AUTHPRIV
PermitRootLogin no
AuthorizedKeysFile      .ssh/authorized_keys
PasswordAuthentication yes
ChallengeResponseAuthentication no
GSSAPIAuthentication yes
GSSAPICleanupCredentials no
UsePAM yes
X11Forwarding yes
Banner /etc/ssh/banner
AcceptEnv LANG LC_CTYPE LC_NUMERIC LC_TIME LC_COLLATE LC_MONETARY LC_MESSAGES
AcceptEnv LC_PAPER LC_NAME LC_ADDRESS LC_TELEPHONE LC_MEASUREMENT
AcceptEnv LC_IDENTIFICATION LC_ALL LANGUAGE
AcceptEnv XMODIFIERS
Subsystem       sftp    /usr/libexec/openssh/sftp-server
Banner /etc/ssh/banner
Ciphers aes128-ctr,aes192-ctr,aes256-ctr,aes128-cbc,3des-cbc
PermitRootLogin no
UseDNS on
#4.启用防火墙

#5.安装fail2ban
```

