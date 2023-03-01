# CentOS

[CentOS](https://www.centos.org/) 是Linux发行版之一，它是来自于 Red Hat Enterprise Linux（RHEL），2014年，CentOS宣布与Red Hat合作，但CentOS将会在新的委员会下继续运作，并不受RHEL的影响

2020年12月8日，红帽公司单方面终止CentOS的开发，**Centos 7** 于2024年06月30日将停止维护，并推出 **Centos stream** 项目，CentOS 从一个稳定的下游版本，变成了一个创新的上游版本

> CentOS的创始人创建了 [Rocky Linux](https://rockylinux.org/) 项目，以继承CentOS的原始目标

 

CentOS和RHEL一样，都可以使用 [Fedora EPEL](https://zh.wikipedia.org/wiki/Fedora_(作業系統)#发行) 来补足软件

```Centos
yum install epel-release -y
```

>Enterprise Linux额外软件包（Extra Packages for Enterprise Linux，**EPEL**）是由来自Fedora Project的志愿者发起的社区力量，为了创建由高质量的附加软件组成的、用于补足RHEL和其他兼容版本的软件仓库