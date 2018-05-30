---
layout: post
title:  "Fabric中文文档笔记4-chaincode开发手册"
date:   2018-05-21 14:01:31 +0800
categories: fabric 
tags: fabric blockchain chaincode
---

* content
{:toc}

Chaincode运行在一个受保护的**Docker容器当中**，**与背书节点的运行互相隔离**。Chaincode可通过应用提交的交易对账本状态初始化并进行管理。

一段chaincode通常处理由网络中的成员一致认可的业务逻辑，故我们很可能用“智能合约”来代指chaincode。**一段chiancode创建的（账本）状态是与其他chaincode互相隔离的，故而不能被其他chaincode直接访问**。不过，**如果是在相同的网络中，一段chiancode在获取相应许可后则可以调用其他chiancode来访问它的账本。**

### 1. chaincode API
每个chaincode程序都必须实现 **chiancode接口(Chaincode interface)** ，接口中的方法会在响应传来的交易时被调用。特别地，`Init`（初始化）方法会在chaincode接收到`instantiate`（实例化）或者`upgrade`(升级)交易时被调用，**进而使得chaincode顺利执行必要的初始化操作，包括初始化应用的状态**；`Invoke`（调用）方法会在响应`invoke`（调用）交易时被**调用以执行交易。**

其他chaincode shim APIs中的接口被称为**chaincode存根接口(ChaincodeSubInterface)**，用于访问及修改账本，并实现chaincode之间的互相调用。

### 2.  简单的资产管理Chaincode
 - 选择代码存放位置
```
mkdir -p $GOPATH/src/sacc && cd $GOPATH/src/sacc
touch sacc.go
```
 - 首先， 我们先进行`内务处理(housekeeping)`。对于每一个chaincode，它都会实现预定义的chaincode接口，特别是Init和Invoke函数接口。所以我们首先为我们的chaincode引入必要的依赖。我们将在此引入`chaincode shim package`和`peer protobuf package`。
```
package main

import (
    "fmt"

    "github.com/hyperledger/fabric/core/chaincode/shim"
    "github.com/hyperledger/fabric/protos/peer"
)
```
 - 初始化Chaincode
```
// Init is called during chaincode instantiation to initialize any data.
func (t *SimpleAsset) Init(stub shim.ChaincodeStubInterface) peer.Response {

}
)
```
