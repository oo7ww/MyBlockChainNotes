# Gas 

## Gas：中介的作用

gas作为ethereum中的单位，用来衡量程序的计算工作量

## Gas in a tx

* gas (gas limit) 交易发送方愿意付出的 gas 额度
* gas price 交易发送方
* gas cost 实际消耗的gas
* estimateGas 

## Gas from miner's view

* gas price的高低：通常情况下 gasPrice 高的优先被打包
* miner决定交易的打包顺序，来自同一地址的交易按nonce值升序排列

## Gas Limit of block

* 区块的 gas limit决定了单个区块能包含的tx数量
* miner 投票决定区块的gas limit

## 