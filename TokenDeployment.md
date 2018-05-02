# EIP20 Token Deployment

## Part 1 convert solc to js and deploy it through geth console

* 1  compile solc file and convert to js

```js
echo "var tokenCompiled=`solc --optimize --combined-json abi,bin,interface ../myContract/FixedSupplyToken/test.sol`" > token.js
```
* 2 start the private chain

```js
geth --networkid 10 --identity "TestNode" --rpc --rpcport "8381" --datadir data0 --port "30303" --nodiscover console
```

* 3 geth console command

   * create account in geth console
   ```js
   personal.newAccount()
   ```

  * load js
  ```js
  loadScript('token.js')    
  ```

  * declare vars
  ```js
  var TokenAbi = tokenCompiled.contracts['../myContract/FixedSupplyToken/test.sol:FixedSupplyToken'].abi

  var TokenContract = eth.contract(JSON.parse(TokenAbi))

  var TokenBin = '0x' + tokenCompiled.contracts['../myContract/FixedSupplyToken/test.sol:FixedSupplyToken'].bin

  var deployToken = {from : eth.accounts[0], data: TokenBin, gas: 100000}

  var TokenInstance = TokenContract.new(deployToken)
  ```

  * mine to deploy
  ```js
  miner.start(1)//to mine a block and deploy the Token contract
  miner.stop()//  after that stop mining
  ```
  done

* bugs: 

  * miner do not work, hashrate = 0
  ```js
  miner.start(1)
  ```
  delete .ethash file to remove the previous DAGs, and then restart the miner  


## Part 2 truffle project and use signature to deploy contracts

(to do)