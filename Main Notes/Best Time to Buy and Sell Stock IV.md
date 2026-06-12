18-03-2026  16:34

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Best Time to Buy and Sell Stock IV

https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/

## Approach 1

- Same as previous problem but with k transactions instead of 2.

```cpp
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>> next(2, vector<int>(k + 1));
		
        for(int i = n - 1; i >= 0; i--) {
            vector<vector<int>> curr(2, vector<int>(k + 1));
			
            for(int canBuy = 0; canBuy <= 1; canBuy++) {
                for(int total = 1; total <= k; total++) {
                    if(canBuy) {
                        int notBuy = next[1][total];
                        int buy = next[0][total] - prices[i];
						
                        curr[canBuy][total] = max(buy, notBuy);
                    } else {
                        int notSell = next[0][total];
                        int sell = next[1][total - 1] + prices[i];
						
                        curr[canBuy][total] = max(sell, notSell);
                    }
                }
            }
			
            next = curr;
        }
		
        return next[1][k];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N*2*K)$      |       $O(2*K)$       |


## Approach 2

- Use a transaction number state that will represent if you can buy or not.

```plain
K = 3
Transaction No: 0 1 2 3 4 5
State:          B S B S B S
```

### Memoization

```cpp
class Solution {
public:
    int trade(int i, int tranNo, int k, vector<int>& prices, vector<vector<int>>& dp) {
        if(i == prices.size() || tranNo == k * 2) {
            return 0;
        }
		
        if(dp[i][tranNo] != -1) {
            return dp[i][tranNo];
        }
        
        // Can Buy
        if(tranNo % 2 == 0) {
            int notBuy = trade(i + 1, tranNo, k, prices, dp);
            int buy = trade(i + 1, tranNo + 1, k, prices, dp) - prices[i];
			
            return dp[i][tranNo] = max(buy, notBuy);
        } else {
            // Cannot Buy
            int notSell = trade(i + 1, tranNo, k, prices, dp);
            int sell = trade(i + 1, tranNo + 1, k , prices, dp) + prices[i];
			
            return dp[i][tranNo] = max(sell, notSell);
        }
    }
	
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
		
        vector<vector<int>> dp(n, vector<int>(k*2, -1));
        
        return trade(0, 0, k, prices, dp);
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N*K*2)$      |  $O(N*K*2) + O(N)$   |

### Tabulation

```cpp
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
		
        vector<vector<int>> dp(n + 1, vector<int>(k*2 + 1));
		
        for(int i = n - 1; i >= 0; i--) {
            for(int tranNo = 0; tranNo < k * 2; tranNo++) {
                // Can Buy
                if(tranNo % 2 == 0) {
                    int notBuy = dp[i + 1][tranNo];
                    int buy = dp[i + 1][tranNo + 1] - prices[i];
					
                    dp[i][tranNo] = max(buy, notBuy);
                } else {
                    // Cannot Buy
                    int notSell = dp[i + 1][tranNo];
                    int sell = dp[i + 1][tranNo + 1] + prices[i];
					
                    dp[i][tranNo] = max(sell, notSell);
                }
            }
        }
        
        return dp[0][0];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N*K*2)$      |      $O(N*K*2)$      |

### Space Optimization

```cpp
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
		
        vector<int> next(k*2 + 1);
		
        for(int i = n - 1; i >= 0; i--) {
            vector<int> curr(k*2 + 1);
            
            for(int tranNo = 0; tranNo < k * 2; tranNo++) {
                // Can Buy
                if(tranNo % 2 == 0) {
                    int notBuy = next[tranNo];
                    int buy = next[tranNo + 1] - prices[i];
					
                    curr[tranNo] = max(buy, notBuy);
                } else {
                    // Cannot Buy
                    int notSell = next[tranNo];
                    int sell = next[tranNo + 1] + prices[i];
					
                    curr[tranNo] = max(sell, notSell);
                }
            }
			
            next = curr;
        }
        
        return next[0];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N*K*2)$      |       $O(K*2)$       |





# References
[[Best Time to Buy and Sell Stock III]]