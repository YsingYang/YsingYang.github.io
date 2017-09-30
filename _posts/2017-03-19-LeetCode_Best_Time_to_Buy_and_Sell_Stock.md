---
layout: post
title:  "LeetCode_Best_Time_to_Buy_and_Sell_Stock"
excerpt: "LeetCode"
tags: [post, LeetCode]
comments: true
---

这是一道系列题，这次主要是先写１,２和Best Time to Buy and Sell Stock with Cooldown

### Best Time to Buy and Sell Stock

##### Description
Say you have an array for which the ith element is the price of a given stock on day i.
If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.

**Example 1:**
> Input: [7, 1, 5, 3, 6, 4]
  Output: 5
  max. difference = 6-1 = 5 (not 7-1 = 6, as selling price needs to be larger than buying price)
 
**Example 2:**
> Input: [7, 6, 4, 3, 1]
  Output: 0
  In this case, no transaction is done, i.e. max profit = 0.


##### 解题思路
 题目的tag为dp算法，其实我们可以用贪心的思想解决这道题，想得到最多的钱，即再找到最小的值前是否存在一个最大的值，那么我们可以考虑到连续和的问题，如果连续和大于0,我们可以继续增加连续和的数组长度，如果\<0,那么就置0,所以这道题可以说是连续和的变形，只不过求的不单纯是a[i] + a[i-1]
 而是sum += a[i]-a[i-1]，以求得中间差值最小


##### Solution
````
 class Solution {
/**
 * This problem is same as max subarray;
 * just different in curMax+= prices[i] - prices[i-1],because we want to calculate the max diff,not the max sum
 */
public:
    int maxProfit(vector<int>& prices) {
        int curMax = 0,curProfit = 0;
        for(int i=1;i<prices.size();++i){
            curMax = max(0,curMax+= prices[i] - prices[i-1]);
            curProfit = max(curProfit,curMax);
        }
        return curProfit;
    }
};
````

### Best Time to Buy and Sell Stock II

##### Description
Say you have an array for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

Subscribe to see which companies asked this question.

##### 解题思路
  这题与上一题出现不同的地方是，这一题允许出现多笔交易，这么一想，跟上一道题的解题方法都没什么区别，因为上一题只是为了求最大利润，那这一题，我们就有利润接+，没有利润就+0即可，所以只需要一点点变形就可以完成这道题
  
##### Solution
````
class Solution {
/**
 * 只是一个很简单的问题，与BTBSS1类似
 * 只不过比第一次写出来的简单很多，因为只有当后面的stock>前面的stock时，才会选择卖
 * 这时候只有将profit+= prices[i] - prices[i-1];即可解决，因为如果后面再大，还是会加上利润
 * 而出现小值时则不会处理
 * 
 */
public:
    int maxProfit(vector<int>& prices) {
        int profit = 0;
        for(int i=1;i<prices.size();++i){
            if(prices[i]>prices[i-1])
                profit+= prices[i] - prices[i-1];
        }
        return profit;
    }
};

````


###  Best Time to Buy and Sell Stock with Cooldown
##### Description
Say you have an array for which the ith element is the price of a given stock on day i.

Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times) with the following restrictions:

* You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
* After you sell your stock, you cannot buy stock on next day. (ie, cooldown 1 day)

**Example **
> prices = [1, 2, 3, 0, 2]
  maxProfit = 3
  transactions = [buy, sell, cooldown, buy, sell]
  
##### 解题思路
这道题就有点dp需要的思维，就是状态转换，我理解的dp是对于不同的子集，我把它称之为一种状态，我们需要的是在这种状态转换成另一种状态，我们需要是就是明确状态转换，这里copy一个discuss的图

![](../image/009.png)
[图出自这里](https://discuss.leetcode.com/topic/30680/share-my-dp-solution-by-state-machine-thinking)

我们把s0看成一种初始化状态，它下一步状态转换可以有两种选择，
1. s0->s0,
2. s0->s1(即我选择买当前的物品)

假设到s1的状态，那么s1又有两种情况
1. s1->s1(不卖)
2. s1->s2(卖出，状态转换到s2)

最后到s2，只有一种情况，回到s0,相当以我停止一回合。但它没有其他选择可以变换状态

理清状态变换后，我们就能得到状态转换的公式
````
int pre = sell;//暂存sell
sell = buy + prices[i];//更新sell状态 
buy = max(buy,rset-prices[i]);//更新buy 状态，他有两种选择，这一次买入/不买入
rset = max(rset,pre);//rset状态，这次继续reset还是卖出中cooldown*/
````

##### Solution
`````
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int size=prices.size();
        if(size<=1) return 0;
        int buy=-prices[0],rset=0,sell = INT_MIN;
        for(int i=1;i<size;++i){//这样子的顺序是确保当前的状态互不干扰
            int pre = sell;//暂存sell
            sell = buy + prices[i];//更新sell状态 
            buy = max(buy,rset-prices[i]);//更新buy 状态，他有两种选择，这一次买入/不买入
            rset = max(rset,pre);//rset状态，这次继续reset还是卖出中cooldown*/
        }
        return max(rset,sell);
        
    }
};
`````