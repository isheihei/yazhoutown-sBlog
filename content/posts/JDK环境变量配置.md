---
title: "JDK环境变量配置"
date: 2021-01-29T23:21:58+08:00
draft: true
author: "YazhouTown"
description: "JDK windows环境配置环境变量"
tags: [环境配置, JAVA]
categories: [tips]
---

<!--more-->

### 新建JAVA_HOME变量，填写jdk安装路径：bin目录的上一级

### PATH变量添加两个：

- %JAVA_HOME%\bin
- %JAVA_HOME%\jre\bin

### 新建CLASSPATH变量：

- .;%JAVA_HOME%\lib;%JAVA_HOME%\lib\tools.jar

