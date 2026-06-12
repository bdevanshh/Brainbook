26-03-2026  13:13

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Burst Balloons

https://leetcode.com/problems/burst-balloons/

## Memoization

- Instead of bursting balloons, reverse the problem and select balloons for not bursting.
- If you select a balloon to not burst, calculate the coins, if you burst it.
- Return the maximum coins which can be obtained across all the possible balloon selection.
- Memoize the indices that is used for selecting non bursting balloons.

```cpp
class Solution {
public:
    int burst(int i, int j, vector<int>& nums, vector<vector<int>>& dp) {
        if(i > j) {
            return 0;
        }
		
        if(dp[i][j] != -1) {
            return dp[i][j];
        }
		
        int maxi = INT_MIN;
        for(int k = i; k <= j; k++) {
            int l = burst(i, k - 1, nums, dp);
            int r = burst(k + 1, j, nums, dp);
            int cost = nums[i - 1] * nums[k] * nums[j + 1];
			
            maxi = max(maxi, l + r + cost);
        }
		
        return dp[i][j] = maxi;
    }
	
    int maxCoins(vector<int>& nums) {
        nums.insert(nums.begin(), 1);
        nums.push_back(1);
        int n = nums.size();
		
        vector<vector<int>> dp(n, vector<int>(n, -1));
		
        return burst(1, n - 2, nums, dp);
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|    $O(N*N * N)$     |     $O(N*N + N)$     |


## Tabulation

```cpp
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        nums.insert(nums.begin(), 1);
        nums.push_back(1);
        int n = nums.size();
		
        vector<vector<int>> dp(n, vector<int>(n));
		
        for(int i = n - 2; i >= 1; i--) {
            for(int j = i; j <= n - 2; j++) {
                int maxi = INT_MIN;
                for(int k = i; k <= j; k++) {
                    int l = dp[i][k - 1];
                    int r = dp[k + 1][j];
                    int cost = nums[i - 1] * nums[k] * nums[j + 1];
					
                    maxi = max(maxi, l + r + cost);
                }
				
                dp[i][j] = maxi;
            }
        }
		
        return dp[1][n - 2];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N*N*N)$      |       $O(N*N)$       |





# References