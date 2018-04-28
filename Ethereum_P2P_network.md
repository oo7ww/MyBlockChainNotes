# 1 **P2P** 网络
## 特点：

* P2P网络中的个体在地位和功能上是平等的，天然的去中心化、分布式的。
* 在 **P2P** 网络中并非每个节点与网络中其他节点均有通信

  ![p2pnetwork](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/p2p.png)

## 分类：

  * 无结构化的：简单易于组建，但是在网络中查找数据效率低
  * 结构化的  ：使用分布式哈希表对数据建立索引，提高查询效率；但是哈希表容量有限，发生 **churn** 时，网络性能大幅降低
    * **churn** 事件： 大量新旧个体频繁加入和离开
# 2 Ethereum 的网络通信

  ![ethNetwork](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/eth_structure.png)

## 需求特征
  * 网络中随时可能存在一些个体加入和离开网络的情况，但同一时间内大量新旧个体同时发生加入或离开的概率很低。
  * 每个节点存储的数据( **block** ), 理想状态下时相同的。

## 通信过程/场景
  * 广播新出现的交易对象
  * 广播新挖掘出的区块
  * 节点间定时的区块全链强制同步
  * 节点间同步新出现的交易对象
