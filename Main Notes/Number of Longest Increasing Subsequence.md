23-03-2026  17:33

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Number of Longest Increasing Subsequence

https://leetcode.com/problems/number-of-longest-increasing-subsequence/

- For every index i, compute the LIS length ending at i using dp.
- Keep a `count[i]` that stores how many LIS end at `i`.
- If a longer LIS is found, copy `count[j]`.
- If another way gives the same LIS length, add `count[j]` to `count[i]`.
- Track the maximum LIS length, and return total count whose LIS length is maxi.

```cpp
class Solution {
public:
    int findNumberOfLIS(vector<int>& nums) {
        int n = nums.size();
		
        vector<int> dp(n, 1);
        vector<int> count(n, 1);
        int maxi = 1;
		
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                if(nums[j] < nums[i]) {
                    if(dp[j] + 1 > dp[i]) {
                        dp[i] = dp[j] + 1;
                        count[i] = count[j];
                    } else if(dp[j] + 1 == dp[i]) {
                        count[i] += count[j];
                    }
                }
            }
			
            maxi = max(maxi, dp[i]);
        }
		
        int total = 0;
		
        for(int i = 0; i < n; i++) {
            if(dp[i] == maxi) {
                total += count[i];
            }
        }
		
        return total;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |       $O(2N)$        |





# References
[[Longest Increasing Subsequence]]