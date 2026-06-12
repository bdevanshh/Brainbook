23-03-2026  16:50

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Longest Bitonic Sequence

https://www.naukri.com/code360/problems/longest-bitonic-sequence_1062688

- For every index i
	- Compute the LIS ending at i (`dp[i][0]`).
	- Compute the bitonic/decreasing sequence ending at i (`dp[i][1]`).
- Transition:
	- If `arr[j] < arr[i]`j, extend increasing sequence.
	- If `arr[j] > arr[i]`, start or continue decreasing from the best sequence at j.
- The answer is the maximum value among all states

```cpp
int longestBitonicSubsequence(vector<int>& arr, int n)
{
	vector<vector<int>> dp(n, vector<int>(2, 1));
	int maxi = 1;
	
	for(int i = 0; i < n; i++) {
		for(int j = 0; j < i; j++) {
			// Increasing sequence
			if(arr[j] < arr[i]) {
				dp[i][0] = max(dp[i][0], dp[j][0] + 1);
			// Decreasing sequence
			} else if(arr[j] > arr[i]) {
				// Make j the peak and start decreasing to i OR
				// Keep decerasing
				dp[i][1] = max(dp[i][1], max(dp[j][0], dp[j][1]) + 1);
			}
		}
		
		maxi = max(maxi, max(dp[i][0], dp[i][1]));
	}
	
	return maxi;
}
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |      $O(N * 2)$      |





# References