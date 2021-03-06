## 最长上升子序列
```C++
class Solution {
public:
    /**
     * @param nums: The integer array
     * @return: The length of LIS (longest increasing subsequence)
     */
    int longestIncreasingSubsequence(vector<int> nums) {
        // write your code here
        int len = nums.size();
        if(len == 0) return 0;
        
        vector<int> dp(len, 0);
        for(int i=0;i<len;i++) {
            dp[i] = 1;
        }
        for(int i=1;i<len;i++) {
            for(int j=0;j<i;j++) {
                if(nums[j] < nums[i]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
        }
        return dp[len - 1];
    }
};

```

## 拓扑排序
```C++
/**
 * Definition for Directed graph.
 * struct DirectedGraphNode {
 *     int label;
 *     vector<DirectedGraphNode *> neighbors;
 *     DirectedGraphNode(int x) : label(x) {};
 * };
 */
class Solution {
public:
    /**
     * @param graph: A list of Directed graph node
     * @return: Any topological order for the given graph.
     */
    vector<DirectedGraphNode*> topSort(vector<DirectedGraphNode*> graph) {
        // write your code here
        vector<DirectedGraphNode*> res;
        if(graph.empty()) {
            return res;
        }
        map<DirectedGraphNode*, int> hash;
        for(auto item : graph) {
            if(hash.find(item) == hash.end()) {
                hash[item] = 0;
            }
            for(auto cell : item->neighbors) {
                hash[cell]++;
            }
        }
        stack<DirectedGraphNode*> s;
        for(auto item : hash) {
            if(item.second == 0) {
                s.push(item.first);
                //cout << "stack push !" << endl;
            }
        }
        
        while(!s.empty()) {
            DirectedGraphNode* tmp = s.top();
            s.pop();
            res.push_back(tmp);
            for(auto cell : tmp->neighbors) {
                hash[cell]--;
                if(hash[cell] == 0) {
                    s.push(cell);
                }
            }
        }
        return res;
    }
};
```

## 最长回文子串
```C++
class Solution {
public:
    /*
     * @param s: input string
     * @return: the longest palindromic substring
     */
    string longestPalindrome(string s) {
        // write your code here
        int len = s.size();
        if(len <= 1) return s;
        vector<vector<bool>> dp(1000, vector<bool>(1000, false));
        for(int i=0;i<1000;i++) {
            dp[i][i] = true;
        }
        int startIdx = 0, maxlen = 0;
        for(int i=0;i<len-1;i++) {
            if(s[i] == s[i+1]) {
                dp[i][i+1] = true;
                startIdx = i;
                maxlen = 2;
            }
        }
        for(int k=3; k<=len; k++) {
            for(int i=0;i<len-k+1;i++) {
                int j = i+k-1;
                if(s[i] == s[j] && dp[i+1][j-1]) {
                    dp[i][j] = true;
                    maxlen = k;
                    startIdx = i;
                }
            }
        }
        if(maxlen >= 2) {
            return s.substr(startIdx, maxlen);
        }
        return "";
    }
};
```
## 最大正方形
```bash
1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0

返回 4
```

```C++
class Solution {
public:
    /**
     * @param matrix: a matrix of 0 and 1
     * @return: an integer
     */
    int maxSquare(vector<vector<int> > &matrix) {
        // write your code here
        int row = matrix.size();
        if (row == 0) return 0;
        int col = matrix[0].size();
        vector<vector<int>> dp(row, vector<int>(col, 0));
        for(int i=0;i<row;i++) {
            dp[i][0] = matrix[i][0];
        }
        for(int i=0;i<col;i++) {
            dp[0][i] = matrix[0][i];
        }
        int max_len = dp[0][0];
        for(int i=1;i<row;i++) {
            for(int j=1;j<col;j++) {
                if(matrix[i][j] == 1) {
                    dp[i][j] = min(min(dp[i-1][j], dp[i][j-1]), dp[i-1][j-1]) + 1;
                    max_len = max(max_len, dp[i][j]);
                } else {
                    dp[i][j] = 0;
                }
            }
        }
        return max_len * max_len;
    }
};
```
## 第K个排列
```C++
class Solution {
public:
    /*
     * @param n: n
     * @param k: the k th permutation
     * @return: return the k-th permutation
     */
    string getPermutation(int n, int k) {
        // write your code here
        string res = "";
        int prod = 1;
        vector<int> nums(n, 0);
        for(int i=0;i<n;i++) {
            nums[i] = i + 1;
            prod *= (i+1);
        }
        k--;
        for(int i=0;i<n;i++) {
            prod = prod / (n-i);
            int c = k / prod;
            res += ('0' + nums[c]);
            for(int j=c;j<n-i;j++) {
                nums[j] = nums[j+1];
            }
            k %= prod;
        }
        return res;
    }
};
```

## 和为零的子矩阵
```
[
  [1 ,5 ,7],
  [3 ,7 ,-8],
  [4 ,-8 ,9],
]
```
返回 [(1,1), (2,2)]

```C++
class Solution {
public:
    /**
     * @param matrix an integer matrix
     * @return the coordinate of the left-up and right-down number
     */
    vector<vector<int>> submatrixSum(vector<vector<int>>& matrix) {
        // Write your code here
        vector<vector<int>> res(2, vector<int>(2, 0));
        int row = matrix.size();
        if(row == 0) return res;
        int col = matrix[0].size();
        vector<vector<int>> dp(row+1, vector<int>(col+1, 0));
        for(int i=0;i<=row;i++) dp[i][0] = 0;
        for(int i=0;i<=col;i++) dp[0][i] = 0;
        for(int i=0;i<row;i++) {
            for(int j=0;j<col;j++) {
                dp[i+1][j+1] = matrix[i][j] + dp[i+1][j] + dp[i][j+1] - dp[i][j];
            }
        }
        for(int l=0;l<row;l++) {
            for(int h=l+1;h<=row;h++) {
                unordered_map<int, int> hash;
                for(int j=0;j<=col;j++) {
                    int diff = dp[h][j] - dp[l][j];
                    if(hash.find(diff) != hash.end()) {
                        int k = hash[diff];
                        res[0][0] = l, res[0][1] = k;
                        res[1][0] = h-1, res[1][1] = j-1;
                        return res;
                    } else {
                        hash[diff] = j;
                    }
                }
            }
        }
        return res;
    }
};
```
