---
author:
  name: 赵鹏
  email: rowanhao@qifun.com
  github: rowanhao
  bio: 岂凡 软件工程师
  email_md5: 42b46657d50a4b876067194de41aa554
layout: post
title: "岂凡游戏服务器网络性能评测"
---

本文对游戏服务器网络性能进行了评测。并分析了评测结果，提出了可以优化的方向。  
本次测试的服务器和客户端都在同一台pc上运行。调整各个参数，得到不同的数据，从而对这些数据进行分析，得到最终的结论。  

#一、服务器测试环境
•	操作系统：win7 64位  
•	CPU：Intel(R) Core(TM) i5-3270 CPU @ 3.20GHz  
•	物理内存：8G

#二、scala跟scala单机性能评测

##1. 测试方法
用scala做服务端跟客户端，客户端每隔一段固定时间往服务端发一个request请求，服务端返回一个response响应。

##2. 测试数据
下面图表就是单机性能评测的结果：  
![](http://i.imgur.com/clx6PVG.png)  


##3. 测试结果
由图表可以轻易的看出，每个客户端发送数据间隔在1s时可以支持2.6万个客户端链接，而2s时可以支持3.2万个客户端链接。  


根据使用jvisualvm对评测过程的分析，可以看出最能消耗cpu的有两个地方。  
一：定时器    
因为需要频繁的创建和取消定时器，所以在定时器上面花费了很多的cpu。大概占总耗的10%~20%。我们可以优化定时器，使得原本的多个定时器公用一个定时器。大概能提升总性能的10%。  
二：反射机制    
反射机制消耗的反射机制耗cpu大概占5%~10%。可以优化掉反射机制。大概能提升总体性能的5%。  


综上所述，如果继续优化，大概能够提升总体性能的15%。
