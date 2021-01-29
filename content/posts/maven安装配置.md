---
title: "Maven安装配置"
date: 2021-01-29T23:25:33+08:00
draft: true
author: "YazhouTown"
description: "maven windows环境下安装配置"
tags: [maven, 环境配置]
categories: [tips]
---

<!--more-->

### 解压maven压缩包

### 配置环境变量

1. 新建系统变量  MAVEN_HOME  变量值：E:\Maven\apache-maven-3.3.9

2. 编辑系统变量  Path     添加变量值： %MAVEN_HOME%\bin

### 打开cmd，输入

>mvn --version

### 查看安装配置是否成功