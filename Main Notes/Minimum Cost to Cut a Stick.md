25-03-2026  15:01

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Minimum Cost to Cut a Stick

https://leetcode.com/problems/minimum-cost-to-cut-a-stick/

## Memoization

- Sort the `cut` array, so that the sub-problems can be solved without checking for valid cut positions.
- Apply possible cut position and partition the cut array from that position.
- Also maintain the stick size while partitioning.
- Return the minimum cost required to cut the stick.
- Memoize the `cut` array's starting and ending indices.

```cpp
class Solution {
public:
    int cut(int i, int j, int start, int end, vector<int>& cuts, vector<vector<int>>& dp) {
        if(i > j) {
            return 0;
        }
		
        if(dp[i][j] != -1) {
            return dp[i][j];
        }
		
        int mini = INT_MAX;
		
        for(int k = i; k <= j; k++) {
            int l = cut(i, k - 1, start, cuts[k], cuts, dp);
            int r = cut(k + 1, j, cuts[k], end, cuts, dp);
			
            mini = min(mini, l + r + end - start);
        }
		
        return dp[i][j] = mini;
    }
	
    int minCost(int n, vector<int>& cuts) {
        sort(cuts.begin(), cuts.end());
        int m = cuts.size();
		
        vector<vector<int>> dp(m, vector<int>(m, -1));
		
        return cut(0, m - 1, 0, n, cuts, dp);
    }
};
```

|       **Time Complexity**       |               **Space Complexity**               |
| :-----------------------------: | :----------------------------------------------: |
| $O(M*M * M)$, M = `cuts.size()` | $O(M*M + \text{Stack Space})$, M = `cuts.size()` |


## Tabulation

- For getting the stick size add 0 to the front and `n` to the end of the `cut` array.
- The stick size at any state will be the `cut[j + 1] - cut[i - 1]`.

```cpp
class Solution {
public:
    int minCost(int n, vector<int>& cuts) {
        cuts.push_back(n);
        cuts.insert(cuts.begin(), 0);
        sort(cuts.begin(), cuts.end());
        int m = cuts.size();
		
        vector<vector<int>> dp(m, vector<int>(m));
		
        for(int i = m - 2; i >= 1; i--) {
            for(int j = i; j < m - 1; j++) {
                int mini = INT_MAX;
                
                for(int k = i; k <= j; k++) {
                    int l = dp[i][k - 1];
                    int r = dp[k + 1][j];
                    
                    mini = min(mini, l + r + cuts[j + 1] - cuts[i - 1]);
                }
                
                dp[i][j] = mini;
            }
        }
		
        return dp[1][m - 2];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|   $O(M * M * M)$    |       $O(M*M)$       |





# References