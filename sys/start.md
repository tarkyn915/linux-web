# Linux的一些基本设置

> 仅供参考，个人用其实没必要这么复杂，设置一些基本的就足够了

## # 修改SSH默认端口

22 为 SSH 默认端口，如果不放心就换个端口

SSH 配置文件目录 `/etc/ssh/sshd_config` ，打开修改  `#Port 22` 修改成`Port **`

重启SSH服务 `service sshd restart`

重启SSH服务`systemctl restart sshd.service` 

>不同发行版重启SSH服务方法不同，第二种基本通用

检查SSH端口是否正常被监听`netstat -tlpna`，没有的话需要安装这个工具

>建议从 1024 - 65535 选择端口修改，*有防火墙的话，记得在防火墙中开启相应的端口*，不然就...

## # 修改系统主机名

查看系统的主机名

```hostname
# 默认查看方式
hostname
# 系统指令
hostnamectl
# 最简单方便
uname -a
# 主机名文件目录
/etc/hostname
```

修改系统主机名

```hostname
# 直接修改这个文件
/etc/hostname
# 指令直接修改，可以用 -h 查看更多设置
hostnamectl set-hostname ###
```

## # 添加新用户

在Linux中添加新用户有两种方法，`useradd` 和 `adduser`，在 [Fedora Linux](https://zh.wikipedia.org/wiki/Fedora_(%E4%BD%9C%E6%A5%AD%E7%B3%BB%E7%B5%B1)) 发行版中两者的用法是一样的，但是在 [Slackware Linux](https://zh.wikipedia.org/zh-hans/Slackware) 发行版中两者还是有所区别

>在Centos中，`adduser` 通过符号链接指向 `useradd`，所以是一样的

- `adduser` 是一个 perl 脚本，输入`adduser 用户名`后，会自动创建用户主目录（并复制`/etc/skel`目录下的文件）、指定系统 shell，提示输入用户密码，根据输入的信息帮你创建新用户

- `useradd` 是一个指令，如果不使用任何选项，创建的用户将无密码、无主目录、没有指定 shell

  `useradd -m -s /bin/bash test`

  > `-m` 自动创建用户的家目录，并将 `/etc/skel`中的文件复制到家目录中
  >
  > `-s` 指定用户登入后所使用的 shell
  >
  > 然后给这个用户设置密码 `passwd`

  ```useradd
  # 用useradd添加名为test的用户
  useradd test
  
  # 设置tset1的密码
  passwd test
  
  # 给test创建home目录
  mkdir /home/tset
  
  # 将/etc/skel目录下的文件复制到该用户目录
  cp -r /etc/skel/. /home/tset
  
  # 更改home目录归属
  chown -R tset:tset /home/tset
  
  # 指定 Shell 为/bin/bash
  usermod -s /bin/bash tset
  ```

  > 如果使用 useradd 不使用任何选项比较麻烦，也没必要这样操作，但是爱装逼可以使用

##  # 删除用户

删除用户使用 `userdel` 指令

```
# 先终结用户的所有进程
pkill -u 用户名
# 然后删除该用户
userdel -r 用户名
```

>`-r` 表示删除用户在系统中的所有的文件

## # sudo神力

想要给普通用户增加root权限，可以用到sudo

首先安装sudo `apt install sudo`，然后输入 `visudo` 找到 `# User privilege specification` 添加下面一行就可以了

```sudo
# User privilege specification

root    ALL=(ALL:ALL) ALL
test    ALL=(ALL:ALL) NOPASSWD:ALL
```

> Debian默认没有安装 sudo，不同发行版的visudo配置文件可能有所区别 `visudo` 其实就是打开 `/etc/sudoers`

## # 禁止root登录

`/etc/ssh/sshd_config` 中找到 `PermitRootLogin` 修改

```PermitRootLogin
# 把yes改成no
PermitRootLogin Yes    

# 不同发行版重启sshd服务
service sshd restart
systemctl restart sshd.service
```

## # 修改用户密码

root用户可以直接修改密码 `passwd root` ，普通用户只能修改自己的密码，要么用su上位或者借用sudo神力

`more /etc/passwd` 查看当前所有用户，自己创建的用户在末尾

## # 禁止Ping

禁止Ping有两个因素，内核参数和防火墙，有一个禁止就无法ping了

1. 关闭ICMP协议来达到禁止Ping的效果

   ```Ping
   # 在配置文件最后添加
   /etc/sysctl.conf
   net.ipv4.icmp_echo_ignore_all=1
   # 重新加载配置文件
   sysctl -p
   
   # 装逼用法
   echo net.ipv4.icmp_echo_ignore_all=1 >> /etc/sysctl.conf
   ```

   > 0表示允许,1表示禁止

2. 通过防火墙设置

   ```Ping
   # iptables防火墙设置
   iptables -A INPUT -p icmp --icmp-type echo-request -j DROP
   iptables -A OUTPUT -p icmp --icmp-type echo-reply -j DROP
   # -A是添加，想要删除用-D就可以了
   
   # Ufw防火墙设置
   /etc/ufw/before.rules
   ok icmp codes for INPUT
   ok icmp code for FORWARD
   自行修改icmp配置就可以了
   ```

   ## # Fail2ban配置

```
[sshd]
enbale = true
port = 22
filter = sshd
logpath =  /var/log/secure
maxretry = 3
bantime = -1
# 配置完成记得重启服务
systemctl restart fail2ban
# 查看状态
fail2ban-client status
# 查看sshd状态
fail2ban-client status sshd
# 取消禁用IP
fail2ban-client set sshd unbanip
```

