11-08-2025  14:52

Status: #Revision-04

Tags: [[Tags/DSA]] [[Stack]] [[Dynamic Programming]]

# Maximal Rectangle

https://leetcode.com/problems/maximal-rectangle/description/

```
matrix = [[1, 0], [0, 1], [1, 1]]

prefix:
[1, 0] ---
------   |
[0, 1]   | get the max rectangle
------   |
[1, 2] ---
```

- Visualize each row as a separate histogram.
	- Perform prefix sum of the column to get the height of the bar.
	- Reset if 0 occurs.
- Find the largest rectangle for each row.

```cpp
class Solution {
public:
    int findMaxRectangle(vector<int>& nums) {
        stack<int> st;
        int maxRect = 0;
		
        for(int i = 0; i < nums.size(); i++) {
            while(!st.empty() && (nums[st.top()] > nums[i])) {
                int el = st.top();
                st.pop();
				
                int nse = i;
                int pse = st.empty() ? -1 : st.top();
				
                maxRect = max(maxRect, nums[el] * (nse - pse - 1));
            }
			
            st.push(i);
        }
		
        while(!st.empty()) {
            int el = st.top();
            st.pop();
			
            int nse = nums.size();
            int pse = st.empty() ? -1 : st.top();
			
            maxRect = max(maxRect, nums[el] * (nse - pse - 1));
        }
		
        return maxRect;
    }
	
    int maximalRectangle(vector<vector<char>>& matrix) {
        int ans = 0;
        vector<vector<int>> prefix(matrix.size(), vector<int>(matrix[0].size()));
		
        for(int j = 0; j < matrix[0].size(); j++) {
            int prefixSum = 0;
			
            for(int i = 0; i < matrix.size(); i++) {
                prefixSum += matrix[i][j] - '0';
				
                if(matrix[i][j] == '0') {
                    prefixSum = 0;
                }
				
                prefix[i][j] = prefixSum;
            }
        }
        
        for(int i = 0; i < matrix.size(); i++) {
            ans = max(ans, findMaxRectangle(prefix[i]));
        }
		
        return ans;
    }
};
```

|  **Time Complexity**   | **Space Complexity** |
| :--------------------: | :------------------: |
| $O(N * M) + O(N * 2M)$ |  $O(N * M) + O(N)$   |





# References

[[Largest Rectangle in Histogram]]