21-03-2026  14:42

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Largest Divisible Subset

https://leetcode.com/problems/largest-divisible-subset/

- Sort the array.
- By sorting the array all the elements that divides `nums[i]` will appear before `i` index.
- Now we can just store the total elements that divides the `ith` element in a dp array.
- With the dp array maintain a child array which will hold the previous index which divides `nums[i]` and generates the longest subset with current index.
- After filling in the dp, backtrack from the maximum answer index and return it.

```cpp
class Solution {
public:
    vector<int> largestDivisibleSubset(vector<int>& nums) {
        sort(nums.begin(), nums.end());
		
        int n = nums.size();
        vector<int> dp(n, 1);
        vector<int> child(n);
		
        for(int i = 0; i < n; i++) {
            child[i] = i;
        }
		
        int idx = 0;
        int maxi = 1;
		
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                if(nums[i] % nums[j] == 0) {
                    if(dp[j] + 1 > dp[i]) {
                        dp[i] = dp[j] + 1;
                        child[i] = j;
                    }
                }
            }
			
            if(dp[i] > maxi) {
                maxi = dp[i];
                idx = i;
            }
        }
		
        vector<int> ans;
		
        while(child[idx] != idx) {
            ans.push_back(nums[idx]);
            idx = child[idx];
        }
        ans.push_back(nums[idx]);
		
        return ans;   
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|    $O(N^2 + N)$     |       $O(2N)$        |





# References
[[Printing Longest Increasing Subsequence]]