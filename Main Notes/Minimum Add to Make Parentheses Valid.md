08-04-2026  16:49

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Strings]]

# Minimum Add to Make Parentheses Valid

https://leetcode.com/problems/minimum-add-to-make-parentheses-valid/

- Count the total number of parentheses that is still remaining.
- If `(` found, increase the opening count.
- Else if `)` found, decrease the opening count if it is > 0, else increase the total count.
- Return the total + remaining opening count.

```cpp
class Solution {
public:
    int minAddToMakeValid(string s) {
        int total = 0;
        int opening = 0;
        int closing = 0;
		
        for(int i = 0; i < s.size(); i++) {
            if(s[i] == '(') {
                opening++;
            } else {
                if(opening == 0) {
                    total++;
                } else {
                    opening--;
                }
            }
        }
		
        return total + opening;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|       $O(N)$        |        $O(1)$        |





# References