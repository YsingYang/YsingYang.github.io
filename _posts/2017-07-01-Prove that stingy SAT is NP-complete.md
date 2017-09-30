---
layout: post
title:  "Prove that stingy SAT is NP-complete"
excerpt: "Prove NP-Complete"
tags: [post, Algorithm]
comments: true
---

# Exercises 8.3
## Prove that STINGY SAT is NP-complete。

#### 3-SAT问题描述
给定一个有穷的布尔变量集合 $ X = \lbrace x_{1}, x_{2}, ..., x_{n} \rbrace， | X |＝　n, $每个变量能取0或1， 一组子句， $C = \lbrace C_{1}, C_{2}, C_{3}..C_{m} \rbrace | C | = m , C = C_{1} \bigwedge C_{2} \bigwedge .... \bigwedge C_{m}$， 每个$C_{i}$是由3个变量组成的析取范式， 即 $z_{1} \bigvee z_{2} \bigvee z_{3} $给定一个布尔变量集合X和子句集合C, 是否存在一个真值赋值， 使得C为真， 即每个子句为真

#### STINGY SAT问题描述
given a set of clauses (each a disjunction of literals)， and an integer k, find a satisfying assignment in which at most k variables are true。 我的理解是给定一组子句， 和一个int k, 找到最多k个变量为真的一个可满足的赋值。

#### 证明
1. 证明Stingy SAT 是一个NP问题
我们可以给出Stingy SAT任意解， 并检查是否满足题目要求， 并且我们可以一次遍历就能检查出是否最多k个变量的赋予真值

2. 将一个已知为NP - complete的问题归约为Stingy SAT <br>

**问题转换** <br>
**给定一个有$n$个变量的SAT公式$f$， 我们可以将$(f, n)$看做是一个Stingy SAT问题的一个可行解**
 * 充分性， 假设$f$满足与SAT, 并且没有超过n个变量为真， 那么满足SAT的$f$一个赋值， 一定会满足Stingy SAT的一个实例$(f, n)$, 所以$(f, n)$是满足Stingy SAT的一个成真赋值， 且不超过$n$个变量为真。
 * 必要性， 假设$(f, n)是一个Stingy SAT$的一个可行解， 那么任意满足该问题的赋值都必将为SAT问题 $f$ 的一个可行解， 所以， $ f $ 是SAT的一个可行解