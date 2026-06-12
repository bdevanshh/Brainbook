31-03-2026  13:53

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Trie]]

# Complete String

https://www.naukri.com/code360/problems/complete-string_2687860

- Insert all the words in trie.
- Now again traverse over the word and find all the prefix of the word in trie by traversing and checking all the links are marked.
- Return the longest lexicographically smallest word.

```cpp
#include <bits/stdc++.h> 

struct Node {
private:
    Node* links[26];
    bool flag = false;
	
public:
    bool containKey(char ch) {
        return links[ch - 'a'] != NULL;
    }
	
    Node* get(char ch) {
        return links[ch - 'a'];
    }
	
    void set(char ch) {
        links[ch - 'a'] = new Node();
    }
	
    void mark() {
        flag = true;
    }
	
    bool isWord() {
        return flag;
    }
};

class Trie {
private:
    Node* root;
	
public:
    Trie() {
        root = new Node();
    }
	
    void insert(string& word) {
        Node* curr = root;
		
        for(int i = 0; i < word.size(); i++) {
            if(!curr->containKey(word[i])) {
                curr->set(word[i]);
            }
			
            curr = curr->get(word[i]);
        }
		
        curr->mark();
    }
	
    bool checkPrefix(string& word) {
        Node* curr = root;
		
        for(int i = 0; i < word.size(); i++) {
            if(!curr->containKey(word[i])) {
                return false;
            }
			
            curr = curr->get(word[i]);
            if(!curr->isWord()) {
                return false;
            }
        }
		
        return true;
    }
};

string completeString(int n, vector<string> &a){
    Trie t;
    
    for(auto& word: a) {
        t.insert(word);
    }
	
    string ans = "";
	
    for(auto& word: a) {
        if(t.checkPrefix(word)) {
            if(word.size() > ans.size()) {
                ans = word;
            } else if(word.size() == ans.size() && word < ans) {
                ans = word;
            }
        }
    }
	
    if(ans.empty()) {
        return "None";
    }
	
    return ans;
}
```

|   **Time Complexity**   |
| :---------------------: |
| $O(2 * N * \text{Len})$ |





# References