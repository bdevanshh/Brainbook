12-04-2026  14:50

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Strings]]

# KMP Algorithm

https://leetcode.com/problems/find-the-index-of-the-first-occurrence-in-a-string/

### Brute Force

- For every index in the array, start matching the pattern from that index.
- If you find the entire pattern, return the index.

```cpp
class Solution {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.size();
        int m = needle.size();
		
        for(int k = 0; k < n; k++) {
            int i = k;
            int j = 0;
            while(i < n && j < m && haystack[i] == needle[j]) {
                i++;
                j++;
            }
			
            if(j == m) {
                return k;
            }
        }
		
        return -1;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N * M)$      |        $O(1)$        |


### Optimal (Knuth Morris Pratt Algorithm)

- KMP algorithm is used for string pattern matching.
- It works by creating a LPS (Longest Prefix which is also Suffix) array from the pattern.
- Each indices of LPS array contains the length of the suffix which is also the prefix of the pattern.
- Next we match the string and pattern.
- If the characters match, move both pointers.
- If characters doesn't match, instead of comparing pattern all over again, we can move the `j` pointer to the `lcs[j - 1]`. (Index of the pattern having similar prefix)
- If the entire pattern is matched move the `j` pointer again to the common prefix.

```cpp
class Solution {
public:
	// O(M)
    vector<int> findLPS(string& needle) {
        int n = needle.size();
        vector<int> lps(n);
		
        int i = 1;
        int j = 0;
        while(i < n) {
            if(needle[i] == needle[j]) {
                j++;
                lps[i] = j;
                i++;
            } else {
                if(j != 0) {
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }
		
        return lps;
    } 
	
    int strStr(string haystack, string needle) {
        int n = haystack.size();
        int m = needle.size();
		
        vector<int> lps = findLPS(needle);
        int i = 0;
        int j = 0;
		
		// O(N)
        while(i < n) {
            if(haystack[i] == needle[j]) {
                i++;
                j++;
				
                if(j == m) {
                    return i - j;
                }
            } else {
                if(j != 0) {
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }
		
        return -1;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(N + M)$      |        $O(M)$        |





# References