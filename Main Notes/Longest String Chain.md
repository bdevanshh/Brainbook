23-03-2026  16:09

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Longest String Chain

https://leetcode.com/problems/longest-string-chain/description/

- Sort the array based on the word length, so that the shorter words are considered when calculating for larger words.
- We can apply the same algorithm as lis, but we just need to check for predecessors validity.
- Return the maximum chain we can form.

```cpp
class Solution {
public:
    bool isPredecessor(string& a, string& b) {
        int n = a.size();
        int m = b.size();
		
        if(n == m || abs(n - m) > 1) {
            return false;
        }
		
        int i = 0;
        int j = 0;
		
        while(i < n) {
            if(j < m && a[i] == b[j]) {
                i++;
                j++;
            } else {
                i++;
            }
        }
		
        return i == n && j == m;
    }
	
    static bool compare(string& a, string& b) {
        return a.size() < b.size();
    }
	
    int longestStrChain(vector<string>& words) {
        int n = words.size();
		
        vector<int> dp(n, 1);
        int maxi = 1;
		
        sort(words.begin(), words.end(), compare);
		
        for(int i = 0; i < n; i++) {
            for(int j = 0; j < i; j++) {
                if(isPredecessor(words[i], words[j])) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
			
            maxi = max(maxi, dp[i]);
        }
		
        return maxi;
    }
};
```

|               **Time Complexity**                | **Space Complexity** |
| :----------------------------------------------: | :------------------: |
| $O(N \log N + N^2 * L)$ L = Length of the string |        $O(N)$        |





# References
[[Longest Increasing Subsequence]]