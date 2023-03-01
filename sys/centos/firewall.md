# Firewall 防火墙使用方法

Centos 7 下默认的防火墙是 **Firewall**，替代了之前的 **iptables**

- 配置 Firewall

- 常用指令

```Firewall
## 查看防火墙状态
firewall-cmd --state

## 用系统命令查看防火墙状态
systemctl status firewalld

## 开启防火墙
systemctl start firewalld

## 停止防火墙
systemctl stop firewalld

## 重启防火墙
systemctl restart firewalld

## 查看已经开放的端口
firewall-cmd --list-ports

## 重载防火墙配置
firewall-cmd --reload

## 开启一个端口，重启配置生效
firewall-cmd --zone=public --add-port=80/tcp --permanent

## 关闭一个端口，重启配置生效
firewall-cmd --zone=public --remove-port=80/tcp --permanent
```
> --permanent  为永久生效，不加为单次生效，重启失效

