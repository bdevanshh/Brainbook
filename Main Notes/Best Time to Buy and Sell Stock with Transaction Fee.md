19-03-2026  15:13

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Best Time to Buy and Sell Stock with Transaction Fee

https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/

- Exactly same as [[Best Time to Buy and Sell Stock II]].
- Only change is when you sell a stock subtract the given fee.

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        vector<int> next(2);
		
        for(int i = n - 1; i >= 0; i--) {
            vector<int> curr(2);
			
            curr[0] = max(next[0], next[1] + prices[i] - fee);
            curr[1] = max(next[1], next[0] - prices[i]);
			
            next = curr;
        }
		
        return next[1];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|       $O(N)$        |        $O(1)$        |
 




# References
[[Best Time to Buy and Sell Stock II]]