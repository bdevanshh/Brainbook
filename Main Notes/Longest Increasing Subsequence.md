20-03-2026  12:23

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Longest Increasing Subsequence

https://leetcode.com/problems/longest-increasing-subsequence/

## Approach 1
### Memoization

- Use the pick & not pick algorithm.
- When you pick an element, it should be greater than previously taken element.
- Memoize the index and previous element's index.

```cpp
class Solution {
public:
    int find(int i, int prev, vector<int>& nums, vector<vector<int>>& dp) {
        if(i >= nums.size()) {
            return 0;
        }
		
        if(dp[i][prev + 1] != -1) {
            return dp[i][prev + 1];
        }
		
        int notTake = find(i + 1, prev, nums, dp);
        int take = 0;
		
        if(prev == -1 || nums[i] > nums[prev]) {
            take = find(i + 1, i, nums, dp) + 1;
        }
		
        return dp[i][prev + 1] = max(take, notTake);
    }
	
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> dp(n, vector<int>(n + 1, -1));
		
        return find(0, -1, nums, dp);
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N*N)$       |   $O(N*N) + O(N)$    |

### Tabulation

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> dp(n + 1, vector<int>(n + 1));
		
        for(int i = n - 1; i >= 0; i--) {
            for(int j = 0; j <= i; j++) {
                int notTake = dp[i + 1][j];
                int take = 0;
				
                if(j == 0 || nums[i] > nums[j - 1]) {
                    take = dp[i + 1][i + 1] + 1;
                }
				
                dp[i][j] = max(take, notTake);
            }
        }
		
        return dp[0][0];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N*N)$       |       $O(N*N)$       |

### Space Optimization

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> next(n + 1);
		
        for(int i = n - 1; i >= 0; i--) {
            vector<int> curr(n + 1);
			
            for(int j = 0; j <= i; j++) {
                int notTake = next[j];
                int take = 0;
				
                if(j == 0 || nums[i] > nums[j - 1]) {
                    take = next[i + 1] + 1;
                }
				
                curr[j] = max(take, notTake);
            }
			
            next = curr;
        }
		
        return next[0];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N*N)$       |        $O(N)$        |


## Approach 2

- For every index, calculate the lis that ends at that index.
- To form a lis, find all the smaller elements before that index and store the maximum length you can get.
- The answer will be the maximum length from all the index.

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> dp(n, 1);
        int maxi = 1;
		
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                if(nums[j] < nums[i]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                    maxi = max(maxi, dp[i]);
                }
            }
        }
		
        return maxi;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |        $O(N)$        |


## Approach 3

- Create a dummy lis array.
- Traverse over the array and add element to the array if they are larger than the back.
- Else change the element at the possible location of that element in existing array.
- Return the array length.

```cpp
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<int> lis;
		
        for(int i = 0; i < n; i++) {
            if(i == 0 || nums[i] > lis.back()) {
                lis.push_back(nums[i]);
            } else {
                int idx = lower_bound(lis.begin(), lis.end(), nums[i]) - lis.begin();
                lis[idx] = nums[i];
            }
        }
		
        return lis.size();
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|    $O(N \log N)$    |        $O(N)$        |





# References