+++
author = "HuTuAzhi"
title = "简化版加密货币的实现"
date = "2020-04-02"
description = "一种模拟比特币的简化加密货币，麻雀虽小，五脏俱全。"
tags = ["markdown", "blockchain", "加密货币", "GOLANG"]
categories = ["blockchain", "personal"]
images  = ["img/2020/04/加密货币.jpg"]
aliases = ["migrate-from-jekyl"]
+++

<!-- ---
title: 简化版加密货币的实现
date: 2020-04-02
categories: ['区块链']
draft: false
--- -->
***
在本系列文章中，我们将实现一个简化版的区块链，并基于它来构建一个简化版的加密货币。  
全系列文章分别实现三个区块链的demo，由简到难，最终逐步完善这个加密货币模型。  
***
第一个demo最简单，它仅仅是一个数组构成的一系列区块，数据结构上没有用更高级的链表，队列等等，使用了最简单的`数组`。它能够实现创始区块，添加区块，对各区块采用sha256加密算法功能。  
***
第二个demo相比第一个demo，增加了工作量证明模块与共识机制，将更贴合真实的加密货币。
***
第三个demo则将原先储存在数组中的区块数据改为利用LMDB数据库储存，利用本地文件进行修改数据。并赋予了更真实的交易记录。
***
# demo入口
>[一、Encryption currency_part1](https://hutuazhi.github.io/article/blockchain/EncCur_part1.html)  
>[二、Encryption currency_part2](https://www.jianshu.com/p/b3dc9ca82dc2#fnref2)  
>[三、Encryption currency_part3](https://www.jianshu.com/p/b3dc9ca82dc2#fnref3)  


