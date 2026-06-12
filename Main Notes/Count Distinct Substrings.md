31-03-2026  15:40

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Count Distinct Substrings

https://www.naukri.com/code360/problems/count-distinct-substrings_985292

## Brute Force

- Generate all substrings and store them in a set.
- Return set's size + 1 as answer.

```cpp
int countDistinctSubstrings(string &s)
{
    int n = s.size();
    set<string> st;
	
    for(int i = 0; i < n; i++) {
        string str = "";
		
        for(int j = i; j < n; j++) {
            str += s[j];
            st.insert(str);
        }
    }
	
    return st.size() + 1;
}
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|   $O(N^2 \log M)$   |        $O(M)$        |
M = No. of distinct substring $O(N^3)$


## Optimal

- Instead of set use a trie.
- Whenever a new node is created increase the distinct substrings count.

```cpp
#include <bits/stdc++.h>

struct Node {
    Node* links[26];
	
    bool containsKey(char ch) {
        return links[ch - 'a'] != NULL;
    }
	
    Node* get(char ch) {
        return links[ch - 'a'];
    }
	
    void set(char ch) {
        links[ch - 'a'] = new Node();
    }
};

int countDistinctSubstrings(string &s)
{
    int n = s.size();
    int total = 0;
    Node* root = new Node();
	
    for(int i = 0; i < n; i++) {
        Node* curr = root;
		
        for(int j = i; j < n; j++) {
            if(!curr->containsKey(s[j])) {
                curr->set(s[j]);
                total++;
            }
			
            curr = curr->get(s[j]);
        }
    }
	
    return total + 1;
}
```

| **Time Complexity** |
| :-----------------: |
|      $O(N^2)$       |





# References