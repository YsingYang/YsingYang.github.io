---
layout: post
title:  "LeetCode-BestTimeToBuyAndSell"
categories: jekyll update
---

**继续把上周那一系列的题目写完**

### Best Time to Buy and Sell Stock III

#### Description
Say you have an array for which the element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete at most two transactions.

**Note**
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

####解题思路
首先，这道题是一道Dp题，那么回想上周我写过的BTBSS with cooldown所说的，dp我们需要考虑的是**状态**与**状态转换**，如果明确了状态以及转换对应的关系，这道题其实也就是一道Easy题而已，很多DP题目都一样，代码量非常想，最主要的是推倒出其切换的过程。

那么这道题，对应的状态有那些呢，因为最多只能有两次交易（可以少于2次），我们会有第一次买，第一次卖，第二次买，第二次卖这样系列的过程。那么它们之间又会又怎么的关系呢。
画图画得大概是这个样子
![](../image/选区_040.png)
画得有点丑...不过基本转换过程也已经标记出来了。
1. first_Buy:对于first_Buy，我们可选的状态有->这次不买，用上次买的/这次买
2. first_Sell: 对于first_Sell,我们可选的状态有1.将first_Buy的卖出当这次卖出之后，firstsell的状态也就进入了second Buy的入口/或者这次不卖，
3. second_Buy,对于Second_Buy,可选的状态转换其实也是跟first_Buy差不多，这次不买，用上次买的/或者说抛弃上一次first_Sell后的second_Buy,重新Second_Buy
4. 最后就是卖出第二次的最优解。
5. 既然状态转换都已经明确，那么我们可以写出基本的程序了

#### Code
````
class Solution {
/**
 * dp状态的巧妙用法，因为只能做两次买卖，那么我们就可以定义四种状态，
 * 1.第一次卖，2.第二次卖，3.第三次卖，4.第四次卖
 * 2.通过描述之间的转换关系，我们就能得到dp关系从而解答出这一道提
 * 
 */
public:
    int maxProfit(vector<int>& prices) {
        int firstHold = INT_MIN,secondHold = INT_MIN;//定义四种状态
        int firstSold = 0,secondSold = 0;//主要是与for循环处理四种状态的顺序有关
        for(int i=0;i<prices.size();++i){
            secondSold = max(secondSold,secondHold + prices[i]);
            secondHold = max(secondHold,firstSold - prices[i]);
            firstSold = max(firstSold,firstHold + prices[i]);
            firstHold = max(firstHold,-prices[i]);
           // cout<<firstHold<<"    "<< firstSold<<"    "<<secondHold<<"   "<<secondSold<<endl;
        }
        return secondSold;
    }
};

````

### Best Time to Buy and Sell Stock IV

#### Description
算法的其中一个魅力在于，我得出一种算法，那么这种算法能不能用于推广到任意参数k？就是说，我能不能考虑出一种具有普世性的算法。我们来看下着一道推广题

Say you have an array for which the ith element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete at most k transactions.

**Note**
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).

#### 解题思路
* 如果你理解我上一题的解题思路的话，那这一题的状态转换图应该随随便便就能写出来了把，既然状态跟状态转换关系都有了，那么代码也就随随便便出来了把。
* 话虽然是这么说，但思路想法应该考虑到方方面面，我想大部分人第一情况都会考虑，k肯定大于2,那么其他情况下，有没有考虑，k<=0呢？看吧，如果没有考虑所有情况，那么拿到WA是很显然的事情。那么除了<=0需要判断还有什么，还有k>size,对于dp问题，我们需要new一块数组或者初始化一个vector来使得我们能够暂存状态转换的数据，但是再这道题中，如果k很大，而且是大于size的，那显然，我们是没有必要new这么大块的数组的，对于一个Size的数据快，最多最多也就只能交易size/2次，一买一卖就已经占用了2个数据空间了。

````
/**
 * 该方法用到了类似BTBSS3中的方法
 * 但注意这里又3个坑，1：k为0
 * 2.当k非常大（甚至大过）prices.size()的时候
 * 3. prices.size()为0
 * 改算法的复杂度挺高用到O （2min(prices.size(),k)）
 * 时间复杂度为O(k * n);
 * 注意**中间那一段，那一段是为了处理k非常大的时候（>prices.size()/2），因为sell+hold需要两次步骤
 * 那么这个时候我们就可以利用BTSS2李的方法简单处理，
 * 
 */
 
 class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        if(k==0||prices.size()==0)
            return 0;
        int size = k>prices.size()?prices.size():k;
        
        if(k>prices.size()/2){
            int maxProfit = 0;
            for(int i=1; i<prices.size(); i++)
                maxProfit += max(prices[i]-prices[i-1], 0);
            return maxProfit;
        }
        vector<int> sell(size,0),hold(size,INT_MIN);
        for(int i=0;i<prices.size();++i){
            for(int j=size-1;j>=0;--j){
                sell[j] = max(hold[j]+prices[i],sell[j]);
                int thisSell = (j>0?sell[j-1]-prices[i]:-prices[i]);
                hold[j] = max(hold[j],thisSell);
            }
        }
       // cout<<sell[k-1]<<"  "<<hold[k-1]<<endl;
        return sell[size-1];
    }
};

````

其中有一个k>prices.size()>2的判断语句是用于优化用的，当买卖的次数满足最大次数，我们可以用BTBSS3的方法解决了。时间复杂度还有O（N）,不然需要用O(N^2)去解决响应的问题