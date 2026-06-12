20-03-2026  13:59

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Printing Longest Increasing Subsequence

https://www.naukri.com/code360/problems/printing-longest-increasing-subsequence_8360670

- We can use the Approach 2 from [[Longest Increasing Subsequence]] .
- With the dp array maintain a child array which will hold the previous index which generated the maximum lis for current index.
- After filling in the dp, backtrack from the maximum answer index and return it.

```cpp
vector<int> printingLongestIncreasingSubsequence(vector<int> arr, int n) {
	vector<int> lis;
	vector<int> dp(n, 1);
	vector<int> child(n);
	
	for(int i = 0; i < n; i++) {
		child[i] = i;
	}
	
    int maxi = 1;
	int idx = 0;
		
    for(int i = 0; i < n; i++) {
        for(int j = 0; j < i; j++) {
			if(arr[j] < arr[i]) {
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
	
	while(child[idx] != idx) {
		lis.push_back(arr[idx]);
		idx = child[idx];
	}
	lis.push_back(arr[idx]);
	
	reverse(lis.begin(), lis.end());
	
	return lis;
}
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |       $O(2N)$        |





# References
[[Longest Increasing Subsequence]]