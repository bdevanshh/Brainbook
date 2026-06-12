08-04-2026  17:03

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Strings]]

# Count and Say

https://leetcode.com/problems/count-and-say/

- Start from `n = 2`, the previous will `1` from `n = 1`.
- For `n` time,
	- Generate RLE (Run-length Encoding) from the previous string.
	- Update the previous. 
- Return the final string.

```cpp
class Solution {
public:
    string countAndSay(int n) {
        if(n == 1) {
            return "1";
        }
		
        string prev = "1";
		
        for(int i = 2; i <= n; i++) {
            string rle = "";
            int count = 1;
			
            for(int j = 1; j < prev.size(); j++) {
                if(prev[j] == prev[j - 1]) {
                    count++;
                } else  {
                    rle += to_string(count) + prev[j - 1];
                    count = 1;
                }
            }
			
            rle += to_string(count) + prev.back();
            prev = rle;
        }
        
        return prev;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|    $O(N * 2^N)$     |       $O(2^N)$       |





# References