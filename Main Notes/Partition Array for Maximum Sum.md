27-03-2026  15:51

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Partition Array for Maximum Sum

https://leetcode.com/problems/partition-array-for-maximum-sum/

## Memoization

- Perform partitions from `i` to `i + k`.
- Add `substring lenght * maximum element` at every partition, and add the result of the other substring.
- Return the maximum ans across all the partition.
- Memoize the index of the array.

```cpp
class Solution {
public:
    int partition(int i, int k, vector<int>& arr, vector<int>& dp) {
        if(i >= arr.size()) {
            return 0;
        }
		
        if(dp[i] != -1) {
            return dp[i];
        }
		
        int maxi = INT_MIN;
        int maxiEl = -1;
		
        for(int j = i; j < arr.size() && j < i + k; j++) {
            maxiEl = max(maxiEl, arr[j]);
            maxi = max(maxi, (j - i + 1) * maxiEl + partition(j + 1, k, arr, dp));
        }
		
        return dp[i] = maxi;
    }
	
    int maxSumAfterPartitioning(vector<int>& arr, int k) {
        int n = arr.size();
        vector<int> dp(n, -1);
        
        return partition(0, k, arr, dp);
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N * K)$      |      $O(N + N)$      |


## Tabulation

```cpp
class Solution {
public:
    int maxSumAfterPartitioning(vector<int>& arr, int k) {
        int n = arr.size();
        vector<int> dp(n + 1);
		
        for(int i = n - 1; i >= 0; i--) {
            int maxi = INT_MIN;
            int maxiEl = -1;
			
            for(int j = i; j < arr.size() && j < i + k; j++) {
                maxiEl = max(maxiEl, arr[j]);
                maxi = max(maxi, (j - i + 1) * maxiEl + dp[j + 1]);
            }
			
            dp[i] = maxi;
        }
		
        return dp[0];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N * K)$      |        $O(N)$        |





# References