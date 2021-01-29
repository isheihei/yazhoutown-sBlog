---
title: "Centos7配置mysql"
date: 2021-01-29T23:17:20+08:00
draft: true
author: "YazhouTown"
description: "Centos7配置mysql详步骤"
tags: [环境配置, mysql]
categories: [tips]
---

<!--more-->

## 将rpm 安装包拷贝到usr/local/sql目录下

## 卸载mariadb

```shell
# rpm -qa | grep mariadb
#   rpm -e --nodeps mariadb-libs-5.5.68-1.el7.x86_64
```

![image-20201221161432398](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201221161504.png)

![image-20201221161452516](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201221161507.png)

## 解压mysql.tar包,解压后目录下会有一些rpm文件。

```shell
# tar -xvf MySQL-5.6.25-1.el6.x86_64.rpm-bundle.tar
```

## 安装Mysql.server和Mysql.client

```shell
# rp-ivh MySQL-server-5.6.25-1.el6.x86_64.rpm

# 打开/root/.mysql_secret文件，获取随机生成的密码： mAw0cco4dAVG332x
# cat /root/.mysql_secret

```

![image-20201221161816429](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201221161817.png)

![image-20201221161934314](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201221161935.png)

## 启动mysql服务

```shell
# service mysql start
```

![image-20201221162037955](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201221162039.png)

## 修改密码

```shell
mysql> set password = password('root');
```

![image-20201221162133558](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201221162135.png)

## 授权远程访问

```shell
mysql> grant all privileges on *.* to 'root' @'%' identified by 'root';
mysql> flush privileges;

# 关闭防火墙
# systemctl stop firewalld
```

![image-20201221162408274](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201221162409.png)

