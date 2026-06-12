16-04-2026  14:17

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Strings]]

# Palindromic Substrings

https://leetcode.com/problems/palindromic-substrings/

### Brute Force

- Generate all the substring of the string.
- If the substring is palindrome increase the total counter.

```cpp
class Solution {
public:
    bool isPalindrome(string s) {
        int i = 0;
        int j = s.size() - 1;
		
        while(i < j) {
            if(s[i++] != s[j--]) {
                return false;
            }
        }
		
        return true;
    }
	
    int countSubstrings(string s) {
        int n = s.size();
        int total = 0;
		
        for(int i = 0; i < n; i++) {
            for(int j = i; j < n; j++) {
                if(isPalindrome(s.substr(i, j - i + 1))) {
                    total++;
                }
            }
        }
		
        return total;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |        $O(N)$        |


### Better

- Iterate over the string and start expanding from that index till its palindrome.
- Expand for both even and odd length strings.
- Maintain a total count and return it.

```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int total = 0;
		
        for(int i = 0; i < s.size(); i++) {
			// Odd length palindromes
            total += expand(s, i, i);
			
			// Even length palindromes
            total += expand(s, i, i + 1);
        }
		
        return total;
    }
    
    int expand(string& s, int left, int right) {
        int total = 0;
		
        while(left >= 0 && right < s.size() && s[left] == s[right]) {
            total++;
			
            left--;
            right++;
        }
		
        return total;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |        $O(1)$        |





# References