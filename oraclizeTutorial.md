# Oraclize Tutorial

## Oraclize 是什么

在合约与外部服务间提供bridge

## oraclize 结构图示

![oraclize](https://github.com/oo7ww/MyBlockChainNotes/blob/master/Pic/oraclize.png)

## 合约：
* 继承 *oraclize* 方法，调用,在 oraclize 生成随机数返回后，oraclize调用自定义的 callback 函数emit 事件接收随机数

## 私链连接 oraclize

* geth
* ethereum-bridge安装： npm install --unsafe-perm -g ethereum-bridge
* ethereum-bridge -H localhost:8381 -a 0
