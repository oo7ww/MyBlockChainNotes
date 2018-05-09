//solidity note
# Solidity 语言

solidity 是静态类型语言

## 1 编译&运行环境

* Linux：
  *  **solc** 编译， 使用 geth console / web3js [部署](https://github.com/oo7ww/MyBlockChainNotes/blob/master/TokenDeployment.md)至区块链上  
  *  truffle框架下完成编译部署
  *  运行环境以太坊虚拟机（**EVM**） 
  ![solidity](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/solidity.png)

## 2 Solidity basic

* 1 EVM ：bytecode的运行时环境 

  ![evm](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/EVM.png)
  
* 2 类型 
    * 数据类型
      * 基本类型：
        * bool
        * int8-256
        *  
      * address:
      * struct:
    * 函数类型
      * internal
      * external

* 3 to add:

   函数可见性，msg，抽象，继承

## 3 contract & ERC20 Token with example

* 1 合约 contract
  * contract 是什么
  * contract 结构，类似于面向对象中的class
    * version
    * library
    * state 变量
    * 函数 

* 2 ERC20/EIP20：为实现token的合约定义了一套统一的接口标准
  
  详解
  [1](https://ethfans.org/ajian1984/articles/understanding-erc-20-token-contracts)
  [2](https://medium.com/@jgm.orinoco/understanding-erc-20-token-contracts-a809a7310aa5)

* 3 Token point，token合约的安全性：safemath

## 4 合约编译，部署和调用

* 1 编译
* 2 部署
* 3 调用

## 5 

### 参考文档：
