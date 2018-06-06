# geth miner

## worker 

  管理 mine 过程，包含多个agent
* work 数据环境
  
  work 结构体主要用以携带数据，被视为挖掘一个区块时所需的数据环境
  ```go
  // Work is the workers current environment and holds
  // all of the current state information
  type Work struct {
      config *params.ChainConfig
      signer types.Signer
      
      state     *state.StateDB // apply state changes here
	  ancestors *set.Set       // ancestor set (used for checking uncle parent validity)
	  family    *set.Set       // family set (used for checking uncle invalidity)
	  uncles    *set.Set       // uncle set
	  tcount    int            // tx count in cycle
	  gasPool   *core.GasPool  // available gas used to pack transactions

	  Block *types.Block // the new block

 	  header   *types.Header
	  txs      []*types.Transaction
	  receipts []*types.Receipt

	  createdAt time.Time
  }
  ```

* agent 完成挖矿过程
  
  每个agent都可完成单个区块的mine
  * worker中包含一组agent, 这些agent相互竞争
    
    worker 将收到的任务发送至所有agent
    ```go
    // push sends a new work task to currently live miner agents.
    func (self *worker) push(work *Work) {
	    if atomic.LoadInt32(&self.mining) != 1 {
		    return
	    }
	    for agent := range self.agents {
		    atomic.AddInt32(&self.atWork, 1)
		    if ch := agent.Work(); ch != nil {
			    ch <- work
		    }
	    }
    }
    ```
	
  * 出块
    
    每个agent执行 mine ,先计算出对应块难度 hash 的 agent 即可将挖矿结果返回给 worker，完成出块 
    ```go
    func (self *CpuAgent) mine(work *Work, stop <-chan struct{}) {
	    if result, err := self.engine.Seal(self.chain, work.Block, stop); result != nil {
		    log.Info("Successfully sealed new block", "number", result.Number(), "hash", result.Hash())
		    self.returnCh <- &Result{work, result}
	    } else {
		    if err != nil {
			   log.Warn("Block sealing failed", "err", err)
		    }
		    self.returnCh <- nil
	    }
    }
    ```


* agent 挖矿结果处理
    
 * worker中接收 agent 挖矿计算结果的函数 wait() 循环

```go
  func (self *worker) wait() {
	for {
		for result := range self.recv {
			atomic.AddInt32(&self.atWork, -1)

			if result == nil {//agent 计算出块失败 returnCh nil
				continue
			}
			block := result.Block
			work := result.Work

			// Update the block hash in all logs since it is now available and not when the
			// receipt/log of individual transactions were created.
			for _, r := range work.receipts {
				for _, l := range r.Logs {
					l.BlockHash = block.Hash()
				}
			}
			for _, log := range work.state.Logs() {
				log.BlockHash = block.Hash()
			}
			stat, err := self.chain.WriteBlockWithState(block, work.receipts, work.state)
			if err != nil {
				log.Error("Failed writing block to chain", "err", err)
				continue
			}
			// Broadcast the block and announce chain insertion event
			self.mux.Post(core.NewMinedBlockEvent{Block: block})
			var (
				events []interface{}
				logs   = work.state.Logs()
			)
			events = append(events, core.ChainEvent{Block: block, Hash: block.Hash(), Logs: logs})
			if stat == core.CanonStatTy {
				events = append(events, core.ChainHeadEvent{Block: block})
			}
			self.chain.PostChainEvents(events, logs)

			// Insert the block into the set of pending ones to wait for confirmations
			self.unconfirmed.Insert(block.NumberU64(), block.Hash())
		}
	}
}
```
	

	
 * 成功获取区块：广播，插入event
    
		worker wait() 获取区块后，将区块插入本地主干链，并广播区块，发送事件通知其他节点
    
    ```go
    // check if canon block and write transactions
		if stat == core.CanonStatTy {
			// implicit by posting ChainHeadEvent
			mustCommitNewWork = false
		}
		// Broadcast the block and announce chain insertion event
		self.mux.Post(core.NewMinedBlockEvent{Block: block})
		var (
			events []interface{}
			logs   = work.state.Logs()
		)
		events = append(events, core.ChainEvent{Block: block, Hash: block.Hash(), Logs: logs})
		if stat == core.CanonStatTy {
			events = append(events, core.ChainHeadEvent{Block: block})
		}
		self.chain.PostChainEvents(events, logs)

		// Insert the block into the set of pending ones to wait for confirmations
		self.unconfirmed.Insert(block.NumberU64(), block.Hash())
    ```

  * 其他节点的确认：监听到该事件的其他节点，就会根据自身的状况，来决定是否接受这个新区块成为全网中公认的区块链新的链头
    
## unconfirmed
* 叔块
* shift 对侧链的判定
* ancestor, famliy set

## consensus engine

