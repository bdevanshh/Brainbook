27-03-2026  14:58

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Palindrome Partitioning II

https://leetcode.com/problems/palindrome-partitioning-ii/

## Memoization

- Perform string partitions after every palindromic substring starting from `i`.
- Add a cost of 1 for every partition.
- At the last index the function will perform a partition even if the string is exhausted, so return `ans - 1` at the end.
- Memoize the string index.

```cpp
class Solution {
public:
    bool isPalindrome(int i, int j, string& s) {
        while(i < j) {
            if(s[i++] != s[j--]) {
                return false;
            }
        }
		
        return true;
    }
	
    int cut(int i, string& s, vector<int>& dp) {
        if(i >= s.size()) {
            return 0;
        }
		
        if(dp[i] != -1) {
            return dp[i];
        }
		
        int mini = INT_MAX;
        for(int j = i; j < s.size(); j++) {
            if(isPalindrome(i, j, s)) {
                mini = min(mini, 1 + cut(j + 1, s, dp));
            }
        }
		
        return dp[i] = mini;
    }
	
    int minCut(string s) {
        int n = s.size();
        vector<int> dp(n, -1);
		
        return cut(0, s, dp) - 1;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N * N)$      |      $O(N + N)$      |


## Tabulation

```cpp
class Solution {
public:
    bool isPalindrome(int i, int j, string& s) {
        while(i < j) {
            if(s[i++] != s[j--]) {
                return false;
            }
        }
		
        return true;
    }
	
    int minCut(string s) {
        int n = s.size();
        vector<int> dp(n + 1);
		
        for(int i = n - 1; i >= 0; i--) {
            int mini = INT_MAX;
            for(int j = i; j < s.size(); j++) {
                if(isPalindrome(i, j, s)) {
                    mini = min(mini, 1 + dp[j + 1]);
                }
            }
			
            dp[i] = mini;
        }
		
        return dp[0] - 1;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N*N)$       |        $O(N)$        |





# References