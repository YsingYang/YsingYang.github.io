---
layout: post
title:  "471. Encode String with Shortest Length"
excerpt: "A ton of text to test readability with image feature."
tags: [post, LeetCode]
comments: true
---

## Description
Given a non-empty string, encode the string such that its encoded length is the shortest.

The encoding rule is: <font color = red>**k[encoded_string]**</font>, where the encoded_string inside the square brackets is being repeated exactly k times.

**Note:**
1. k will be a positive integer and encoded string will not be empty or have extra space.
2. You may assume that the input string contains only lowercase English letters. The string's length is at most 160.
3. If an encoding process does not make the string shorter, then do not encode it. If there are several solutions, return any of them is fine.


**Example 1:**
>> Input: "aaa"
Output: "aaa"
Explanation: There is no way to encode it such that it is shorter than the input string, so we do not encode it.

**Example 2：**
>> Input: "aaaaa"
Output: "5[a]"
Explanation: "5[a]" is shorter than "aaaaa" by 1 character.

**Example 3**
>> Input: "aaaaaaaaaa"
Output: "10[a]"
Explanation: "a9[a]" or "9[a]a" are also valid solutions, both of them have the same length = 5, which is the same as "10[a]".

**Example 4**
>> Input: "aabcaabcd"
Output: "2[aabc]d"
Explanation: "aabc" occurs twice, so one answer can be "2[aabc]d".

**Example 5**
>> Input: "abbbabbbcabbbabbbc"
Output: "2[2[abbb]c]"
Explanation: "abbbabbbc" occurs twice, but "abbbabbbc" can also be encoded to "2[abbb]c", so one answer can be "2[2[abbb]c]".

## 个人理解与题目描述
  这是谷歌的一道题，标记为Hard,我觉得这是跟LeetCode394. Decode String 是一道相互关联的题，(这是一道Medium的题，简单得运用stack存储就可以还原原字符串）
  >> s = "3[a]2[bc]", return "aaabcbc".
	s = "3[a2[c]]", return "accaccacc".
	s = "2[abc]3[cd]ef", return "abcabccdcdcdef".
  
  我想应该是面试官会先问给一串已编码好的字符串，问如何对这个字符串进行decode，然后再问那么如何encode。
  按照常理来讲Brute search 会TLE的，而且对于这种题目明显是可以分解成子问题，所以是可以用dynamic programming的方法来解决。那么如何找到推导公式是解决这一个问题的关键所在。开始我拼命想用O(n^2)解决这一个问题，但发现解决不了。。可能我没想到那一个keypoint，开始我想的是对于一个字符串，我们可以new 一个 n*n的矩阵暂存每个子集的最优解，如果
 <center> 
  $$  dp[i][pos] = dp[pos+1][j]$$
  $$ 那么就有dp[i][j] = to\_string(num) + '[' + dp[i][i+pos-1] + ']'$$ 
 </center>
   
  
 但是显然以上式子不能在O（n^2）的时间复杂度上解决问题，因为在寻找子串的时候明显需要O(n)的时间复杂度（开始我以为需要O（N^2）），思路卡住，于是取找discuss 看看里面有没有更好的推导公司，另外discuss中，发现一种判断重复子序列O（n）的方法。下面会做解释
 
## Algorithm
````
class Solution {
    vector<vector<string>> dp;
    string canEncoding(const string &s,int i,int step){
        string tmp = s.substr(i,step);
        size_t pos = (tmp+tmp).find(tmp,1);
        if(pos>=tmp.size()){
            return tmp;
        }
        return to_string(tmp.size()/pos) +'[' + dp[i][i+pos-1] + ']';
    }
public:
    string encode(string s) {
        int size = s.size();
        dp = vector<vector<string>> (size,vector<string> (size));
        for(int step = 1;step <= size;++step){  //每个string开始跨步的步长
            for(int i = 0; i+step-1<size;++i){    //string的起始点
                int j = i + step -1;                 //substr的截止下标
                dp[i][j] = s.substr(i,step);
                for(int k = i; k<j;++k){  //开始判断是否存在更小的sub组合成dp[i][j]
                    string left = dp[i][k],right = dp[k+1][j];
                    if(left.size()+right.size()<dp[i][j].size()){
                        dp[i][j] = left + right;
                    }
                }
                if(step>=4){  //寻找当前s(i,j)是否可以encoding,这里用到剪枝算法，因为当step<4时encode没有缩短编码长度
                    string tmp = canEncoding(s,i,step);
                        if(tmp.size()<dp[i][j].size()){
                        dp[i][j] = tmp;
                    }
                }
            }
        }
        return dp[0][size-1];
    }
};
````
 -  主函数encode
     *  外层for循环定义每一步的步长
     *  第二层循环，我们需要继续对s进行拆分，根据步长step,与起始点的不同，我们得到的substr也不同，所以我们初始化每个dp[i][j]为s的原始字符串，即dp[i][j] = s.substr(i,step),可以简单看出初始化后的结果为。

    *  | - | a | b | c |
        |-|- | -| -|
        |a| a|ab |abc|
        |b|-|b|bc|
        |c|-| -|c|

    * 那么，我们如何得到dp[i][j]存在更小的string呢， 所以这里引入第三层循环
    $$如果存在leftstring = dp[i][k],rightstring = d[k+1][j],且(left + right).size()\ < dp[i][j].size()\quad则更新dp[i][j]$$
    * 当更新完后，我们需要再判断当前字符串subste是否可以自我压缩，这里@yanzhan2 提出一种O（n）的判断方法 @zzg_zzm 给出了相应的数学证明
 - 函数canEncoding
   * 我们取当前dp[i][j]初始字符串s.substr(i,step);，如果pos = (tmp + tmp).find(tmp,1)得到的pos>=tmp.size()，证明不存在可重复压缩串.
   * 证明 [URL](https://discuss.leetcode.com/topic/72732/rigorous-proof-why-condition-s-s-find-s-1-s-size-is-equivalent-to-substring-repetition)

	>> Why condition (s+s).find(s,1) < s.size() is equivalent to substring repetition?
	>> Proof: Let N = s.size() and L := (s+s).find(s,1), actually we can prove that the following 2 statements are equivalent:
	
	>> 1. 0 < L < N;
	2. N%L == 0 and s[i] == s[i%L] is true for any i in [0, N). (which means s.substr(0,L) is the repetitive substring)
	Consider function char f(int i) { return s[i%N]; }, obviously it has a period N.

	>> "1 => 2": From condition 1, we have for any i in [0,N)

	>> * s[i] == (s+s)[i+L] == s[(i+L)%N],
	which means L is also a positive period of function f. Note that N == L*(N/L)+N%L, so we have
	* f(i) == f(i+N) == f(i+L*(N/L)+N%L) == f(i+N%L),
	which means N%L is also a period of f. Note that N%L < L but L := (s+s).find(s,1) is the minimum positive period of function f, so we must have N%L == 0. Note that i == L*(i/L)+i%L, so we have
	* s[i] == f(i) == f(L*(i/L)+i%L) == f(i%L) == s[i%L],
	so condition 2 is obtained.
	"2=>1": If condition 2 holds, for any i in [0,N), note that N%L == 0, we have

	>>* (s+s)[i+L] == s[(i+L)%N] == s[((i+L)%N)%L] == s[(i+L)%L] == s[i],
	which means (s+s).substr(L,N) == s, so condition 1 is obtained.
    
 - 最后，相应更新，返回dp[0][size-1]得到的就是我们所要的结果
