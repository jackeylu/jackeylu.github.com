---
layout: post
title: "PageRank on an Evolving Graph"
categories:
- algorithm
- dynamic algorithm
- reading report
tags:
- reading report
- dynamic algorithm
- big data algorithm


---

#  PageRank on an Evolving Graph#

<!--
 @Date    : 2015-05-04 10:40:38
 @Author  : Jackey Lyu (lvlin@huawei.com)
 @Link    : http://jackeylu.com 
 @Version : $Id$
-->

这是KDD 2012中的一篇论文，作者是Bahman Bahmani, Ravi Kumar, Mohammad Mahdian, Eli Upfal，
是Stanford、Yahoo! Research,Brown Universtiy的合作文章。
讲述的问题是：没有办法获得全体数据（或者数据在求解过程中会发生变化）的情况下，
如何让阶段性结果的准确性逼近。作者选取PageRank问题和算法在此类输入下进行分析验证。

背景描述
==================================================

Broadly speaking, algorithms for PageRank fall into one of two categories: 
linear algebraic methods and Monte Carlo methods.

Algorithms for updating PageRank values upon changes to the network have 
been proposed in both the broad categories mentioned above. 
A family of the linear algebraic methods use the "aggregation" idea to 
efficiently update the PageRank vector. 
Tong et al. and McSherry also propose algorithms to deal with PageRank updates.

但这些都是 assume knowledge of all the changes in the network. 

问题的模型最早由Anagnostopoulos et al.提出。 
In contrast with more traditional computation models such as streaming, 
property testing, multi-armed-bandits, and dynamic graph models, 
this model captures both of the two essential properties of large evolving data applications:
the input is changing over time without notifying the algorithm, 
and the only way for the algorithm to learn about these changes 
(to accordingly update its soultion) is by periodically probing the input in a limited way.
Anagnostopoulos et al. formalize this framework and study sorting, selection, 
and basic graph connectivity problems in this computation model.
