# EVM: geth实现分析

Ethereum 中采用广义交易的概念，交易的执行分为内外两层：

* 第一层是EVM外，执行前将tx类型转为Message，创建evm对象，计算gas消耗，以及执行交易后创建receipt对象并返回等；

* 第二层是EVM内，执行转账，创建合约并执行合约的指令数组

## 1 transaction to message： 

* 在 EVM 内部使用统一的 message 结构/对象，transaction转为message：[tx.AsMessage()](https://github.com/ethereum/go-ethereum/blob/master/core/types/transaction.go) 

## 2 EVM 交易（transaction）调用

* call： 
* create 
* callcode 
* delegateCall 
* staticCall

## 3 合约函数调用过程