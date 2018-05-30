---
layout: post
title:  "Fabric中文文档笔记3-构建第一个fabric网络"
date:   2018-05-21 14:01:31 +0800
categories: fabric 
tags: fabric blockchain
---

* content
{:toc}

构建你的第一个网络（BYFN）场景提供了由两个组织组成的示例Hyperledger Fabric网络，每个组织持有2个peer节点，以及一个“solo”排序服务。

### 1. 加密生成器
我们将使用`cryptogen`工具为我们生成各种网络实体的加密材料（x509证书）。这些证书是身份的代表，它们允许在我们的网络实体进行交流和交易时进行签名/验证身份验证。

Cryptogen消费一个包含网络拓扑的`crypto-config.yaml`，并允许我们为组织和属于这些组织的组件生成一组证书和密钥。每个组织都配置了唯一的根证书`(ca-cert)`,它将特定组件`（peers和orders）`绑定到该组织。通过为每一个组织分配唯一的CA证书，我们正在模仿一个经典的网络，这个网络中的成员将使用自己的证书颁发机构。Hyperledger Fabric中的交易和通信是通过存储在`keystore`中的实体的私钥签名，然后通过公钥手段进行验证`（signcerts）`。

我们运行`cryptogen`工具，生成的证书和密钥将被保存到名为`crypto-config`的文件夹中。

### 2. 配置交易生成器
configtxgen tool用于创建4个配置工作： order的`genesis block`, channel的`channel configuration transaction`, * 以及两个`anchor peer transactions`一个对应一个Peer组织。

`order block`是一个ordering service的创世区块，`channel transaction`文件在Channel创建的时侯广播给order。`anchor peer transactions`，正如名称所示，指定了每个组织在此channel上的`Anchor peer`。