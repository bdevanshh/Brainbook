09-04-2026  16:05

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Strings]]

# Rabin Karp Algorithm

https://leetcode.com/problems/repeated-string-match/

- Rabin Karp is an algorithm used to find substring in a string using string hashing.
- For hashing the string,
	- We can map each character to an integer.
	- We can create a single integer by using a base like 29 or 31.
- First create a string `s` which is long enough to store the string `b`.
- Then check if it has substring `b`, if not add `a` to the string again and check.
- For checking the substring using Rabin Karp,
	- Find the hash of the pattern string.
	- Find the hash of the first `m` chars of source string.
	 - For every char after `m`, remove the last character's hash value and check.
	- If hashes are same, perform explicit check for equality because there can be collisions in hash values.

> It is guaranteed that, if `b` is substring of repeated `a`, then repeating `a` in `s` when `s.size() >= b.size()` will contain `b`.

![[Pasted image 20260409175736.png]]

```cpp
class Solution {
public:
    int MOD = 1e6;
    int BASE = 29;
	
    int hashChar(char ch) {
        return (ch - 'a') + 1;
    }
	
    bool rabinKarp(string source, string pattern) {
        int n = source.size();
        int m = pattern.size();
		
		// BASE^M (For removing char from hash values)
        int lastPower = 1;
        for(int i = 0; i < m; i++) {
            lastPower = (lastPower * BASE) % MOD;
        }
		
		// Pattern string hash
        int pHash = 0;
        for(int i = 0; i < m; i++) {
            pHash = ((pHash * BASE) + hashChar(pattern[i])) % MOD;
        }
		
		// Source string hash
        int sHash = 0;
        for(int i = 0; i < n; i++) {
            sHash = ((sHash * BASE) + hashChar(source[i])) % MOD;
			
			// Remove the last char
            if(i >= m) {
                sHash = (sHash - hashChar(source[i - m]) * lastPower) % MOD;
            }
			
            if(sHash < 0) {
                sHash += MOD;
            }
			
            if(sHash == pHash && source.substr(i - m + 1, m) == pattern) {
                return true;
            }
        }
		
        return false;
    }
	
    int repeatedStringMatch(string a, string b) {
		// O(M)
        string s = a;
        int count = 1;
		
        while(s.size() < b.size()) {
            s += a;
            count++;
        }
		
        if(rabinKarp(s, b)) {
            return count;
        }
		
        if(rabinKarp(s + a, b)) {
            return count + 1;
        }
		
        return -1;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N+M)$       |        $O(M)$        |





# References