18-09-2025  15:51

Status: #Revision-04

Tags: [[Tags/DSA|DSA]] [[Sliding Window & Two Pointers Problems]]

# Maximum Points You Can Obtain from Cards

https://leetcode.com/problems/maximum-points-you-can-obtain-from-cards/

### Brute Force

- Take some elements from the left, and remaining from the right.
- Find the max total and return it.

```cpp
class Solution {
public:
    int maxScore(vector<int>& cardPoints, int k) {
        int ans = 0;
        
        for(int i = 0; i <= k; i++) {
            int total = 0;
            
            for(int j = 0; j < i; j++) {
                total += cardPoints[j];
            }
            
            for(int j = cardPoints.size() - 1; j > (cardPoints.size() - 1 - (k - i)); j--) {
                total += cardPoints[j];
            }
            
            ans = max(ans, total);
        }      
        
        return ans;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(K^2)$       |        $O(1)$        |


### Optimal

- Get sum of k element from left.
- Remove 1 element from left and add 1 from the right.
- Keep track of the max total.

```cpp
class Solution {
public:
    int maxScore(vector<int>& cardPoints, int k) {
        int leftSum = 0;
        int rightSum = 0;
        
        for(int i = 0; i < k; i++) {
            leftSum += cardPoints[i];
        }
        
        int ans = leftSum;
        
        // Remove from left and add from right
        for(int i = 1; i <= k; i++) {
            leftSum -= cardPoints[k - i];
            rightSum += cardPoints[cardPoints.size() - i];
            
            ans = max(ans, leftSum + rightSum);
        }
        
        return ans;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|       $O(2K)$       |        $O(1)$        |





# References