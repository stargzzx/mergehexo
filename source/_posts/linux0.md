---
title: linux 入门
date: 2019-01-30 13:21:17
categories:
- [linux,基础]
tags:
- linux
- system
- 鸟叔
---
只输入不输出，永远只是纸上谈兵。

技术至上，知识无罪。

参考书籍：《鸟哥的Linux私房菜-基础篇》第四版.pdf

<!-- more -->

## CPU架构

主要由两种

	RISC 精简指令集
	CISC 复杂指令集
	
RISC 每个指令运行时间都很短，执行性能较佳，如果要做复杂的事情，就要由多个指令完成。

常见的 RISC微指令集 CPU 主要例如甲骨文 （Oracle） 公司的 SPARC 系列、 IBM 公司的 Power Architecture （包括 PowerPC） 系列、与安谋公司 （ARM Holdings） 的 ARM CPU 系列等。

CISC 在微指令集的每个小指令可以执行一些较低阶的硬件操作，指令数目多而且复杂， 每条指令的长度并不相同。

常见的CISC微指令集CPU主要有AMD、Intel、VIA等的x86架构的CPU

### 什么是 x86

由于AMD、Intel、VIA所开发出来的x86架构CPU被大量使用于个人电脑（Personalcomputer）用途上面， 因此，个人电脑常被称为x86架构的电脑！

这是因为最早的那颗Intel发展出来的CPU代号称为8086，后来依此架构又开发出80286,80386...， 因此这种架构的CPU就被称为x86架构了。

在2003年以前由Intel所开发的x86架构CPU由8位升级到16、32位，后来AMD依此架构修改新一代的CPU为64位， 为了区别两者的差异，因此64位的个人电脑CPU又被统称为x86_64的架构喔！

>64位CPU代表CPU一次可以读写64bits这么多的数据，32位CPU则是CPU一次只能读取32位的意思。

不同的x86架构的CPU主要是在于微指令集的不同。

新的x86的CPU大多含有很先进的微指令集， 这些微指令集可以加速多媒体程序的运行，也能够加强虚拟化的性能，而且某些微指令集更能够增加能源效率， 让CPU耗电量降低呢！

## 存储

假设你今天购买了500GB的硬盘一颗，但是格式化完毕后却只剩下460GB左右的容量，这是什么原因？

答：因为一般硬盘制造商会使用十进制的单位，所以500GByte代表为500 * 1000 * 1000*1000Byte之意。 转成文件的容量单位时使用二进制（1024为底），所以就成为466GB左右的容量了。

硬盘厂商并非要骗人，只是因为硬盘的最小物理量为512Bytes，最小的组成单位为扇区（sector）， 通常硬盘容量的计算采用“多少个sector”，所以才会使用十进制来处理的。相关的硬盘信息在这一章后面会提到的！


## 超线程(Hyper-Threading, HT)

在每一个CPU 内部将重要的寄存器 （register） 分成两群， 而让程序分别使用这两群寄存器。也就是说，可以有两个程序“同时竞争 CPU 的运算单元”，而非通过操作系统的多任务切换！ 这一过程就会让 CPU 好像“同时有两个核心”的模样！因此，虽然大部分 i7 等级的 CPU 其实只有四个实体核心，但通过 HT 的机制， 则操作系统可以抓到八个核心！并且让每个核心逻辑上分离，就可以同时运行八个程序了。












































