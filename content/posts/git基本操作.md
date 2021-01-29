---
title: "Git基本操作"
date: 2021-01-29T22:47:54+08:00
draft: true
author: "YazhouTown"
description: "git的使用和操作"
tags: [git, 教程]
categories: [tips]
---

<!--more-->

## 本地库操作

### 本地库初始化

- 命令：git add
- 注意：.git目录中存在的是本地库相关的子目录和文件，不要删除，也不要胡乱修改。

### 设置签名

- 形式：
  - 用户名：tom
  - Email地址：goodmoring@dsd.com
  - 作用：区分不同开发人员的身份
  - 辨析：这里的设置的签名和登录远程库（代码托管中心）的账号、密码没有任何关系。
- 命令：
  - 形目级别/仓库级别：仅在当前本地库范围内有效
    - git config user.name tom_pro
    - git config user.email goodMoring_pro@dsd.com
  - 系统用户级别：登录当前操作系统的用户范围
    - git config --global user.name tom_glb
    - git config --global user.email goodMoring_pro@dsd.com
    - 信息保存位置：~/.gitconfig![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205122253.png)
    - cd ~跳转至系统用户目录![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205122307.png)
  - 级别优先级
    - 就近原则：项目级别优先于系统用户级别，二者都有事采用项目级别的签名
    - 如果只有系统用户级别的签名，就一系统用户级别的签名为准
    - 二者都没有是不允许的

### 基本操作

- 状态查看操作：git status 查看工作区、暂存区状态
- 添加操作：git add[file name] 将工作区的“新建/修改”添加到暂存区
- 提交操作：git commit -m"commit message"[file name]  将暂存区的内容提交到本地库

### 查看历史记录

![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205122311.png)

- git log

- 多屏显示控制方式：

  - 空格向下翻页
  - b向上翻页
  - q退出

- git log --pretty=oneline

  ![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205122316.png)

- git log --oneline

  ![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205122318.png)

- git reflog

  ![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205122341.png)

### 版本前进后退

- 基于索引值操作
  - git reset --hard  【局部索引值】
  - git reset --hard  f30a827
- 使用^符号
  - git reset --hard HEAD^^
  - 注：一个^表示后退一步
- 使用~符号
  - git reset --hard HEAD~n
  - 注：表示后退n步

### reset命令的三个参数对比

- --soft

  - 仅仅移动指针

- --mixed
  - 在本地库移动HEAD指针
  - 重置暂存区

- --hard
  - 在本地库移动HEAD指针
  - 重置暂存区
  - 重置工作区

- 这就是**--soft** 和 **--hard** 的区别：**--hard** 会清空工作目录和暂存区的改动,*而 **--soft则会保留工作目录的内容，并把因为保留工作目录内容所带来的新的文件差异放进暂存区**。

- **reset三种模式区别和使用场景**

  - 区别：
    - **--hard**：重置位置的同时，直接将 **working Tree工作目录**、 **index 暂存区**及 **repository** 都重置成目标**Reset**节点的內容,所以效果看起来等同于清空暂存区和工作区。
    - **--soft**：重置位置的同时，保留**working Tree工作目录**和**index暂存区**的内容，只让**repository**中的内容和 **reset** 目标节点保持一致，因此原节点和**reset**节点之间的【差异变更集】会放入**index暂存区**中(**Staged files**)。所以效果看起来就是工作目录的内容不变，暂存区原有的内容也不变，只是原节点和**Reset**节点之间的所有差异都会放到暂存区中。
    - **--mixed（默认）**：重置位置的同时，只保留**Working Tree工作目录**的內容，但会将 **Index暂存区** 和 **Repository** 中的內容更改和reset目标节点一致，因此原节点和**Reset**节点之间的【差异变更集】会放入**Working Tree工作目录**中。所以效果看起来就是原节点和**Reset**节点之间的所有差异都会放到工作目录中。

  - 使用场景:
    - **--hard**：(1) **要放弃目前本地的所有改变時**，即去掉所有add到暂存区的文件和工作区的文件，可以执行 **git reset -hard HEAD** 来强制恢复git管理的文件夹的內容及状态；(2) **真的想抛弃目标节点后的所有commit**（可能觉得目标节点到原节点之间的commit提交都是错了，之前所有的commit有问题）。
    - **--soft**：原节点和**reset**节点之间的【差异变更集】会放入**index暂存区**中(**Staged files**)，所以假如我们之前工作目录没有改过任何文件，也没add到暂存区，那么使用**reset  --soft**后，我们可以直接执行 **git commit** 將 index暂存区中的內容提交至 **repository** 中。为什么要这样呢？这样做的使用场景是：假如我们想合并「当前节点」与「**reset**目标节点」之间不具太大意义的 **commit** 记录(可能是阶段性地频繁提交,就是开发一个功能的时候，改或者增加一个文件的时候就**commit**，这样做导致一个完整的功能可能会好多个**commit**点，这时假如你需要把这些**commit**整合成一个**commit**的时候)時，可以考虑使用**reset  --soft**来让 **commit** 演进线图较为清晰。总而言之，**可以使用--soft合并commit节点**。
    - **--mixed（默认）**：(1)使用完**reset --mixed**后，我們可以直接执行 **git add** 将這些改变果的文件內容加入 **index 暂存区**中，再执行 **git commit** 将 **Index暂存区** 中的內容提交至**Repository**中，这样一样可以达到合并**commit**节点的效果（与上面--soft合并commit节点差不多，只是多了git add添加到暂存区的操作）；(2)移除所有Index暂存区中准备要提交的文件(Staged files)，我们可以执行 **git reset HEAD** 来 **Unstage** 所有已列入 **Index暂存区** 的待提交的文件。(有时候发现add错文件到暂存区，就可以使用命令)。(3)**commit**提交某些错误代码，或者没有必要的文件也被**commit**上去，不想再修改错误再**commit**（因为会留下一个错误**commit**点），可以回退到正确的**commit**点上，然后所有原节点和**reset**节点之间差异会返回工作目录，假如有个没必要的文件的话就可以直接删除了，再**commit**上去就OK了。

### 删除文件并找回

- 前提：删除前，文件存在时的状态提交到了本地库
- 操作：git reset --hard[指针位置]
  - 删除操作已经提交到本地库：指针位置指向历史记录
  - 删除操作尚未提交到本地库（在暂存区中）:指针位置使用HEAD

### 比较文件差异

- git diff[文件名]
  - 将工作区中的文件和暂存区进行比较
- git diff[本地库中历史版本] [文件名]
  - 将工作区中的文件和本地历史库记录比较
- 不带文件名 则比较多个文件

### 分支操作

- 创建分支
  - git branch[分支名]
- 查看分支
  - git branch -v
- 切换分支
  - git checkout [分支名]
- 合并分支
  1. 切换到接受修改的分支（被合并，增加新内容）
     - git checkout[被合并的分支名]
  2. 执行merge命令
     - git merge[有新内容的分支名]
- 解决冲突
  - 编辑文件，删除特殊符号
  - 把文件修改到满意程度，保存退出
  - git add[文件名]
  - git commit -m"日志信息"
    - 注意：此时commit一定不能带具体文件名



## 远程库操作

### GitHub

#### 创建远程库

#### 推送

- git remote -v   查看当前所有远程地址别名
- git remote add[别名] [远程地址]   添加远程地址
- git push[别名] [分支名]   推送
- git push [别名] [分支名] --force 强行覆盖远程库分支
- git credential-manager uninstall 清理git远程仓库的密码缓存（当使用不同的git远程账户时需要使用）

#### 克隆

- 命令：git origin[远程地址]
- 完整的把远程库下载到本地
- 创建origin 远程地址别名
- 初始化本地库

#### 拉取

- pull = (fetch + merge)[远程库地址别名] [远程分支名]
- git fetch [远程库地址别名] [远程分支名]
- git merge[远程地址别名/远程分支名]

#### 解决冲突

- 要点
  - 如果不是基于GitHub远程库的最新版所作的修改，不能推送，必须先拉取
  - 拉取下来后吐过进入冲突状态，则按照“分支冲突解决”操作即可

