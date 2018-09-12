# Fabric中文文档笔记2-构建第一个fabric网络

标签（空格分隔）： fabric blockchain 

---

构建你的第一个网络（BYFN）场景提供了由两个组织组成的示例Hyperledger Fabric网络，每个组织持有2个peer节点，以及一个“solo”排序服务。

## 准备工作
1. 修改fabric-samples/first-network目录下的docker-compose-cli.yaml日志级别为debug <br>
![docker-compose-cli.yaml][1] <br>

## 创建网络
### 1. 加密生成器
我们将使用`cryptogen`工具为我们生成各种网络实体的加密材料（x509证书和签名密钥）。这些certificates证书是身份的代表，它们允许在我们的网络实体进行交流和交易时进行签名/验证身份验证。

Cryptogen消费一个包含网络拓扑的`crypto-config.yaml`，并允许我们为组织和属于这些组织的组件生成一组证书和密钥。每个组织都配置了唯一的根证书`(ca-cert)`,它将特定组件`（peers和orders）`绑定到该组织。通过为每一个组织分配唯一的CA证书，我们正在模仿一个经典的网络其中一个Member将使用自己的`Certificate Authority`（证书授权）。 Hyperledger Fabric中的Transactions和通信由实体的`private key`（私钥，存储的文件名是 keystore）签名，然后通过`public key`（公钥，存储的文件名是 signcerts）进行验证。

![crypto-config.yaml][2] <br>
网络实体的命名约定如下 - “{{.Hostname}}.{{.Domain}}”。 因此，使用我们的ordering node作为参考点，我们看到一个名为 - orderer.example.com的ordering node，它与Orderer的MSP ID绑定。

Name和Domain就是关于这个组织的名字和域名，这主要是用于生成证书的时候，证书内会包含该信息。而Template Count=2是说我们要生成2套公私钥和证书，一套是peer0.org2的，还有一套是peer1.org2的。

最后Users. Count=1是说每个Template下面会有几个普通User（注意，Admin是Admin，不包含在这个计数中）。

我们运行`cryptogen`工具，生成的证书和密钥将被保存到名为`crypto-config`的文件夹中。
`crypto-config`的层级结构如下
![crypto-config层级结构][3] <br>

目录/hyperledger/fabric-samples/first-network/crypto-config/ordererOrganizations/example.com的详细结构如下：
![example.com层级结构][4] <br>

### 2. 配置交易生成器
configtxgen tool用于创建4个配置工作： order的`genesis block`, channel的`channel configuration transaction`, * 以及两个`anchor peer transactions`一个对应一个Peer组织。

`order block`是一个ordering service的创世区块，`channel transaction`文件在Channel创建的时侯广播给order（因为orderer负责创建Channel）。`anchor peer transactions`，正如名称所示，指定了每个组织在此channel上的`Anchor peer`。

### 3.1启动网络
首先，启动网络的命令:`docker-compose -f docker-compose-cli.yaml up -d`
如果想要查看网络的实时日志，就不要使用 -d 选项。启动后，会有6个 docker containers，如下图：
![启动网络][5]

### 3.2 创建和加入Channel
前面，我们在 Create a Channel Configuration Transaction 部分，使用 configtxgen工具创建了channel configuration transaction。 你可以重复该过程以创建其它channel configuration transaction，（使用相同或不同的configtx.yaml配置文件）。 然后，可以重复本节中定义的流程，在网络中建立其它Channel。

### 3.3更新anchor peers
以下命令是Channel更新，它们将传播到Channel的定义。实质上，我们在channel’s genesis block的顶部添加额外的配置信息。请注意，我们没有修改生成块，而只是将 delta（增量） 添加到，定义anchor peers的链中。

### 4.安装并实例化Chaincode
应用程序通过Chaincode与区块链ledger进行交互。因此，我们需要在每个将执行并endorse我们的transactions的peer上，安装Chaincode，然后在Channel上实例化Chaincode。

注意,对于每个Chaincode名称和版本，只能安装一个版本的源代码（即不能安装一份代码的多个版本）。 源代码在peer的文件系统中，文件系统的上下文（或者文件名字）是Chaincode名称和版本，它是语言不可知的。 同样，实例化的Chaincode容器将反映peer上已安装的是哪个语言的源码。


  [1]: http://static.zybuluo.com/xiaocorn/vcjlxtmatya5jtds73qjxy6a/image_1cmkkte8pe83pltaaj13vd1tt19.png
  [2]: http://static.zybuluo.com/xiaocorn/lif8nz4080pc7mwzzvgz1rz4/image_1cmklg54up801apa8ul1kkstqhm.png
  [3]: http://static.zybuluo.com/xiaocorn/tcisx7y4ejz11dc5zb4wo6yf/image_1cmkls0o3dnh1jc3tek60g1lqa1j.png
  [4]: http://static.zybuluo.com/xiaocorn/zlfbj4f0hc4l5yzgsgymqmbz/image_1cmkltf6cus91fch10ln1bpntsn20.png
  [5]: http://static.zybuluo.com/xiaocorn/00n2re06comykikndqiqhrmw/image_1cmkngc59anb1s8m1rdf10vipee2d.png