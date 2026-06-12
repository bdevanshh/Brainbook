16-04-2026  13:23

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Strings]]

# Longest Happy Prefix

https://leetcode.com/problems/longest-happy-prefix/

### Brute Force

- For every prefix of the string, generate suffix.
- Start from the largest, and return the prefix if found.

```cpp
class Solution {
public:
    string longestPrefix(string s) {
        int n = s.size();
		
        for(int i = n - 1; i > 0; i--) {
            string prefix = s.substr(0, i);
            string suffix = s.substr(n - i);
            
            if(prefix == suffix) {
                return prefix;
            }
        }
		
        return "";
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |        $O(N)$        |


### Optimal

- Generate the lps (Longest Prefix which also Suffix) table from the string. 
- The last index will have the length of the longest prefix which is also the suffix.
- Return the prefix of the string with that length.

```cpp
class Solution {
public:
    vector<int> findLPS(string& s) {
        int n = s.size();
        vector<int> lps(n);
		
        int i = 1;
        int j = 0;
		
        while(i < n) {
            if(s[i] == s[j]) {
                j++;
                lps[i] = j;
                i++;
            } else {
                if(j > 0) {
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }
		
        return lps;
    }
	
    string longestPrefix(string s) {
        int n = s.size();
        vector<int> lps = findLPS(s);
		
        if(lps.back() == 0) {
            return "";
        }
		
        return s.substr(0, lps.back());
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|       $O(N)$        |        $O(N)$        |





# References