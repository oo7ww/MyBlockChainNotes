# Blockchain Note

## 1 区块链基本概念&原理

* 哈希算法：sha256/keccak256
  
* 非对称加密：address & privatekey
  * ECDSA算法
  * ethereum地址生成过程
    
    ![key](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/key.png)

    * 1 生成 private key:
        
      椭圆曲线加密算法（EC）生成 **32 bytes** 的私钥
  
    * 2 private key --> public key：

      由 **private key** 生成 **64 bytes** 的 **public key**

    * 3 public key --> account address：

      对 **public key** 使用 Keccak-256 算法后， 截取后 **20 bytes** 作为账户地址
  
  * Keccak-256 != SHA-3
    

* 挖矿：区块交易打包过程
  * ethereum machanism 
    
    ![ethMachanism](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/EthMechanism.jpg)

* 共识算法：POW、POS、DPOS、PBFT
  * consensus
  * POW
  * POS
  * DPOS
  * PBFT
    
## 2 ethereum & web3.js

* geth 为例，ethereum 结构
  * ethereum 结构
    
    ![ethStructure](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/EthStructure.png)

* web3.js 应用案例：转账、部署合约、event 监听
  * [转账](https://rinkeby.etherscan.io/tx/0x6d948ba3e9989579a24fef7246ddcfb364c0ac286befde3f835570fc80d83d6e)
    
    ```js
    //test batch txs send

    const Web3 = require('web3');
    var net = require('net');
    //local chain
    /*
    let rpcHost = 'http://localhost:8545';
    let address = '0x533AD76Bea37A00EdcC1394d955fA918A69D9481';
    let privateKey = '0xb4d8347bdb2eb03128baaf9c2fe4047761088d135a5e1a749774e2a1c068d03f';
    const provider = new Web3(rpcHost);
    
    let to = '0x85C72e9F54Af3c00F558f840eA89C9e15718f902';
    */

    //rinkeby
    let address = '0x0D9FdaeFa06F24190609691c3039a5629dceDC97';
    let privateKey = '0x0e7b5a57d30fca235f7c076b839bcad0b5585474fba40759420989c3586fe471';
    //testnet
    const provider = new Web3(new Web3.providers.HttpProvider("https://rinkeby.infura.io/kIC5FaNWTL1xVwpeU3o1"));
    let to = '0x627306090abaB3A6e1400e9345bC60c78a8BEf57';
    //const provider = new Web3('/root/.ethereum/rinkeby/geth.ipc', net);
    //to = address;
    class BatchTxs {
        constructor(provider) {
            this.web3 = provider;
        }
    
        txGenerator(from_address, to_address, value, Gas, GasPrice, tx_nonce){
            let tx = {
                from: from_address,
                to: to_address,
                value: value,
                nonce: tx_nonce,
                gas: Gas,
                gasPrice: GasPrice,
            };
            return tx;
        }

        txsSend(address, privateKey, tx) {
            this.address = address;
            this.privateKey = privateKey;
            //this.txNum = txNum;
            this.web3.eth.accounts
                .signTransaction(tx, this.privateKey)
                .then(signed => {
                    console.log("signature finished");
                    this.web3.eth
                    .sendSignedTransaction(signed.rawTransaction)
                    .then(function(receipt){
                        console.log(receipt);
                    });
                });
        }

        async txFire(from, to, privateKey, Gas, GasPrice, txNum, callback){
            let balance = await this.web3.eth.getBalance(from);
            console.log(balance);
            let nonce = await this.web3.eth.getTransactionCount(from);
            console.log(nonce);
            
            for (var i = 0; i < txNum; ++i){
                var tx = this.txGenerator(from, to, 500000000000000000, Gas, GasPrice, nonce+i);
                console.log(tx);
                this.txsSend(from, privateKey, tx);      
            } 
        }
    }

    let bee = new BatchTxs(provider);
    bee.txFire(address, to, privateKey, 21000, "1000000000", 10, function(err){
        console.log(err);
    });
    ```
  * 合约部署
    
    ```js
    //compile and deploy
    const solc = require('solc');
    const Web3 = require('web3');
    const fs = require('fs');

    let address = '0x0D9FdaeFa06F24190609691c3039a5629dceDC97';
    let privateKey = '0e7b5a57d30fca235f7c076b839bcad0b5585474fba40759420989c3586fe471';
    //testnet
    const provider = new Web3(new Web3.providers.HttpProvider("https://rinkeby.infura.io/kIC5FaNWTL1xVwpeU3o1"));

    class ContractBiter {
        constructor(provider) {
            this.web3 = provider;
        }

        async encodeABI_deploy(address, privateKey, name, solFile, abiFile, txId, callback) {
            console.log('start biting');
            this.address = address;
            this.privateKey = privateKey;

            const input = fs.readFileSync(solFile);
            console.log('sol src loaded');

            const output = solc.compile(input.toString(), 1);
            
            const bytecode = output.contracts[`:${name}`].bytecode;
            console.log(bytecode);
        
            const abi = JSON.parse(output.contracts[`:${name}`].interface);
            console.log(abi);
            fs.writeFile(abiFile, JSON.stringify(abi, null, 4), function(err){
                if(err){
                    throw err;
                }
            });
        
            const contract = new this.web3.eth.Contract(abi);

            let nonce = await this.web3.eth.getTransactionCount(address);
            console.log('nonce:' + nonce);

            let encodeABI = contract
                .deploy({
                  data:bytecode,
                  arguments:[]
            })
            .encodeABI();

            let tx = {
                nonce: nonce,
                from: this.address,
                gas: 2500000,
                gasPrice: "1000000000",
                data: "0x" + encodeABI
            };
      
            this.web3.eth.accounts
               .signTransaction(tx, this.privateKey)
               .then(signed => {
                  console.log("hello");
                  this.web3.eth
                     .sendSignedTransaction(signed.rawTransaction)
                     .then(contractInstance => {
                          console.log(contractInstance.contractAddress);
                          fs.writeFile('contractAddress.json', JSON.stringify(contractInstance.contractAddress), function(err){
                              if(err){
                                  throw err;
                              }
                          });
                      }).catch(err => {
                          console.info(err);
                      });
                }).catch(err => {
                  console.info(err);
                });
        } 
    }

    let bite = new ContractBiter(provider);

    bite.encodeABI_deploy(address, privateKey, 'Bomber', 'eventListen.sol','eventListen.json', function(err){
        console.info(err);
    });
    ```

  * event
    * solidity编写合约定义event
      
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

    * event 在区块中的存储
      
      当 event 被调用(**emit**)时：
      * 传入 event 的变量被写入 transaction 的 log 中
      * 在transaction被矿工打包的过程中，log被存入transactionReceipt的merkle树结构中，作为block的一部分

    * event 监听
      
      * web3js provider: websocket
        
        ```js
        const Web3 = require('web3');
        const fs = require('fs');
        //testnet
        const web3 = new Web3(new Web3.providers.WebsocketProvider("wss://rinkeby.infura.io/ws"));
        //rinkeby
        let address = '0x627306090abaB3A6e1400e9345bC60c78a8BEf57';
        let privateKey = '0xc87509a1c067bbde78beb793e6fa76530b6382a4c0241e5e4a9ec0a0f44dc0d3';

        //contract info
        let contractAddress = JSON.parse(fs.readFileSync('contractAddress.json'));
        let abi = JSON.parse(fs.readFileSync('eventListen.json'));

        //contract function call
        async function SendCall(web3 ,contractCall, _from, _to, _privateKey){
            var ABI = contractCall.encodeABI();
            var tx_nonce = await web3.eth.getTransactionCount(_from, "pending");//
            console.log(tx_nonce);
            var tx = {
                from : _from,
                to: _to,
                nonce : tx_nonce,
                gas: 2500000,             
                gasPrice: "1000000000",
                data: ABI
            };

            web3.eth.accounts.signTransaction(tx, _privateKey)
                .then(signed => {
                     console.log('ready to send raw tx');
                     var tran = web3.eth.sendSignedTransaction(signed.rawTransaction);
                     tran.on('transactionHash', hash => {
                        console.log('hash:'); 
                        console.log(hash);
                     });
                    tran.on('receipt', receipt => {
                         console.log('receipt:',receipt);
                     }); 
                    tran.on('error', error=>{
                        console.log("signTransaction error",error);
                     });
                    }).catch(err => {
                     console.info(err);
                    });                   
        }

        var Bomb = new web3.eth.Contract(abi, contractAddress);

        var DropOnce = Bomb.methods.DropBomb();
        SendCall(web3, DropOnce, address, contractAddress, privateKey);
  
        Bomb.once('drop', function(error, event){
             console.log('listen once');
             console.log(event);   
        });
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
  
        原因:http与WebSocket的区别
  
        ![httpVSwebsocket](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/httpVsWebSocket.png)

## 3 solidity & 智能合约

* solidity 编程
  * solidity 是静态类型语言

  * 1 编译&运行环境
    * 编译：[solc](http://solidity.readthedocs.io/en/develop/installing-solidity.html)
    * 运行环境 : 以太坊虚拟机（**EVM**） 
    
      ![solidity](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/solidityV1.png)

  * 2 Solidity basic
    * 1 EVM ：bytecode的运行时环境 
  
      ![evm](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/EVM.png)

    * 2 类型 
      * 值类型
        * 布尔 (boolean)
        * 整型 int8-256
        * 地址 Address 20 byte (size of an Ethereum address)
        * 字节数组:定长/变长 string, bytes
        * 字符串常量
        * 十六进制常量、有理数、整型常量
        * 枚举  enum
        * 函数类型：
          
          ```
          function (<parameter types>) {internal|external} [pure|constant|view|payable] [returns (<return types>)]
          ```
          * 内部函数 internal 只能在当前合约内被调用
          * 外部函数 external 由地址和函数方法签名两部分组成，可作为外部函数调用的参数，或返回值
          * 函数的访问权限
            * public 公共函数。内部正常访问，外部正常访问，子类可继承
            * private 私有函数。内部正常访问，外部无法访问，子类无法继承
            * internal 内部函数。内部正常访问，外部无法访问，子类可继承
            * external 外部函数。内部不能直接访问(**this.f()**)，外部正常访问，子类可继承
      * 引用类型 struct, mapping
        
        * 数据位置：
          * storage ：合约创建时确定，永久存储在区块上 eg. 状态变量 key-value形式
          * memory ：用于函数内部，告知EVM在运行时创建固定大小内存区域给变量使用

    * 3 [特殊变量&属性](https://solidity.readthedocs.io/en/v0.4.23/units-and-global-variables.html#special-variables-and-functions) 
      * msg 
      * this 当前合约

* Token与ERC20, 智能合约漏洞：BEC
  * ERC20(EIP20) : Ethereum 代币合约的标准接口
  * [BEC合约漏洞](https://etherscan.io/address/0xc5d105e63711398af9bbff092d4b6769c82f793d#code) 
    
    ```js
    function batchTransfer(address[] _receivers, uint256 _value) public whenNotPaused returns (bool) {
        uint cnt = _receivers.length;
        uint256 amount = uint256(cnt) * _value; //未使用safemath中定义的 mul                                              
                                              //修正：uint256 amount = _value.mul(cnt);
        require(cnt > 0 && cnt <= 20);
        require(_value > 0 && balances[msg.sender] >= amount);

        balances[msg.sender] = balances[msg.sender].sub(amount);
        for (uint i = 0; i < cnt; i++) {
            balances[_receivers[i]] = balances[_receivers[i]].add(_value);
            Transfer(msg.sender, _receivers[i], _value);
        }
        return true;
    }
    ```

* LucyGuess [实例](http://gitlab.weicou.com:20080/xiongyu/LuckyGuess/blob/master/LuckyGuessV1.2.sol)  

## 4 Misc

* ethereum private chain ： genesis.json
* EVM basic
* ethereum random number

## 参考资料
