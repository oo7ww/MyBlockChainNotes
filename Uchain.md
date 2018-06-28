# Blockchain Note

## 1 区块链基本概念&原理

* 哈希算法：SHA3-256/Keccak256
  
  SHA-3，之前名为Keccak算法，是一个加密散列算法。
  
  每个SHA3-256 哈希值包含 64 个十六进制字符
  
  SHA-256("yank") = 45F1B9FC8FD5F760A2134289579DF920AA55830F2A23DCF50D34E16C1292D7E0
  
  SHA3-256("") = a7ffc6f8bf1ed76651c14756a061d662f580ff4de43b49fa82d80a4b80f8434a
  
* 非对称加密：address & privatekey

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
    
    ![ethMine](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/TxMine.png)

* 共识算法：POW、POS、DPOS、PBFT
  * consensus
    
    以太坊网络中的共识是指多个节点或代理商在给定时间点就区块链状态达成一致的能力。 这与传统的共识定义密切相关，但不同于定义为个体或群体之间的一般性共     识。在这种情况下，社区必须解决在技术上（网络内）和社会上（以确保协议不分叉或断裂）达成共识的挑战。
  * POW
    
    ![pow](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/pow-bitcoin.png)
    工作证明是一个共识协议，认为网络中的有效区块链是计算量最大的链。这里提到的计算工作是为了将所有块添加到当前区块链而必须完成的工作。这项工作是由网     络节点完成的，工作证明在计算上有困难，但是可行的，可以在经过合理的努力之后可以实现。最终，网络将依靠提供该PoW的节点来维持区块链。
    
    在以太坊网络以及许多其他区块链网络中，获取PoW需要找到要添加到区块链的区块的哈希值。这个哈希值是通过对由块的数据和一个随机数组成的字符串进行 hash     获得的。这个哈希值必须小于某个阈值（由网络难度决定），并且一旦节点计算出产生该哈希值的随机数，相应的块将被接受并添加到区块链中。
  * POS
    
    ![pos](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/PoW.png)
    权益证明（PoS）是一种公链的共识算法，它依赖于验证者在网络中的经济利益。在基于工作证明（PoW）的公链（例如比特币和当前的以太坊）中，PoW算法奖励解     决密码难题的参与者以验证交易并创建新的区块（即挖矿 mine ）。在基于PoS的公共区块链（例如以太坊即将推出的Casper实施）中，一组验证者轮流在下一个区     块建议和投票，每个验证者投票的权重取决于其存款的大小（即股份）。 PoS的显著优势包括安全性，集中化风险低和高效率。

    一般来说，PoS算法如下所示。区块链跟踪一组验证者，任何持有区块链基础加密货币（在以太坊的情况下为ether）的人都可以通过发送一种特殊类型的交易来锁定     他们的以太币进入存款，从而成为验证者。然后通过所有当前验证者都可以参与的共识算法完成创建和同意新块的过程。
  * DPOS
    
    ![DPOS](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/PoSvsDPoS.jpeg)
    
    高效的 "人民代表" 制度 
    
    DPoS是一种修改后的股权证明形式，网络参与者投票选举一批代表（也称为见证人）来验证和确保区块链的共识。这些代表有点类似于 PoA 的权力节点，除了他们     的权力可能会被选民撤销。

    在DPoS的共识中，就像在PoS中一样，投票的权重与用户注入的股权数量成正比。这创建了一个场景，其中较大的代币持有者比较小的代币拥有比例较高的投票权。     这从博弈论的角度来看是有意义的，因为那些把自己利益投入的人自然会有更大的动机去选择最有效率的代表目击者。

  * PBFT
    
    ![PBFT](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/PBFT.png)
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
        
        //转账交易生成函数
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
        
        //交易发送函数
        txsSend(address, privateKey, tx) {
            this.address = address;
            this.privateKey = privateKey;
            //this.txNum = txNum;
            this.web3.eth.accounts
                .signTransaction(tx, this.privateKey)  //使用私钥对交易进行签名
                .then(signed => {
                    console.log("signature finished");
                    this.web3.eth
                    .sendSignedTransaction(signed.rawTransaction) //发送签名交易
                    .then(function(receipt){  //获取交易收据
                        console.log(receipt);
                    });
                });
        }
        
        //批量转账函数
        async txFire(from, to, privateKey, Gas, GasPrice, txNum, callback){
            let balance = await this.web3.eth.getBalance(from);
            console.log(balance);
            let nonce = await this.web3.eth.getTransactionCount(from); //获取交易发送方nonce
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
    let privateKey = '0x0e7b5a57d30fca235f7c076b839bcad0b5585474fba40759420989c3586fe471'; // prefix '0x'
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
            
            //编译sol源代码
            const output = solc.compile(input.toString(), 1);
            
            //获取bytecode
            const bytecode = output.contracts[`:${name}`].bytecode;
            console.log(bytecode);
            
            //获取abi
            const abi = JSON.parse(output.contracts[`:${name}`].interface);
            console.log(abi);
            fs.writeFile(abiFile, JSON.stringify(abi, null, 4), function(err){
                if(err){
                    throw err;
                }
            });
            
            //合约实例
            const contract = new this.web3.eth.Contract(abi);

            let nonce = await this.web3.eth.getTransactionCount(address);
            console.log('nonce:' + nonce);
            
            //对合约abi进行编码
            let encodeABI = contract
                .deploy({
                  data:bytecode,
                  arguments:[]
            })
            .encodeABI();
            
            //构造交易
            let tx = {
                nonce: nonce,
                from: this.address,
                gas: 2500000,
                gasPrice: "1000000000",
                data: "0x" + encodeABI
            };

            //签名发送交易
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
        //合约实例：需要 合约abi 和 合约地址
        var Bomb = new web3.eth.Contract(abi, contractAddress);
        //合约方法 
        var DropOnce = Bomb.methods.DropBomb();
        //调用合约方法（函数）
        SendCall(web3, DropOnce, address, contractAddress, privateKey);
        //监听时间
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

* ethereum private chain [搭建](http://blog.luoyuanhang.com/2018/04/18/set-up-eth-private-chain-for-development/)
* EVM [basic](https://abelsu7.top/2018/02/28/ethereum-virtual-machine/) 
* ethereum random number [RNG](https://medium.com/@promentol/lottery-smart-contract-can-we-generate-random-numbers-in-solidity-4f586a152b27)

## 参考资料
* ethereum 基本概念与原理
  
  地址生成https://kobl.one/blog/create-full-ethereum-keypair-and-address/
  https://ethereum.stackexchange.com/questions/268/ethereum-block-architecture
  https://lilymoana.github.io/ethereum_theory.html?from=timeline
* web3.js
  
  https://web3js.readthedocs.io/en/1.0/index.html
* ERC20详解
  
  https://ethfans.org/ajian1984/articles/understanding-erc-20-token-contracts
* solidity

  工具 https://remix.ethereum.org
  文档 https://solidity.readthedocs.io/en/v0.4.23/index.html
* BEC 漏洞分析
  
  https://learnblockchain.cn/2018/04/25/bec-overflow/
* 书籍
  
  https://github.com/ethereumbook/ethereumbook
