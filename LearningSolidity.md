//solidity note
# Solidity 语言

solidity 一种静态类型语言

## 1 编译&运行环境

* Linux：
  *  编译：[solc](http://solidity.readthedocs.io/en/develop/installing-solidity.html)
  *  运行环境 : 以太坊虚拟机（**EVM**） 
  ![solidity](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/solidityV1.png)

## 2 Solidity basic

* 1 EVM ：bytecode的运行时环境 

  ![evm](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/EVM.png)

* 2 类型 
    * 值类型
        * 布尔 (boolean)
        * 整型 int8-256
        * 地址 Address 20 byte (size of an Ethereum address) [以太坊地址生成过程](https://github.com/oo7ww/MyPrivateChain/blob/master/DailyResume.md)
        * 字节数组:定长/变长
        * 字符串常量
        * 十六进制常量、有理数、整型常量
        * 枚举  
        * 函数类型：
          ```
          function (<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]
          ```
          * 内部函数 internal 只能在当前合约内被调用
          * 外部函数 external 由地址和函数方法签名两部分组成，可作为外部函数调用的参数，或返回值
          * 函数可见性：
            * public 任意访问
            * private 仅当前合约内
            * internal 仅当前合约及所继承的合约
            * external 仅外部访问，内部使用this.f()
    * 引用类型 struct, mapping
       * 数据位置：
          * storage ：合约创建时确定，永久存储在区块上 eg. 状态变量 key-value形式
          * memory ：用于函数内部，告知EVM在运行时创建固定大小内存区域给变量使用

* 3 特殊变量&属性
    * msg
    * this 当前合约    

* 4 对象，继承 
* 5 to add

## 3 contract & ERC20 Token with example

* 1 合约 contract
  * contract 是什么
  * contract 结构，类似于面向对象中的class
    * version 
      ```sol
         pragma solidity ^0.4.20;
      ```
    * library
    * state 变量
    * 函数 

* 2 ERC20/EIP20：为实现token的合约定义了一套统一的接口标准
  
  详解
  [1](https://ethfans.org/ajian1984/articles/understanding-erc-20-token-contracts)
  [2](https://medium.com/@jgm.orinoco/understanding-erc-20-token-contracts-a809a7310aa5)

* 3 Token point，token合约的安全性：safemath

## 4 合约编译，部署和调用

* 1  **solc** 编译 & 部署
  * 使用 geth console / web3js [部署](https://github.com/oo7ww/MyBlockChainNotes/blob/master/TokenDeployment.md)至区块链上  
  * truffle框架下完成编译部署
* 2 调用

## 5 BEC token

### 参考文档：
