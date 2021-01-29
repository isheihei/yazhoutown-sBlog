---
title: "Fabric环境配置"
author: "YazhouTown"
date: 2021-01-29T20:27:48+08:00
draft: true
tags: [Fabric,环境配置]
categories: [Fabric]
---

shell脚本基础知识以及Fabric的基本概念，以及手动部署Fabric网络的步骤。

<!--more-->

## 1. Shell脚本

### shell脚本的基本格式

- 命名格式

  - 一般命名规则：xxxx.sh（建议以.sh位后缀命名）

- 书写格式 

  ```shell
  # test.sh #是shell脚本的注释
  # 第一行如果忘了写了，使用默认的命令解析器 /bin/sh
  #! /bin/bash # 指定解析shell脚本的时候使用的命令解析器 /bin/sh也可以
  # 一系列的shell命令
  ls
  pwd
  cp
  rm
  ```

- shell脚本的执行

  ```shell
  # shell脚本编写完成之后，必须添加执行权限
   chomd u+x test.sh
  # 执行shell脚本
  ./test.sh
  或者sh test.sh
  ```

### shell脚本中的变量

- 变量的定义

  - 普通变量（本地变量）

  ```shell
  # 定义变量，定义完成，必须要赋值，=前后不能有空格
  temp=666
  # 普通变量只能在当前进程中使用
  ```

  - 环境变量

  ```shell
  # 可以理解为全局变量，在当前操作系统中可以全局访问
  # 分类
  	- 系统自带的
  		- PWD
  		- SHELL
  		- PATH
  		- HOME	
  	- 用户自定义的
  		- 将普通的变量提升为系统的环境变量
  		GOPATH=/home/usr/go/src ->普通环境变量
  		set GOPATH=/home/usr/go/src ->系统环境变量
  		export GOPATH=/home/zusr/go/src
  ```

  - 位置变量

    - $0：执行的脚本文件的名字
    - $1：第一个参数的名字

    - $2：第二个参数的名字

    - . . .

  > 执行脚本的是时候，可以给脚本传递参数，在脚本内部接受这些参数，需要使用位置变量。

  ```shell
  # 已经存在脚本test,sh
  # 执行test.sh
  ./test.sh aa bb cc dd ee ff
  ```

  - 特殊变量
    - $#：获取传递的参数的个数
    - $@：给脚本传递的所有参数
    - $?：脚本执行完成之后的状态，失败>0 or 成功=0
    - $$：脚本进程执行之后对应的进程ID
  - 普通变量取值

  ``` shell
  # 变量定义
  value=123 456 #默认以字符串处理
  value1 = "123 456"
  echo $value
  # 如何取变量的值：
  - $变量名
  - ${变量名}
  ```

  - 取得命令执行之后的结果值

  ```shell
  # 取值的两种方式：
  var=$(shell命令)
  var=`shell命令`
  ```

  - 引号的使用

  ```shell
  # 双引号
  echo "当前文件： $var"
  - 打印的时候会将var的值取出并输出
  # 单引号
  echo '当前文件：$var'
  - 将字符串原样输出
  
  ```

  

  

### 条件判断和循环

- shell脚本中的if条件判断

  ```shell
  # if 语句注意事项：
  	- if 和 []之间有一个空格
  	- [ 条件 ] : 条件的的前后都有空格
  	- 结尾：fi
  
  if [ 条件判断 ];then
  	逻辑处理
  	xxx
  	xxx
  fi
  #=======================
  if [ 条件判断 ]
  then
  	逻辑处理
  	xxx
  	xxx
  fi
  # if . . . elis . . .fi
  if [ 条件判断 ];then
  	逻辑处理
  	xxx
  	xxx
  elif [ 条件判断 ];then
  	逻辑处理
  	xxx
  	xxx
  . . .
  else
  	逻辑处理
  fi
  ```

  

- shell条件测试

  - -a and
  - -o or

- shell  脚本 for循环

  ```shell
  #shell中的循环 for/while
  # 语法：for 变量 in 集合; do;done
  list='ls'
  for var in $list;do
  	echo "当前文件：$var"
  done
  ```

### shell脚本中的函数

```shell
# 没有函数修饰，没有参数，没有返回值
# 格式
funcName(){
	# 得到第一个参数
	arg1=$1
	# 得到第二个参数
	arg2=$2
	函数体//shell命令+逻辑循环和判断
}
#没有参数列表，但是可以传参
# 函数调用
funcName aa bb cc dd
# 函数调用之后的状态：
0：调用成功
非0：失败
```



## 2. Fbric环境搭建（Ubuntu)

### 前置安装（Git和curl)

打开终端，不需要root。

```shell
# 更新软件源
sudo apt update
# 安装git
sudo apt install git
# 安装curl
sudo apt install curl
```

### 安装docker-ce

**step1.更新源，安装相应的依赖包**

```shell
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
```

**Step2 安装Docker镜像**

```shell
 # 安装国内阿里Docker CE镜像
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

**Step3. 安装稳定版的repository**

```shell
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

**step4.安装Docker CE**

```shell
sudo apt-get update
sudo apt-get install docker-ce
```

**step5. 验证Docker CE是否成功安装**

方式1）以root身份运行

```shell
sudo docker run hello-world
```

方式2）以普通用户运行

```shell
# 2. 创建名为docker的组
sudo groupadd docker

# 3. 将当前用户加入到docker组中
sudo usermod -aG docker $USER

# 4. 注销,如果远程登录先断开连接在重新连接，使得usermod修改生效，这一步非常重要

# 5. 验证
docker run hello-world
```



### 安装docker-compose

```shell
# 安装
sudo apt install docker-compose
# 安装成功后查看版本
docker-compose --version
```

![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205121618.png)

### 安装go

[国内镜像地址](https://studygolang.com/dl)

在这个网站中下载go安装包

```shell
# 解压tar包到/usr/local 注意：go压缩包文件名根据下载的文件名修改
sudo tar zxvf go1.15.2.linux-amd64.tar.gz -C /usr/local
# 3. 创建Go目录
mkdir $HOME/go
# 4. 用vi打开~./bashrc，配置环境变量
vim ~/.bashrc
# 5. 增加下面的环境变量，保存退出
	export GOROOT=/usr/local/go
    export GOPATH=$HOME/go
    export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
# 6. 使环境变量立即生效, 一些命令二选一
source ~/.bashrc  
 . ~/.bashrc
# 7. 检测go是否安装好
 go version
```

![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205121808.png)



### 下载Fabric和Fabric-samples

```shell
# 配置镜像加速器 阿里云->容器镜像服务
# 针对Docker客户端版本大于 1.10.0 的用户

# 您可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://srejsoa5.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

# 创建hyperledger文件夹并进入
mkdir -p ~/go/src/github.com/hyperledger
cd ~/go/src/github.com/hyperledger

#下载fabric 源码
git clone https://gitee.com/snitro/fabric.git
cd ./fabric

#切换版本
git checkout v2.0.0

# 下载镜像 直接执行脚本即可
sudo ~/go/src/github.com/hyperledger/fabric/scripts/bootstrap.sh

# 接下来配置环境变量
#环境变量配置路径务必按照自己本身的fabric-samples/bin路径配置
vi ~/.bashrc

export PATH=$PATH:$GOROOT/bin:$GOPATH/bin:/home/heihei/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin


source ~/.bashrc

```

### 启动first-network

``` shell
$ cd ~/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/test-network
$ ./network.sh up
$ docker ps
#成功！！！！！
```

![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205121917.png)

## 3. Fabric基本概念

### 成员管理

- 会员注册
  - 注册成功一个账号得到的不是用户名和密码
  - 使用证书作为身份认证的标志
- 身份保护
- 交易审计
- 内容保密
  - 可以多条区块链，通过通道来区分的。

### 账本管理

- 区块链
  - 保存所有的交易记录
- 世界状态
  - 数据的最新状态
  - 数据存储在当前节点的数据库中
    - 自带的迷人数据库：levelDB。也可以使用couchdb
      - 以键值对的方式进行存储

### 交易管理

- 部署交易
  - 部署的是链码，就是给节点安装链码 -chaincode
  - 调用交易
    - invoke

### 智能合约

- 一段代码，处理网路成员所同意的业务逻辑
- 真正实现了链码和账本的分离（逻辑和数据分离）

###  组织

> 在 Fabric中一个组织里面都有什么？
>
> - 用户
> - 进行数据处理的节点
>   - put -> 谢数据到区块链中
>   - get -> 数据查询

### 节点

- client

  > 进行交易管理（cli, node sdk, java sdk）
  >
  > - cli -> 通过linux的命令进行通过，使用的是shell命令对象数据进行提交和查询
  > - node.js -> 通过node.js实现一个客户端
  > - java ->通过java api实现一个客户端
  > - go也可以

- peer 

  >  存储和同步账本数据
  >
  >  - 用户通过客户端工具对数据进行 put 操作，数据写入到一个节点里面
  >  - 数据同步是fabroc框架实现的

- orderer

  > 排序和分发交易
  >
  > - 为什么要排序？
  >   - 解决双花问题
  >   - 每发起一笔交易都会在orderer节点进行排序
  > - 交易数据先进行打包，然后写入到区块中

### 通道（chanel）

>通道是有共识服务提供的一种通讯机制，将peer 和orderer连接在一起，形成一个个具有保密性的通讯链路（虚拟），实现了业务隔离的要求。
>
>一个peer节点是可以同时加入到不同的通道中的
>
>peer 节点每加入到一个新的通道，存储数据的区块链需要添加一条，只要加入到通道中就可以拥有这个通道中的数据，每个通道对应一个区块链。

### 交易流程

> 要完成交易，这笔交易必须要有背书策略，假设：
>
> - 组织A中的成员必须同意
> - 组织B中的成员也必须同意
>
> 1. Application/SDK:充当的是客户端的角色
>    - 写数据
> 2. 客户端发起一个提案，给到peer节点
>    - 发送给组织A和组织B中的节点
>
> 3. peer节点将交易进行预演，会得到一个结果
> 4. peer节点将交易结果发送给客户端
>    - 如果模拟交易失败，整个交易流程终止
>    - 成功，继续。
> 5. 客户端将交易提交给排序节点
> 6. 排序节点对交易进行打包
> 7. orderer节点将打包数据发送给peer, peer节点将数据写入区块中
>    1. 打包数据的发送，这不是实时的
>    2. 有设定条件，在配置文件中
>
> 背书策略：
>
> - 要完成一笔交易，这笔交易完成过程叫背书

## 4. Fabric核心模块

| 模块名称       | 功能                                         |
| -------------- | -------------------------------------------- |
| peer           | 主节点模块，负责存储区块链数据，运行维护链码 |
| orderer        | 交易打包，排序模块                           |
| cryptogen      | 组织和证书生成模块                           |
| configtxgen    | 区块和交易生成模块                           |
| configtxklator | 区块和交易解析模块                           |

> 五个模块中，peer和orderer舒徐系统模块，另外三个属于工具模块。工具模块负责证书文件、区块链创世块、通道创世块等线管文件和证书的生成工作，但是工具模块不参与系统的运行。peer模块和orderer模块作为系统模块是Fabric的核心模块，启动以后会以守护进程的方式在系统后台长期运行。
>
> Fabric的5个核心模块都是基于命令行的方式运行的，目前Fabric没有为这些模块提供相关的图形界面，因此想要熟练使用Fabric的这些核心模块，必须熟悉这些模块的命令选项。

## 5. 手动部署Fabric网络

### 生成Fabric证书

```shell
$ cryptogen --help
$ cryptogen showtemplate
$ cryptogen generate --config=crypto-config.yaml
```

**配置文件模板**

> 注意：yaml 文件不能有制表符！！！！！！！！！！！

```yaml
# ---------------------------------------------------------------------------
# "OrdererOrgs" - Definition of organizations managing orderer nodes
# ---------------------------------------------------------------------------
OrdererOrgs:	# 排序节点组织信息
  # ---------------------------------------------------------------------------
  # Orderer
  # ---------------------------------------------------------------------------
  - Name: Orderer	# 排序节点组织的名字
    Domain: example.com	# 根域名，排序节点组织的根域名

    # ---------------------------------------------------------------------------
    # "Specs" - See PeerOrgs below for complete description
    # ---------------------------------------------------------------------------
    Specs:
      - Hostname: orderer	# 访问这台orderer对应的域名为：orderer.example.com
      - Hostname: orderer2	# 访问这台orderer对应的域名为：orderer2.example.com
      
# ---------------------------------------------------------------------------
# "PeerOrgs" - Definition of organizations managing peer nodes
# ---------------------------------------------------------------------------
PeerOrgs:
  # ---------------------------------------------------------------------------
  # Org1
  # ---------------------------------------------------------------------------
  - Name: Org1	#  第一个组织的名字，自己指定
    Domain: org1.example.com	# 访问第一个组织用到的根域名
    EnableNodeOUs: false 		# 是否支持node.js
    Template:					# 模板，根据默认的规则生成2个peer存储数据节点
      Count: 2 # peer0.Org1.ecample.com     peer1.Org1.ecample.com
    Users:		# 创建的普通用户的个数，对应的就是client
      Count: 1 

  # ---------------------------------------------------------------------------
  # Org2: See "Org1" for full specification
  # ---------------------------------------------------------------------------
  - Name: Org2
    Domain: org2.example.com
    EnableNodeOUs: false
    Template:
      Count: 1
    Users:
      Count: 1

```

> 上面使用的域名，在真是的生产环境中需要注册备案；测试环境自己起名就可以。
>
> template  和specs区别：
>
> - template自动生成hostname
>   -  Template:					# 模板，根据默认的规则生成2个peer存储数据节点
>          Count: 2 # peer0.Org1.ecample.com     peer1.Org1.ecample.com
>        Users:		# 创建的普通用户的个数
>          Count: 1 
> - specea自己指定
>   -  Specs:
>          - Hostname: orderer	# 访问这台orderer对应的域名为：orderer.example.com
>                - Hostname: orderer2	# 访问这台orderer对应的域名为：orderer2.example.com

### 创始块文件和通道文件的生成

```shell
$ configtxgen --help
# 需要一个配置文件configtx.yaml
```

> 从tets-network/configtx目录下拷贝configtx.yaml
>
> 

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

---
################################################################################
#
#   Section: Organizations
#
#   - This section defines the different organizational identities which will
#   be referenced later in the configuration.
#
################################################################################
Organizations:			# 固定不能改
    - &OrdererOrg 		# 排序节点组织，自己起个名字 &代表对变量OredrerOrg引用
        Name: OrdererOrg # 排序节点的组织名字       				
        ID: OrdererMSP	# 排序节点的组织ID
        MSPDir: crypto-config/ordererOrganizations/itcast.com/msp # 相对目录
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('OrdererMSP.member')"
            Writers:
                Type: Signature
                Rule: "OR('OrdererMSP.member')"
            Admins:
                Type: Signature
                Rule: "OR('OrdererMSP.admin')"

        OrdererEndpoints:
            - orderer.example.com:7050
    - &org_go				#第一个组织，名字自己起
        Name: OrgGoMSP	# 第一个组织的名字
        ID: OrgGoMSP		# 第一个组织的ID
        MSPDir: crypto-config/peerOrganizations/orggo.itcast.com/msp
        
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('Org1MSP.admin', 'Org1MSP.peer', 'Org1MSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('Org1MSP.admin', 'Org1MSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('Org1MSP.admin')"
            Endorsement:
                Type: Signature
                Rule: "OR('Org1MSP.peer')"                
        AnchorPeers: 						# 锚节点
            - Host: peer0.orggo.itcast.com	# 指定一个peer节点的域名
              Port: 7051					# 端口固定，不要改
    - &org_cpp
        Name: OrgCppMSP
        ID: OrgCppMSP
        MSPDir: crypto-config/peerOrganizations/orgcpp.itcast.com/msp
        Policies:
            Readers:
                Type: Signature
                Rule: "OR('Org2MSP.admin', 'Org2MSP.peer', 'Org2MSP.client')"
            Writers:
                Type: Signature
                Rule: "OR('Org2MSP.admin', 'Org2MSP.client')"
            Admins:
                Type: Signature
                Rule: "OR('Org2MSP.admin')"
            Endorsement:
                Type: Signature
                Rule: "OR('Org2MSP.peer')"

        AnchorPeers:
            - Host: peer0.orgcoo.itcast.com
              Port: 7051

################################################################################
#
#   SECTION: Capabilities
#
#   - This section defines the capabilities of fabric network. This is a new
#   concept as of v1.1.0 and should not be utilized in mixed networks with
#   v1.0.x peers and orderers.  Capabilities define features which must be
#   present in a fabric binary for that binary to safely participate in the
#   fabric network.  For instance, if a new MSP type is added, newer binaries
#   might recognize and validate the signatures from this type, while older
#   binaries without this support would be unable to validate those
#   transactions.  This could lead to different versions of the fabric binaries
#   having different world states.  Instead, defining a capability for a channel
#   informs those binaries without this capability that they must cease
#   processing transactions until they have been upgraded.  For v1.0.x if any
#   capabilities are defined (including a map with all capabilities turned off)
#   then the v1.0.x peer will deliberately crash.
#
################################################################################
Capabilities:	# 默认，不用动。
    Channel: &ChannelCapabilities
        V2_0: true
    Orderer: &OrdererCapabilities
        V2_0: true
    Application: &ApplicationCapabilities
        V2_0: true

################################################################################
#
#   SECTION: Application
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for application related parameters
#
################################################################################
Application: &ApplicationDefaults	# 默认，不动。
    Organizations:
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        LifecycleEndorsement:
            Type: ImplicitMeta
            Rule: "MAJORITY Endorsement"
        Endorsement:
            Type: ImplicitMeta
            Rule: "MAJORITY Endorsement"
    Capabilities:
        <<: *ApplicationCapabilities
################################################################################
#
#   SECTION: Orderer
#
#   - This section defines the values to encode into a config transaction or
#   genesis block for orderer related parameters
#
################################################################################
Orderer: &OrdererDefaults # 共识机制==排序算法
    # Orderer Type: The orderer implementation to start
    OrdererType: solo	# 共识机制solo,kafuka,etcdraft. . .
    Addresses:			  	# order节点地址
        - orderer.itcast.com:7050	# 端口不改
    BatchTimeout: 2s	# 多长时间产生一个区块
    BatchSize:
        MaxMessageCount: 10	# 交易的最大条数，数量达到之后会产生区块，建议100左右
        AbsoluteMaxBytes: 99 MB	# 数据量达到这个值，会产生一个区块，32M/64M
        # BatchTimeout MaxMessageCount AbsoluteMaxBytes
        # 以上三个条件，只要一个条件满足，就产生区块！
        PreferredMaxBytes: 512 KB
    Organizations:
    Policies:
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"
        # BlockValidation specifies what signatures must be included in the block
        # from the orderer for the peer to validate it.
        BlockValidation:
            Type: ImplicitMeta
            Rule: "ANY Writers"
################################################################################
#
#   CHANNEL
#
#   This section defines the values to encode into a config transaction or
#   genesis block for channel related parameters.
#
################################################################################
Channel: &ChannelDefaults
    # Policies defines the set of policies at this level of the config tree
    # For Channel policies, their canonical path is
    #   /Channel/<PolicyName>
    Policies:
        # Who may invoke the 'Deliver' API
        Readers:
            Type: ImplicitMeta
            Rule: "ANY Readers"
        # Who may invoke the 'Broadcast' API
        Writers:
            Type: ImplicitMeta
            Rule: "ANY Writers"
        # By default, who may modify elements at this config level
        Admins:
            Type: ImplicitMeta
            Rule: "MAJORITY Admins"

    # Capabilities describes the channel level capabilities, see the
    # dedicated Capabilities section elsewhere in this file for a full
    # description
    Capabilities:
        <<: *ChannelCapabilities

################################################################################
#
#   Profile
#
#   - Different configuration profiles may be encoded here to be specified
#   as parameters to the configtxgen tool
#
################################################################################
Profiles:	# 不能改
    ItcastOrgsOrdererGenesis:	# 系统的通道及创世块配置
        <<: *ChannelDefaults
        Orderer:
            <<: *OrdererDefaults
            Organizations:
                - *OrdererOrg
            Capabilities:
                <<: *OrdererCapabilities
        Consortiums:
            SampleConsortium:	# 联盟的名字，可以改
                Organizations:
                    - *org_go
                    - *org_cpp
    ItcastOrgsChannel: #  应用通道模板
        Consortium: SampleConsortium # 联盟名字，对应上面的哪个名字
        <<: *ChannelDefaults
        Application:
            <<: *ApplicationDefaults
            Organizations:
                - *org_go
                - *org_cpp
            Capabilities:
                <<: *ApplicationCapabilities
```

[关于policy](https://hyperledger-fabric.readthedocs.io/en/release-2.0/policies/policies.html)

生成创始块文件和通道文件

> -profile后面的参数从configtx.yaml中的profiles中找

 ```shell
 # sys_channel是系统通道名称，自己起、
 $  configtxgen -profile ItcastOrgsOrdererGenesis -channelID sys-channel -outputBlock ./genesis.block
 
 # itcastchannel 通道名，如过没有指定channelID，默认的通道名就叫mychannel
 $ configtxgen -profile ItcastOrgsChannel -outputCreateChannelTx channel.tx -channelID itcastchannel
 
 ```

生成锚节点更新文件(该操作是可选的)

- 每个组织对应一个锚节点文件

```shell
# Go组织
$ configtxgen -profile ItcastOrgsChannel -outputAnchorPeersUpdate GoMSPanchors.tx -channelID itchannel -asOrg OrgGoMSP

# Cpp组织
configtxgen -profile ItcastOrgsChannel -outputAnchorPeersUpdate CppMSPanchors.tx -channelID itchannel -asOrg OrgCppMSP
```

> 执行后会生成四个文件，创建一个文件夹，命名为channel-artifacts，将这四个文件拷贝进去。



### docker_compose文件的编写

> docker-compose.yaml

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

version: '2'

volumes:
  orderer.itcast.com:
  peer0.orggo.itcast.com:
  peer1.orggo.itcast.com:
  peer0.orgcpp.itcast.com:
  peer1.orgcpp.itcast.com:

networks:
  byfn:

services:

  orderer.itcast.com:
    extends:
      file:   base/docker-compose-base.yaml
      service: orderer.itcast.com
    container_name: orderer.itcast.com
    networks:
      - byfn

  peer0.orggo.itcast.com:
    container_name: peer0.orggo.itcast.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.orggo.itcast.com
    networks:
      - byfn

  peer1.orggo.itcast.com:
    container_name: peer1.orggo.itcast.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.orggo.itcast.com
    networks:
      - byfn

  peer0.orgcpp.itcast.com:
    container_name: peer0.orgcpp.itcast.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer0.orgcpp.itcast.com
    networks:
      - byfn

  peer1.orgcpp.itcast.com:
    container_name: peer1.orgcpp.itcast.com
    extends:
      file:  base/docker-compose-base.yaml
      service: peer1.orgcpp.itcast.com
    networks:
      - byfn

  cli:
    container_name: cli
    image: hyperledger/fabric-tools:latest
    tty: true
    stdin_open: true
    environment:
      - GOPATH=/opt/gopath 	# 客户端docker容器启动之后，go的工作目录，不需要修改
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock # 套接字文件，不需要修改
      - CORE_LOGGING_LEVEL=INFO 	# 日志级别,critical,error,warning,notice,info,debug从高到低
      - CORE_PEER_ID=cli 	# 当前客户端节点的ID即名字，自己指定即可
      - CORE_PEER_ADDRESS=peer0.orggo.itcast.com:7051 	# 连接peer节点的地址
      - CORE_PEER_LOCALMSPID=OrgGoMSP 	# 连接peer节点所属的组织
      - CORE_PEER_TLS_ENABLED=true 	# 通信是否使用TLS加密,true时下面的3个文件才生效 
      # 通信时使用的TLS证书, .crt证书, .key密钥
      # ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/  volumes挂载后目录直接从crypto/开始，之前的路径不需要管了。
      - CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orggo.itcast.com/peers/peer0.orggo.itcast.com/tls/server.crt
      # 通信时使用的TLS密钥 
      - CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orggo.itcast.com/peers/peer0.orggo.itcast.com/tls/server.key
      # TLS根证书文件
      - CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orggo.itcast.com/peers/peer0.orggo.itcast.com/tls/ca.crt
      # 客户端身份,连接网络访问节点,需要账号
      # 普通账号: 交易(写数据),查询(读数据)
      # 管理员账号: 交易(写数据),查询(读数据),创建通道,让某个节点加入到通道,安装链码,链码初始化
      - CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/orggo.itcast.com/users/Admin@orggo.github.com/msp
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer # 不改
    command: /bin/bash	# 不改
    volumes: 
        - /var/run/:/host/var/run/
        - ./chaincode/:/opt/gopath/src/github.com/chaincode
        - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
    depends_on: 	# 启动顺序
      - orderer.itcast.com
      - peer0.orggo.itcast.com
      - peer1.orggo.itcast.com
      - peer0.orgcpp.itcast.com
      - peer1.orgcpp.itcast.com
    networks:
      - byfn

```

>  base/docker-compose-base.yaml

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

version: '2'

services:

  orderer.itcast.com:
    container_name: orderer.itcast.com
    image: hyperledger/fabric-orderer:latest
    environment:
      - ORDERER_GENERAL_LOGLEVEL=INFO	# 日志级别
      - ORDERER_GENERAL_LISTENADDRESS=0.0.0.0 # 监听本机地址,如果是0会自动读取网卡,识别实际的IP
      - ORDERER_GENERAL_GENESISMETHOD=file # 生成创始区块的数据来源，来源于文件
      - ORDERER_GENERAL_GENESISFILE=/var/hyperledger/orderer/orderer.genesis.block # 来自具体哪个文件，映射路径
      - ORDERER_GENERAL_LOCALMSPID=OrdererMSP # 当前节点属于哪个组织ID
      - ORDERER_GENERAL_LOCALMSPDIR=/var/hyperledger/orderer/msp	# 映射路径
      # enabled TLS
      - ORDERER_GENERAL_TLS_ENABLED=true
      - ORDERER_GENERAL_TLS_PRIVATEKEY=/var/hyperledger/orderer/tls/server.key
      - ORDERER_GENERAL_TLS_CERTIFICATE=/var/hyperledger/orderer/tls/server.crt
      - ORDERER_GENERAL_TLS_ROOTCAS=[/var/hyperledger/orderer/tls/ca.crt]
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric
    command: orderer
    volumes:
    - ../channel-artifacts/genesis.block:/var/hyperledger/orderer/orderer.genesis.block
    - ../crypto-config/ordererOrganizations/itcast.com/orderers/orderer.itcast.com/msp:/var/hyperledger/orderer/msp
    - ../crypto-config/ordererOrganizations/itcast.com/orderers/orderer.itcast.com/tls/:/var/hyperledger/orderer/tls
    - orderer.itcast.com:/var/hyperledger/production/orderer
    ports:
      - 7050:7050 #端口映射，前面的可以改，后面的不可以

  peer0.orggo.itcast.com:
    container_name: peer0.orggo.itcast.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer0.orggo.itcast.com 	# 指定当前peer节点的名字
      - CORE_PEER_ADDRESS=peer0.orggo.itcast.com:7051 	# 当前peer节点的访问地址
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.orggo.itcast.com:7051 	# 启动节点后向哪些节点发起gossip链接，以加入网络。启动的是时候，一般写自己就行。
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.orggo.itcast.com:7051	# 设置当前节点是否被外部感知, 如果想, 就指定自己的地址, 如果不想不用指定了
      - CORE_PEER_LOCALMSPID=OrgGoMSP 	# 当前peer所属的组织的ID
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/orggo.itcast.com/peers/peer0.orggo.itcast.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/orggo.itcast.com/peers/peer0.orggo.itcast.com/tls:/etc/hyperledger/fabric/tls
        - peer0.orggo.itcast.com:/var/hyperledger/production
    ports:
      - 7051:7051
      - 7053:7053

  peer1.orggo.itcast.com:
    container_name: peer1.orggo.itcast.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.orggo.itcast.com
      - CORE_PEER_ADDRESS=peer1.orggo.itcast.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.orggo.itcast.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.orggo.itcast.com:7051
      - CORE_PEER_LOCALMSPID=OrgGoMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/orggo.itcast.com/peers/peer1.orggo.itcast.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/orggo.itcast.com/peers/peer1.orggo.itcast.com/tls:/etc/hyperledger/fabric/tls
        - peer1.orggo.github.com:/var/hyperledger/production

    ports:
      - 8051:7051
      - 8053:7053

  peer0.orgcpp.itcast.com:
    container_name: peer0.orgcpp.itcast.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer0.orgcpp.itcast.com
      - CORE_PEER_ADDRESS=peer0.orgcpp.itcast.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer0.orgcpp.itcast.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer0.orgcpp.itcast.com:7051
      - CORE_PEER_LOCALMSPID=OrgCppMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/orgcpp.itcast.com/peers/peer0.orgcpp.itcast.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/orgcpp.itcast.com/peers/peer0.orgcpp.itcast.com/tls:/etc/hyperledger/fabric/tls
        - peer0.orgcpp.itcast.com:/var/hyperledger/production
    ports:
      - 9051:7051
      - 9053:7053

  peer1.orgcpp.itcast.com:
    container_name: peer1.orgcpp.itcast.com
    extends:
      file: peer-base.yaml
      service: peer-base
    environment:
      - CORE_PEER_ID=peer1.orgcpp.itcast.com
      - CORE_PEER_ADDRESS=peer1.orgcpp.itcast.com:7051
      - CORE_PEER_GOSSIP_EXTERNALENDPOINT=peer1.orgcpp.itcast.com:7051
      - CORE_PEER_GOSSIP_BOOTSTRAP=peer1.orgcpp.itcast.com:7051
      - CORE_PEER_LOCALMSPID=OrgCppMSP
    volumes:
        - /var/run/:/host/var/run/
        - ../crypto-config/peerOrganizations/orgcpp.itcast.com/peers/peer1.orgcpp.itcast.com/msp:/etc/hyperledger/fabric/msp
        - ../crypto-config/peerOrganizations/orgcpp.itcast.com/peers/peer1.orgcpp.itcast.com/tls:/etc/hyperledger/fabric/tls
        - peer1.orgcpp.itcast.com:/var/hyperledger/production
    ports:
      - 10051:7051
      - 10053:7053
```

> base/peer-base.yaml

```yaml
# Copyright IBM Corp. All Rights Reserved.
#
# SPDX-License-Identifier: Apache-2.0
#

version: '2'

services:
  peer-base:
    image: hyperledger/fabric-peer:latest
    environment:
      - CORE_VM_ENDPOINT=unix:///host/var/run/docker.sock	# 套接字地址，不需要修改
      # the following setting starts chaincode containers on the same
      # bridge network as the peers
      # https://docs.docker.com/compose/networking/
      # 要指定docker加入的网络名.举例:如果是Demo,得到的是demo_byfn
      - CORE_VM_DOCKER_HOSTCONFIG_NETWORKMODE=itcast.byfn	# 当前节点属于哪个网络
      - CORE_LOGGING_LEVEL=INFO
      - CORE_PEER_TLS_ENABLED=true 
      - CORE_PEER_GOSSIP_USELEADERELECTION=true 	# 是否自动选举leader节点
      - CORE_PEER_GOSSIP_ORGLEADER=false 	# 指定当前节点是不是leader节点
      - CORE_PEER_PROFILE_ENABLED=true	 # peer节点启动后PROFILE进程是否启动
      - CORE_PEER_TLS_CERT_FILE=/etc/hyperledger/fabric/tls/server.crt
      - CORE_PEER_TLS_KEY_FILE=/etc/hyperledger/fabric/tls/server.key
      - CORE_PEER_TLS_ROOTCERT_FILE=/etc/hyperledger/fabric/tls/ca.crt
    working_dir: /opt/gopath/src/github.com/hyperledger/fabric/peer
    command: peer node start
```

### docker-compose启动

> 启动docker-compose使用的配置文件 docker-compose.yaml
>
> 被docker-compose.yaml依赖的文件 base/docker-compose-base
>
> 被base/docker-compose-base依赖的文件 base/peer.base.yaml

```shell
# 显示所有容器
$ docker-compose ps -a
# 关闭并移除所有容器
$ docker-compose down
# 启动
$ docker-compose up -d
```

启动成功：

``` shell
         Name                 Command       State                       Ports                     
--------------------------------------------------------------------------------------------------
cli                       /bin/bash         Up                                                    
orderer.itcast.com        orderer           Up      0.0.0.0:7050->7050/tcp                        
peer0.orgcpp.itcast.com   peer node start   Up      0.0.0.0:9051->7051/tcp, 0.0.0.0:9053->7053/tcp
peer0.orggo.itcast.com    peer node start   Up      0.0.0.0:7051->7051/tcp, 0.0.0.0:7053->7053/tcp
peer1.orgcpp.itcast.com   peer node start   Up      0.0.0.0:10051->7051/tcp,                      
                                                    0.0.0.0:10053->7053/tcp                       
peer1.orggo.itcast.com    peer node start   Up      0.0.0.0:8051->7051/tcp, 0.0.0.0:8053->7053/tcp

```

![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205121937.png)

### 通过客户端操作各个节点

客户端对Peer节点的操作流程：

- ##### 创建通道，通过客户端节点来完成

  ``` shell
  # 在宿主机里面
           Name                 Command       State                       Ports                     
  --------------------------------------------------------------------------------------------------
  cli                       /bin/bash         Up                                                    
  orderer.itcast.com        orderer           Up      0.0.0.0:7050->7050/tcp                        
  peer0.orgcpp.itcast.com   peer node start   Up      0.0.0.0:9051->7051/tcp, 0.0.0.0:9053->7053/tcp
  peer0.orggo.itcast.com    peer node start   Up      0.0.0.0:7051->7051/tcp, 0.0.0.0:7053->7053/tcp
  peer1.orgcpp.itcast.com   peer node start   Up      0.0.0.0:10051->7051/tcp,                      
                                                      0.0.0.0:10053->7053/tcp                       
  peer1.orggo.itcast.com    peer node start   Up      0.0.0.0:8051->7051/tcp, 0.0.0.0:8053->7053/tcp
  #-------------------------------------------------------------------------------#
  # 进入客户端的容器中
  $ docker exec -it cli /bin/bash
  # 创建 通道
  $ peer channel create -o orderer节点地址：端口 -c 通道名 -f 通道文件 --tls true --cafile orderer节点pem格式的证书文件
  # -orderer节点地址：可以是IP地址或者域名
  # -orderedr节点监听的是7050端口
  # pem文件：
  #	（宿主机中的地址）：itcast/crypto-config/ordererOrganizations/itcast.com/msp/tlscacerts
  # 	容器中的地址（绝对地址）：/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/itcast.com/msp/tlscacerts
  $ peer channel create -o orderer0.example.com:7050 -c businesschannel -f ./channel-artifacts/businesschannel.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer0.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
  # 执行成功狗在当前工作目录下生成一个文件:通道名.block(itcastchannel.block)
  
  ```

  ![](https://gitee.com/isheihei/imagesRepo/raw/master/img/20201205121945.png)

- ##### 将每个组织的每个节点都加入通道中（通过客户端来完成）

  - 一个客户端同时只能连接一个peer节点

  ``` shell
  # 将当前的客户端连接的节点加入channel中 
  $ peer channel join -b businesschannel.block
  ```

  - 切换环境变量，来切换不同的节点.切换一次执行一次上面的命令

  ``` shell
  #  第一个节点 Go组织的peer0
  export CORE_PEER_ADDRESS=peer0.org1.example.com:7051
  export CORE_PEER_LOCALMSPID=Org1MSP
  export CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.crt
  export  CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/server.key
  export  CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
  export  CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
  
  $ peer channel join -b businesschannel.block
  
  #  第二个节点 Go组织的peer1
  export CORE_PEER_ADDRESS=peer1.org1.example.com:7051
  export CORE_PEER_LOCALMSPID=Org1MSP
  export CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/server.crt
  export  CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/server.key
  export  CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/ca.crt
  export  CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
  
  $ peer channel join -b businesschannel.block
  
  #  第一个节点 Cpp组织的peer0
  export CORE_PEER_ADDRESS=peer0.org2.example.com:7051
  export CORE_PEER_LOCALMSPID=Org2MSP
  export CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/server.crt
  export  CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/server.key
  export  CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
  export  CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
  
  $ peer channel join -b businesschannel.block
  
  #  第二个节点 Cpp组织的peer1
  export CORE_PEER_ADDRESS=peer1.org2.example.com:7051
  export CORE_PEER_LOCALMSPID=Org2MSP
  export CORE_PEER_TLS_CERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/server.crt
  export  CORE_PEER_TLS_KEY_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/server.key
  export  CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/ca.crt
  export  CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
  
  $ peer channel join -b businesschannel.block
  ```

- 给每个peer节点安装智能合约（链代码：go, node.js,  java）

  - 将智能合约拷贝到chaincode/目录

  - [打包智能合约](https://blog.csdn.net/bean_business/article/details/108609208)

    - 使用Go模块安装链码依赖项。依赖项列在fabcar/go目录中的go.mod文件中

    - 要安装智能合约依赖项，请从`fabcar/go`目录运行以下命令

    ``` shell
    GO111MODULE=on go mod vendor
    ```

    ```  shell
    # 打包链码
    $ peer lifecycle chaincode package fabcar.tar.gz --path /opt/gopath/src/github.com/chaincode/go --lang golang --label testchaincode
    
    #如果打包出错，在cli中执行一下的命令即可
      go env -w GO111MODULE=on
    go env -w GOPROXY=https://goproxy.cn,direct
    
    ```


  - 在我们打包Fabcar智能合约之后，我们可以在我们的节点上安装链码。链码需要安装在每一个背书交易的节点上。

  ``` shell
  $ peer lifecycle chaincode install fabcar.tar.gz
  ```

- 批准链码定义 (此步骤需要在每个组织中的任意节点执行一次)

  - 安装链码包后，需要批准组织的链码定义。定义包括链码管理的重要参数，如名称、版本和链码背书策略。默认情况下，此策略要求大多数通道成员在链码可用于通道之前需要批准它。因为我们在通道上只有两个组织，而且大多数是2，所以我们需要批准Fabcar的链码定义OrgGp和OrgCpp。如果一个组织已在其节点上安装了链码，则需要在其组织批准的链码定义中包含package ID。包ID用于将节点上安装的链码与已批准的链码定义相关联，并允许组织使用链码来背书交易。您可以使用以下命令来查询节点，从而找到链码的包ID。

    ``` shell
    $ peer lifecycle chaincode queryinstalled
    ```

  - 包ID是链码标签和链码二进制文件的哈希的组合。每个节点都将生成相同的包ID。您应该看到类似于以下内容的输出：	

  ``` shell
    Installed chaincodes on peer:
  Package ID: fabcar_1:9c81ee466b3a85ade7e664483ce0e80c82939a12a8ab3f2a9268bd38d488bb82, Label: fabcar_1
  ```

    - 链码是在组织级别批准的，因此命令只需要针对一个节点。批准通过gossip分发给组织内的其他节点。使用以下命令批准链码定义：

      ``` shell
      $ peer lifecycle chaincode approveformyorg  --channelID businesschannel --name fabcar --version 1.0 --package-id testchaincode:8f65b95b91490ee37866b537b9e42849050eafeda0150911dac7b8a27ef22e5e --sequence 2 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer0.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
      ```

- 将链码定义提交到通道

  - 在足够多的组织批准了链码定义后，一个组织可以将链码定义提交到通道。如果大多数通道成员已批准该定义，则提交交易将成功，并且在通道上实现链码定义中约定的参数。( 此步骤需要在刚才提交链码批准的某一节点中进行)

    ``` shell
    peer lifecycle chaincode checkcommitreadiness --channelID businesschannel --name fabcar --version 1.0 --sequence 2 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer0.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --output json
    ```

    输出结果：

    ``` shell
    {
    	"approvals": {
    		"OrgCppMSP": true,
    	"OrgGoMSP": true
    	}
    }
    ```
    
  - 由于作为通道成员的两个组织都批准了相同的参数，因此链码定义已准备好提交给通道。可以使用以下命令将链码定义提交到通道。commit命令还需要由组织管理员提交。

  ```shell
  peer lifecycle chaincode commit  -o orderer0.example.com:7050 --channelID businesschannel --name fabcar --version 1.0 --sequence 2 --tls --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer0.example.com/msp/tlscacerts/tlsca.example.com-cert.pem --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
  ```

    - 可以使用一下命令来确认chaincode定义已提交到通道。

      ```shell
      peer lifecycle chaincode querycommitted --channelID businesschannel --name fabcar --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer0.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
      ```

      输出:

    ``` shell
      Committed chaincode definition for chaincode 'fabcar' on channel 'itcastchannel':
  Version: 1.0, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc, Approvals: [OrgCppMSP: true, OrgGoMSP: true]
    ```

- 调用链码

  - 在将链码定义提交给通道后，链码将从连接到安装链码的通道的节点开始。Fabcar链码现在可以由客户端应用程序调用了。使用以下命令在账本上创建一组初始的汽车。注意invoke命令需要有足够数量的节点来满足链码背书策略。

    ``` shell
    peer chaincode invoke -o orderer0.example.com:7050  --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer0.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C businesschannel -n fabcar --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt   --peerAddresses peer1.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/ca.crt -c '{"function":"initLedger","Args":[]}'
    ```
  
  - 我们可以使用查询函数来读取由链码创建的汽车集
  
  ```shell
  peer chaincode query -C businesschannel -n fabcar -c '{"Args":["queryAllCars"]}'
  ```
  
  输出：
  
```shell
  [{"Key":"CAR0","Record":{"make":"Toyota","model":"Prius","colour":"blue","owner":"Tomoko"}},{"Key":"CAR1","Record":{"make":"Ford","model":"Mustang","colour":"red","owner":"Brad"}},{"Key":"CAR2","Record":{"make":"Hyundai","model":"Tucson","colour":"green","owner":"Jin Soo"}},{"Key":"CAR3","Record":{"make":"Volkswagen","model":"Passat","colour":"yellow","owner":"Max"}},{"Key":"CAR4","Record":{"make":"Tesla","model":"S","colour":"black","owner":"Adriana"}},{"Key":"CAR5","Record":{"make":"Peugeot","model":"205","colour":"purple","owner":"Michel"}},{"Key":"CAR6","Record":{"make":"Chery","model":"S22L","colour":"white","owner":"Aarav"}},{"Key":"CAR7","Record":{"make":"Fiat","model":"Punto","colour":"violet","owner":"Pari"}},{"Key":"CAR8","Record":{"make":"Tata","model":"Nano","colour":"indigo","owner":"Valeria"}},{"Key":"CAR9","Record":{"make":"Holden","model":"Barina","colour":"brown","owner":"Shotaro"}}]
```



## 6.智能合约