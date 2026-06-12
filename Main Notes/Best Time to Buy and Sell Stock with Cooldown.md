19-03-2026  14:34

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Best Time to Buy and Sell Stock with Cooldown

https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/

- Same as [[Best Time to Buy and Sell Stock II]], the only change being you can only buy at `i + 2` index after selling. 

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
		
        vector<int> next1(2);
        vector<int> next2(2);
		
        for(int i = n - 1; i >= 0; i--) {
            vector<int> curr(2);
			
            curr[1] = max(next1[0] - prices[i], next1[1]);
            curr[0] = max(next2[1] + prices[i], next1[0]);
			
            next2 = next1;
            next1 = curr;
        }
		
        return next1[1];
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|       $O(N)$        |        $O(1)$        |





# References
[[Best Time to Buy and Sell Stock II]]