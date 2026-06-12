24-03-2026  15:43

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Matrix Chain Multiplication

https://www.naukri.com/code360/problems/matrix-chain-multiplication_975344

## Memoization

- Partition the array at different positions.
- Calculate the total operations it takes to multiply two partitions and add the partitions operation count also.
- Return the minimum operations required across all the possible partitions.
- Memoize the partition results.

```cpp
#include <bits/stdc++.h> 

int multiply(int i, int j, vector<int>& arr, vector<vector<int>>& dp) {
    if(i == j) {
        return 0;
    }
	
    if(dp[i][j] != -1) {
        return dp[i][j];
    }
	
    int mini = INT_MAX;
	
    for(int k = i; k < j; k++) {
        int left = multiply(i, k, arr, dp);
        int right = multiply(k + 1, j, arr, dp);
        int steps = arr[i - 1] * arr[k] * arr[j];
		
        mini = min(mini, left + right + steps);
    }
	
    return dp[i][j] = mini;
}

int matrixMultiplication(vector<int> &arr, int N)
{
    vector<vector<int>> dp(N, vector<int>(N, -1));
	
    return multiply(1, N - 1, arr, dp);
}
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|    $O(N*N * N)$     |     $O(N*N + N)$     |


## Tabulation

```cpp
#include <bits/stdc++.h> 

int matrixMultiplication(vector<int> &arr, int N)
{
    vector<vector<int>> dp(N, vector<int>(N));
	
    for(int i = 1; i < N; i++) {
        dp[i][i] = 0;
    }
	
    for(int i = N - 1; i >= 0; i--) {
        for(int j = i + 1; j < N; j++) {
            int mini = INT_MAX;
			
            for(int k = i; k < j; k++) {
                int left = dp[i][k];
                int right = dp[k + 1][j];
                int steps = arr[i - 1] * arr[k] * arr[j];
				
                mini = min(mini, left + right + steps);
            }
			
            dp[i][j] = mini;
        }
    }
	
    return dp[1][N - 1];
}
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^3)$       |       $O(N^2)$       |





# References