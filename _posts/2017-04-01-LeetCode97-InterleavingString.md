---
layout: post
title:  "LeetCode97-InterleavingString"
excerpt: "LeetCode"
tags: [post, LeetCode]
comments: true
---


### Description
Given s1, s2, s3, find whether s3 is formed by the interleaving of s1 and s2.

**For example**
Given:
s1 = "aabcc",
s2 = "dbbca",

When s3 = "aadbbcbcac", return true.
When s3 = "aadbbbaccc", return false.

Subscribe to see which companies asked this question.


### 解题分析


  &nbsp;&nbsp;我一开始想到的方法是基于memorization的DFS搜索方法，我对于这种方法的理解是，它其实跟dp一样，基本上每个结果只搜索一次，如果取得的值之前已经计算过了，那么直接从memorization提取，它们之前**不同**的是DP是一种**buttom to top**的方法,而该搜索方法是**top - to - buttom**的，相当于我们需要从大问题开始，逐步递归进子问题，得到子问题的解，同时返回给上一层。
  
  那么我们如果将大问题分解呢，从题目所需要解决的问题可以看出，s3无非就是s1,s2顺序插入自身的字符串组成s3嘛，这么想很容易GG的，因为有时候会有这么一种情况
> s1 = abcd ,s2 = bdc。

就是说如果我们在某个状态碰到了2种可选的情况应该如何办？这时候可以想到backtracking，但是你不减枝肯定是TLE的，所以我们需要记录下走过的状态，从而再下一次搜索到该状态的时候可以直接返回它的结果

我记录的“状态”为p1,p2的位置，p1,p2为分别指向s1,s2的index的指针，简单说就是p1,p2指到s1,s2的哪个字符。如果p1,p2已经check过了，我们就可以直接返回p1,p2的结果。所以我们就需要一个** unordered_map&lt;pair&lt;int,int&gt;，bool&gt; ** 这里有一点要注意的。就是unordered_map内置没有用pair&lt;T,N>作为key值的hasher，所以我们需要自定义一个hasher,具体怎么hash你可以自己定义，但注意一定要为const

```
struct myhasher{
    size_t operator()(const pair<int,int> &p1) const {//为什么这里不加const 就错了
        return p1.first ^ p1.second ^ 3;
    }
};
```

明确了DFS策略，我们就可以进行搜索了，那么搜索的选项有
1. s1[p1] == s3[p]&&s2[p2]!=s[p]，搜索p1++。
2. s1[p1] != s3[p]&&s2[p2]!=s[p] 搜索p2++。
3. s1[p1] \== s3[p] && s2[p2] == s[p] 两者都要进行搜索，有一个为true即可
还需注意的是，注意p1,p2的index不要越界
搜索完该层的结果后mp[{p1,p2}] = res ,res 为该层搜索得到的结果
最后代码如下所示

```
struct myhasher{
    size_t operator()(const pair<int,int> &p1) const {//为什么这里不加const 就错了
        return p1.first ^ p1.second ^ 3;
    }
};

class Solution {
unordered_map<pair<int,int>,bool,myhasher> mp;


/***
 * 基于memorization的dfs搜索方法
 * 这里与unordered_map<pair<int,int>,bool,myhasher> mp;记录p1,p2指针再i,j位置时的结果
 * 这里不能用substr做key,因为访问到该substr也会是有不同的状态的
 * 通过dfs调用 ，如果mp里面又结果，直接返回结果，当index==s3.size()为递归出口
 */
public:
    bool isInterleave(string s1, string s2, string s3) {
        int p1 = 0;
        int p2 = 0;
        return check(s1,s2,s3,0,0,0);
    }
    
    bool check(const string &s1,const string &s2,const string &s3,int p1,int p2,int index){
        if(index==s3.size()){
            return p1==s1.size()&&p2==s2.size();
        }
        bool res;
        if(mp.find({p1,p2})!=mp.end()){
            return mp[{p1,p2}];
        }
        if(s1[p1]==s3[index]&&(p2==s2.size()||s2[p2]!=s3[index]))
            res = check(s1,s2,s3,p1+1,p2,index+1);
        else if((p1==s1.size()||s1[p1]!=s3[index])&&s2[p2]==s3[index])
            res = check(s1,s2,s3,p1,p2+1,index+1);
        else if(p1<s1.size()&&p2<s2.size()&&s1[p1]==s3[index]&&s2[p2]==s3[index]){
            res = check(s1,s2,s3,p1+1,p2,index+1)||check(s1,s2,s3,p1,p2+1,index+1);
        }
        else
            res = false;
        //cout<<tmp<<"   "<<(int)(res)<<p1<<p2<<endl;
        mp[{p1,p2}] = res;
        return res;
    }
    
};

```

### 另一种解法(DP)
既然我们可以用top - to - buttom的解法，自然也可以用DP的思路来解决，只要我们有子问题到父问题的推倒公式
1. 当i\==0&&j==0时，dp[i][j] = true;
2. if(i==0) dp[i][j] = dp[i][j-1]&&s2[j-1]==s3[i+j-1];
3. if(j==0) dp[i][j] = dp[i-1][j]&&s1[i-1] == s3[i+j-1];
4. else dp[i][j] = (dp[i-1][j]&&s1[i-1]\==s3[i+j-1])||(dp[i][j-1]&&s2[j-1]==s3[i+j-1]);

简单说，就是利用上一层dp[i-1][j]||dp[i][j-1]的答案以及是否满足s1[i-1] \== s[i+j-1] s2[j-1]==s3[i+j-1],有了推到公式之后，这个DP问题也自然很容易得到解决。
```
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int size1 = s1.size(),size2 = s2.size(),size3 = s3.size();
        if(size1+size2!=size3)
            return false;
        vector<vector<bool>> dp(size1+1,vector<bool> (size2+1,false));
        for(int i=0;i<=size1;++i){
            for(int j=0;j<=size2;++j){
                if(i==0&&j==0)
                    dp[i][j] = true;
                else if(i==0)
                    dp[i][j] = dp[i][j-1]&&s2[j-1]==s3[i+j-1];
                else if(j==0)
                    dp[i][j] = dp[i-1][j]&&s1[i-1] == s3[i+j-1];
                else
                    dp[i][j] = (dp[i-1][j]&&s1[i-1]==s3[i+j-1])||(dp[i][j-1]&&s2[j-1]==s3[i+j-1]);
            }
        }
        return dp[size1][size2];
    }
};
```
