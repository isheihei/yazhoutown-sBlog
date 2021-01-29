---
title: "Mysql安装配置"
date: 2021-01-29T23:28:00+08:00
draft: true
author: "YazhouTown"
description: "mysql windows环境安装配置"
tags: [环境配置, mysql]
categories: [tips]
---

<!--more-->

1. 将 zip 包解压到相应的目录，这里我将解压后的文件夹放在**D:\software\mysql\mysql-8.0.22-winx64** 下。

2. 以管理员身份打开cmd命令行工具，切换目录到：**D:\software\mysql\mysql-8.0.22-winx64\bin**

3. 初始化数据库：

   > mysqld --initialize --console

4. 执行完场后，会输出root默认的随机密码，如：

   > 2021-01-18T03:55:52.326932Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: =tL5>rI40v_>

   随机密码就是：=tL5>rI40v_>

5. 安装

   > ```
   > mysqld install
   > ```

6. 启动

   > ```
   > net start mysql
   > ```

7. 输入以下命令登录数据库

   > mysql -u root -p

   需要输入密码，默认密码就是步骤4中的随机密码

8. 登陆后输入命令

   > alter user 'root'@'localhost' identified by '想要设置的密码';
   >
   > commit;

   修改密码

9. 将Mysql的bin目录配置到环境变量中

10. 属性配置文件

    ```
    jdbc.driver=com.mysql.cj.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/db0?useUnicode=true&characterEncoding=utf-8&useSSL=false&serverTimezone=GMT
    jdbc.user=root
    jdbc.password=root
    ```

