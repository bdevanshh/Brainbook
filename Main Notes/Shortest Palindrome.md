13-04-2026  16:32

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Shortest Palindrome

https://leetcode.com/problems/shortest-palindrome/

### Brute Force

- Generate all the possible palindrome by combining the original string and its reverse.
- Start the combination from the shortest string.
- If the combined string becomes palindrome return it.

```cpp
class Solution {
public:
    bool isPalindrome(string& s) {
        int i = 0;
        int j = s.size() - 1;
		
        while(i < j) {
            if(s[i++] != s[j--]) {
                return false;
            }
        }
		
        return true;
    }
	
    string shortestPalindrome(string s) {
        if(isPalindrome(s)) {
            return s;
        }
		
        for(int i = s.size() - 1; i >= 0; i--) {
            string ans = s.substr(i, s.size() - i);
            reverse(ans.begin(), ans.end());
            ans += s;
			
            if(isPalindrome(ans)) {
                return ans;
            }
        }
		
        return "";
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |       $O(2N)$        |


### Optimal

- Create a combined string of `original$reverse`.
- Find the LPS (Longest Prefix which also Suffix) table of the combined string.
- Get the `suffix` substring from `reverse`, which is `reverse[0..original.size() - lps back()]`.
- Return `suffix + original`.

> The `lps.back()` stores no. of elements, which are matching in both the reverse and original string.
>
> We can prepend the reverse of remaining non matching character to make the string palindrome. 

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
                if(j != 0) {
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }
		
        return lps;
    }
	
    string shortestPalindrome(string s) {
        // Original: aacecaaa
        // Reverse: aaacecaa
        string reversed = s;
        reverse(reversed.begin(), reversed.end());
		
        // Combined: aacecaaa$aaacecaa
        string combinedString = s + "$" + reversed;
		
        // LPS: 01000122012234567
        vector<int> lps = findLPS(combinedString);
		
        // Suffix: a
        string suffix = reversed.substr(0, s.size() - lps.back());
		
        // Ans: aaacecaaa
        return suffix + s;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|       $O(M)$        |     $O(N + 2M)$      |
$M = 2N$





# References