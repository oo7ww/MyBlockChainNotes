# Solidity 语言

solidity 是静态类型语言

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

## 3 contract & ERC20 Token with example

* 1 合约 contract : 类似于面向对象中的 class 
  * contract 结构
    * 编译器版本
    ```sol
    pragma solidity ^0.4.20;
    ```
    * library: 与合约类似，目的是在一个位置仅部署一次
    ```js
    //safe math
    library SafeMath {
        function add(uint a, uint b) internal pure returns (uint c) {// pure: not allowed to read from state variables or modifiy blockchain data
            c = a + b;                                               // internal: only used in current context
            require(c >= a);
        }

        function sub(uint a, uint b) internal pure returns (uint c) {
            require(b <= a);
            c = a - b;
        }

        function mul(uint a, uint b) internal pure returns (uint c) {
            c = a * b;
            require(a == 0 || c / a == b);
        }

        function div(uint a, uint b) internal pure returns (uint c) {
            require(b > 0);
            c = a / b;
        } 
    }
    ```
    * state 变量
    ```js
    contract FixedSupplyToken is ERC20Interface, Owned {
        using SafeMath for uint;

        string public symbol;
        string public name;
        uint8 public decimals;
        uint public _totalSupply;

        mapping(address => uint) balances;
        mapping(address => mapping(address => uint)) allowed;
    
        ...
    }
    ``` 

* 2 **ABI** ：合约应用二进制接口
  * Ethereum 智能合约 **ABI** 用一个 array 表示，其中会包含数个用 **JSON** 格式表示的 Function 或 Event
  * eg
  
  ```js
  pragma solidity ^0.4.20;

  contract SimpleStorage {
      uint public data; 

      event Set(address indexed _from, uint value);

      function set(uint x) public {
          data = x;
          emit Set(msg.sender, x);
      }
  }
  ```
  
  **JSON**格式的abi
  ```js
  [
  //Function
	{
		"constant": false,
		"inputs": [ //输入的参数
			{
				"name": "x",
				"type": "uint256"
			}
		],
		"name": "set", //函数名
		"outputs": [], //函数返回值
		"payable": false, //是否接收Ether
		"stateMutability": "nonpayable",
		"type": "function" 
	},
  //Contract
	{
		"constant": true,
		"inputs": [],
		"name": "data",
		"outputs": [
			{
				"name": "",
				"type": "uint256"
			}
		],
		"payable": false,
		"stateMutability": "view",
		"type": "function"
	},
  //Event
	{
		"anonymous": false,
		"inputs": [
			{
				"indexed": true,
				"name": "_from",
				"type": "address"
			},
			{
				"indexed": false,
				"name": "value",
				"type": "uint256"
			}
		],
		"name": "Set",
		"type": "event"
	}
  ]
  ```

  
* 3 ERC20/EIP20：为实现token的合约定义了一套统一的接口标准
  
  详解
  [1](https://ethfans.org/ajian1984/articles/understanding-erc-20-token-contracts)
  [2](https://medium.com/@jgm.orinoco/understanding-erc-20-token-contracts-a809a7310aa5)

* 3 Token point，token合约的安全性：safemath的使用

[BEC合约漏洞](https://etherscan.io/address/0xc5d105e63711398af9bbff092d4b6769c82f793d#code) 
```js
function batchTransfer(address[] _receivers, uint256 _value) public whenNotPaused returns (bool) {
    uint cnt = _receivers.length;
    uint256 amount = uint256(cnt) * _value; //未使用safemath中定义的 mul
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

## 4 合约编译，部署和调用

* 1  **solc** 编译 & 部署
  * 使用 geth console / web3js
  * truffle框架下完成编译部署 
  [部署](https://github.com/oo7ww/MyBlockChainNotes/blob/master/TokenDeployment.md)至区块链上

* 2 调用
  * 合约 abi 获取函数列表
  ```js
  var myContract = new web3.eth.Contract(**abi array**);
  ```
  * 合约地址
  ```js
  var ContractInstance = myContract.at('Contact Address'); 
  ```

## 5 参考文档
  * solidity 文档：https://solidity.readthedocs.io/en/v0.4.23/index.html
  * web3.js 文档：https://web3js.readthedocs.io/en/1.0/web3-eth.html
  * 合约部署：https://medium.com/@gus_tavo_guim/deploying-a-smart-contract-the-hard-way-8aae778d4f2a
  * Ethereum 地址生成：https://kobl.one/blog/create-full-ethereum-keypair-and-address/#derive-the-ethereum-address-from-the-public-key
  * Ethereum 设计原理：https://github.com/ethereumbook/ethereumbook