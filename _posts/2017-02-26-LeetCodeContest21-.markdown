---
layout: post
title:  "LeetCode　Contest 21"
categories: jekyll update
---


## **题目转载自[leetcode.com](https://leetcode.com/)**

详解迟点更新
## Q1:Easy　Minimum Absolute Difference in BST
### Description
Given a binary search tree with non-negative values, find the minimum absolute difference between values of any two nodes.

**<font size='4'>Example1</font>**

![](../image/006.png)

**Node**

There are at least two nodes in this BST.

### Explanation

  我的方法是简单利用到BST的性质，节点的左子树的最右根为最接近该节点值的，小于该节点值的节点，该节点右子树的左边节点为最接近该点值的，大于该节点值的节点，利用这一性质可以用递归求的。算法效率并不高，对于每个节点找的其领进点的时间复杂度为 
  
  $$
  O(2\log{n}) \quad所以整体的时间复杂度应该为Ｏ（2n\log{n}）
  $$
  
  感觉这样不如直接将ＢＳＴ中序列遍历直接展开成一个递增vector，然后逐步遍历vector那么时间复杂度应该为
  
  $$
  O(2n)
  $$
  
 优于原本算法时间复杂度

### Code

````

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int getMinimumDifference(TreeNode* root) {
        //int maxdiff = INT_MAX;
        return get(root);
    }
    
    int get(TreeNode *root){
        if(!root)
            return INT_MAX;
        int left = get(root->left);
        int right = get(root->right);
        int val;
        //int thisdiff = min(abs(root->val-findMin(root->left,0)),abs(root->val - findMin(root->right,1)));
        int leftdiff = ((val = findMin(root->left,0))==INT_MIN?INT_MAX:abs(root->val-val));
        int rightdiff = ((val= findMin(root->right,1))==INT_MIN?INT_MAX:abs(root->val-val));
        return min(left,min(right,min(leftdiff,rightdiff)));
        
    }
    
    int findMin(TreeNode *root,int flag){
        if(flag==0&&root&&root->right){
            return findMin(root->right,flag);
        }
        else if(flag&&root&&root->left){
            return findMin(root->left,flag);
        }
        return root?root->val:INT_MIN;
    }
};

````


## Q2:Medium 523. Continuous Subarray Sum

### Description

Given a list of non-negative numbers and a target integer k, write a function to check if the array has a continuous subarray of size at least 2 that sums up to the multiple of k, that is, sums up to n*k where n is also an integer.

**Example1**

> 	Input: [23, 2, 4, 6, 7],　k=6
	Output: True
	Explanation: Because [2, 4] is a continuous subarray of size 2 and sums up to 6.

**Example2**

>   **Input:** [23, 2, 6, 4, 7],  k=6<br>
	**Output:** True<br>
	**Explanation:** Because [23, 2, 6, 4, 7] is an continuous subarray of size 5 and sums up to 42.

**Note**
1. The length of the array won't exceed 10,000.
2. You may assume the sum of all the numbers is in the range of a signed 32-bit integer.


### Explanation

这题其实跟　**525.Contiguous Array   ** ,**325.Maximum Size Subarray Sum Equals k**两道题是一样道理，打算在下个星期写个关于这种类型的题的详解，其实就是简单利用Hash_Table解决问题，另外，这道题有很多坑。。。我死在两个坑，不过好像有人比我更惨死在11个坑里.

$$
\quad坑 \to [0,0],[0,1,0]
$$

因为这道题需要用%号运算，遇到0会报runtime error。。主要没看清楚 **non-negative numbers**。
算法简单描述为遍历数组，同时取sum，将sum%k得到的值在unordered_map中查找是否存在过，如果存在，比较i-mp[sun%k]>1?如果成功return true，否则继续遍历。
举个栗子，如果u_map中存在值1->(index)2,且在index 5中sum%k也出现了1那么５－２得到的sum ％k必等于０

### PreCode 

contest时写个代码，被题目坑了个地方，其实不用记录sum的。。这里就不解释太多了，纯粹想多了

```
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int preSum = 0;
        unordered_map<int,pair<int,int>> mp;
        mp[0] = {-1,0};
        for(int i=0;i<nums.size();++i){
            preSum += nums[i];
            int diff = (k==0?preSum:preSum%k);
            if(mp.find(diff)!=mp.end()&&i-mp[diff].first>1)
                return true;
            if(mp.find(diff)==mp.end())
                mp[diff] = {i,preSum};
            //cout<<preSum-mp[diff].second<<endl;
        }
        return false;
    }
};
```

### Code 

修改后的代码，简洁很多

```

class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        int preSum = 0;
        unordered_map<int,int> mp;
        mp[0] = -1;
        for(int i=0;i<nums.size();++i){
            preSum += nums[i];
            int diff = (k==0?preSum:preSum%k);
            if(mp.find(diff)!=mp.end()&&i-mp[diff]>1)
                return true;
            if(mp.find(diff)==mp.end())
                mp[diff] = i;
        }
        return false;
    }
};
```


## Q3:Medium 524. Longest Word in Dictionary through Deleting

### Description

Given a string and a string dictionary, find the longest string in the dictionary that can be formed by deleting some characters of the given string. If there are more than one possible results, return the longest word with the smallest lexicographical order. If there is no possible result, return the empty string.

**Example1**

> **Input:**
s = "abpcplea", d = ["ale","apple","monkey","plea"]
**Output: **
"apple"


**Example2**

> **Input:**
s = "abpcplea", d = ["a","b","c"]
**Output: **
"a"

**Note**

1.All the strings in the input will only contain lower-case letters.
2.The size of the dictionary won't exceed 1,000.
3.The length of all the strings in the input won't exceed 1,000.

### Explanation

这题没想太多。。开始没有看清楚，注意字符串序列是不能变的　如s = "abs" d = ["sba"]，这两个是不匹配的，所以我简单的用了暴力搜索。。效率也不会太慢，不过提升空间应该还是挺多的.

### Code 

```
class Solution {
public:
    string findLongestWord(string s, vector<string>& d) {
        int count = s.size();
        string res;
        int maxCount = INT_MIN,thisSize;
        for(int i=0;i<d.size();++i){
            if((getMinCount(s,d[i],count))==false)
                continue;
            thisSize = d[i].size();
            if(thisSize>maxCount||(thisSize==maxCount&&d[i]<res)){
                maxCount = thisSize;
                res = d[i];
            }
        }
        return res;
    }
    
    bool getMinCount(const string &original , const string &s,int count){
        int i=0,index =0,size = s.size();
        while(i<original.size()){
            if(original[i]==s[index]){
                ++index;
            }
            if(index == size)
                return true;
            ++i;
        }
        return false;
    }
};

```
## Q4 Hard 	529. Minesweeper

### Description

Let's play the minesweeper game ([Wikipedia](https://en.wikipedia.org/wiki/Minesweeper_(video_game), [online game](http://minesweeperonline.com/))!

You are given a 2D char matrix representing the game board. 'M' represents an unrevealed mine, 'E' represents an unrevealed empty square, 'B' represents a revealed blank square that has no adjacent (above, below, left, right, and all 4 diagonals) mines, digit ('1' to '8') represents how many mines are adjacent to this revealed square, and finally 'X' represents a revealed mine.

Now given the next click position (row and column indices) among all the unrevealed squares ('M' or 'E'), return the board after revealing this position according to the following rules:

If a mine ('M') is revealed, then the game is over - change it to 'X'.
If an empty square ('E') with no adjacent mines is revealed, then change it to revealed blank ('B') and all of its adjacent unrevealed squares should be revealed recursively.
If an empty square ('E') with at least one adjacent mine is revealed, then change it to a digit ('1' to '8') representing the number of adjacent mines.
Return the board when no more squares will be revealed.

**Example1**

![](../image/007.png)
**Example2**

![](../image/008.png)

**Node**

1. The range of the input matrix's height and width is [1,50].
2. The click position will only be an unrevealed square ('M' or 'E'), which also means the input board contains at least one clickable square.
3. The input board won't be a stage when game is over (some mines have been revealed).
4. For simplicity, not mentioned rules should be ignored in this problem. For example, you don't need to reveal all the unrevealed mines when the game is over, consider any cases that you will win the game or flag any squares.


### Explanation

简单说就是扫雷的实现...，这个好像没有太大的难度。简单的BFS search 就可以了..比之前Ｇoogle　Maze3简单多了..不过有几点要注意的是，如果该点是数字，那么不push到队列中，如果该点是白框(B),push附近的点，同时为了避免附近的点重复搜索，将附近符合的点改为'W'（改什么你自己喜欢）

**mark一个问题**

我在用que&lt;vector&lt;int&gt;&gt;与vector&lt;vector&lt;int&gt;&gt;  vc

```
为什么对于
for(auto i:temp){
    board[i[0]][i[1]] = 'W';
    que.emplace(i[0],i[1]);//不能emplace一个vector元素吗？
}
之后取出que的值不是我期望的值？DeBug时，我push进去的是[2,0],[2,1],[3,1],出来后的缺是[0,0],[1,1]???
```


### Code 

```
class Solution {
int rsize,csize;
queue<vector<int>> que;
vector<vector<int>> temp;
public:
    vector<vector<char>> updateBoard(vector<vector<char>>& board, vector<int>& click) {
        rsize = board.size();
        que.push(click);
        int mine;
        while(!que.empty()){
            vector<int> check = que.front();
            que.pop();
                /*if(board[check[0]][check[1]]=='B'||isdigit(board[check[0]][check[1]]))
                    continue;*/
            if(board[check[0]][check[1]]=='M'){
                board[check[0]][check[1]] = 'X';
                return board;
            }
            else{
                mine = checkSquare(board,check[0],check[1]);
                if(mine>0){
                    board[check[0]][check[1]] = mine + '0';
                }
                else{
                    board[check[0]][check[1]] = 'B';
                    for(int i=0;i<temp.size();++i){
                        que.emplace(temp[i][0],temp[i][1]);
                    }
                }
            }
            temp.clear();
        }
        return board;
    }
    
    int checkSquare(const vector<vector<char>>&board,int row,int col){
        int rstart = row-1<0?0:row-1,cstart = col-1<0?0:col-1;
        int rend = row+1>=rsize?rsize-1:row+1,cend = col+1>=csize?csize:col+1,count = 0;
        for(int i=rstart;i<=rend;++i){
            for(int j=cstart;j<=cend;++j){
                if(i==row&&j==col)
                    continue;
                if(board[i][j] == 'M')
                    ++count;
                else if(board[i][j] == 'E')
                    temp.push_back({i,j});
            }
        }
        return count;
    }
};

```