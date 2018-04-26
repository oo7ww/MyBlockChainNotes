# Part 1 区块链基础内容

### 1 分布式数据库的角度    
//to add blockchain structure
### 2 交易：事务性数据库
//pic to add block & transaction structure
* 区块链是一个共享的事务型数据库，数据库事务的特性：_ACID_
   
    * 原子性（Atomicity )    : 事务作为一个整体被执行，包含在其中的对数据库的操作要么全部被执行，要么都不执行
    * 一致性（Consistency）: 事务应确保数据库的状态从一个一致状态转变为另一个一致状态。一致状态的含义是数据库中的数据应满足完整性约束
    * 隔离性（Isolation）： 多个事务并发执行时，一个事务的执行不应影响其他事务的执行
    * 持久性（Durability）： 已被提交的事务对数据库的修改应该永久保存在数据库中
    
### 3 区块：交易的验证打包即公共账本化
//mining process to add
  * 交易顺序的验证——顺序选择机制——mining

### 4 区块链与分布式数据库的对比

![compareDC](https://github.com/oo7ww/MyBlockChainNotes/blob/master/compareDC.jpg)

# Part 2 以太坊虚拟机 EVM

### 1 EVM是什么，特点
   * 智能合约的运行环境，完全独立的沙盒
    
### 2 账户、交易、GAS
   * 账户分类：外部账户，合约账户，EVM同等对待
   * 交易：外部账户间的交易，合约注册交易，合约执行交易，由交易的字段区分to, data/input
     
   //to add comparing pic
   
   * GAS：执行交易的费用
   
### 3 Storage, Memory, Stack & 指令集
   * Storage: 每个 account 含有的永久存储区， key-value store， 256bits-256bits的映射
   * Memory: 为message call 提供的存储区
   * Stack: EVM是基于栈运行的，并不是基于寄存器。
   * 指令集：支持基本数据类型，256-bit words，基本的算术运算、位运算、逻辑运算、指令跳转

### 4 Message Calls， Delegatecall
   * Message Call：实现合约之间的调用，与transaction类似
   * Delegatecall：合约运行时调用其他合约的code

### 5 Logs：实现 events (more to add)

### 6 Create & Self-destruct 
   * Create：已有合约创建新的合约
   * Self-destruct : 合约自毁
