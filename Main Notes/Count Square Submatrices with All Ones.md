28-03-2026  15:36

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Count Square Submatrices with All Ones

https://leetcode.com/problems/count-square-submatrices-with-all-ones/

- For every cells in the matrix, count how many times that cell will be the **bottom-right cell of a square**.
- The topmost row and leftmost column's count  would be the same as the matrix. Because forming a `2x2` square will go out of bounds.
- For every other cells, the count would be `1 + minimum([i - 1][j - 1], [i][j - 1], [j][i - 1])`.
- Return the total of all the count.

> The count at a particular cells represent its maximum square forming ability.
> 
> So, a count of 1 can only make `1x1` square. And to extend it, all the neighboring cell should have similar count. And that is why we take the minimum from all the neighbors.

```cpp
class Solution {
public:
    int countSquares(vector<vector<int>>& matrix) {
        int n = matrix.size();
        int m = matrix[0].size();
        int total = 0;
		
        vector<vector<int>> dp(n, vector<int>(m));
		
        for(int i = 0; i < n; i++) {
            dp[i][0] = matrix[i][0];
        }
		
        for(int i = 0; i < m; i++) {
            dp[0][i] = matrix[0][i];
        }
		
        for(int i = 1; i < n; i++) {
            for(int j = 1; j < m; j++) {
                if(matrix[i][j]) {
                   dp[i][j] = 1  + min(dp[i - 1][j - 1], min(dp[i - 1][j], dp[i][j - 1]));
                }
            }
        }
		
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < m; j++) {
                total += dp[i][j];
            }
        }
		
        return total;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N*M)$       |       $O(N*M)$       |





# References