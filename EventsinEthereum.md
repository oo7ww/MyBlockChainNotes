# 以太坊中的 Events
events 提供了访问 EVM log 的方法

## solidity编写合约定义event
举个栗子：
```js
pragma solidity ^0.4.20;
contract Bomber {
    //event 定义
    event drop(uint indexed i);
    
    //合约的构造函数
    constructor(){
        emit drop(42);
    }
    //简单的方法函数，调用一次drop event
    function DropBomb() public {
        emit drop(21);
    }
}  
```
* events 在合约中是可继承的
* events 的内容在合约内无法被访问

## event 在区块中的存储

当 event 被调用(**emit**)时：
* 传入 event 的变量被写入 transaction 的 log 中
* 在transaction被矿工打包的过程中，log被存入transactionReceipt的merkle树结构中，作为block的一部分

## event 监听

* web3js provider: websocket

  ```js
  const web3 = new Web3(new Web3.providers.WebsocketProvider("wss://rinkeby.infura.io/ws"));
  ```

* 监听返回结果：
  ```js
  { 
    address: '0xE5Ead843388c34cfE487810054cc8eE3c78DCa1F',
    blockNumber: 2343850,
    transactionHash: '0x42219edf41b83648f7c1fc1e06a48bdcc657e5d59d453a2af1ebc372ab22329f',
    transactionIndex: 11,
    blockHash: '0x10b0891256926f0b2a9effc87dec0e21fb358aa51e859a113925d4e00f485727',
    logIndex: 18,
    removed: false,
    id: 'log_1f122690',
    returnValues: Result { '0': '21', i: '21' },//event传入的变量值
    event: 'drop',
    signature: '0x211d9a536bf7e8771ad9e23602007a395b46ab2c974ef3e209731543695a3697',
    raw:
    { data: '0x',
      topics:
      [ '0x211d9a536bf7e8771ad9e23602007a395b46ab2c974ef3e209731543695a3697',
        '0x0000000000000000000000000000000000000000000000000000000000000015' ] 
    } 
  }
  ```

* web3js provider：http无法监听events
  
  原因http与WebSocket的区别
  ![httpVSwebsocket](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/httpVsWebSocket.png)
