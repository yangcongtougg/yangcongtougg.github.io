---
title: （二）最新python面试题之操作系统
date: 2018-01-10 15:53:06
tags: python面试题
categories: 面试题
---


## 操作系统
> 主要了解操作系统得相关的概念

#### select，poll和epoll
> 其实所有的I/O都是轮询的方法，只不过实现的层面不同罢了。

这个问题可能有点深入了，但相信只要能答出这个问题的话是对I/O的多路复用有很好的了解的，其中tornado使用的就是epoll的。
> tornado是一种Web服务框架；关键是它和现在的主流的框架（包括Python的框架）有着明显的区别：它是非阻塞式服务器，而且速度相当的快。得力于非阻塞方式和epoll的运用。Tornado每秒可以处理数以千计的连接，每一个活动用户都会保持着一个服务器连接。

select，poll和epoll的区别和总结：http://www.cnblogs.com/Anker/p/3265058.html
基本上select有3个缺点：
- 连接数受限。
- 查找配对的速度慢。
- 数据由内核拷贝到用户态。

poll改善了第一个缺点。
epoll改了三个缺点。
很好的关于介绍epoll的：http://www.cnblogs.com/my_life/articles/3968782.html


---
#### 调度算法
> - 闲来先服务（FCFS, First Come First Serve）
- 短作业优先（SJF, Shortest Job First）
- 最高优先权调度（Priority Scheduling）
- 时间片轮转（RR, Round Robin）
- 多级反馈队列调度（multilevel feedback queue scheduling）

常见的调度算法的总结：https://www.jianshu.com/p/6edf8174c1eb

实时调度算法：
- 最早截至时间优先 EDF
- 最低松弛度优先 LLF

---
#### 死锁
> 一般引起死锁的原因有：
- 竞争资源
- 程序推进的顺序不当

必要条件
- 互斥条件
- 请求和保持条件
- 不剥夺条件
- 环路等待条件

处理死锁的基本方法
- 预防死锁（摒弃除互斥条件以外的条件）
- 避免死锁（银行家算法）
- 检测死锁（资源分配图）
- 解除死锁
 - 剥夺资源
 - 撤销进程
 
 
 死锁概念处理策略详细介绍：https://wizardforcel.gitbooks.io/wangdaokaoyan-os/content/10.html
 
 
