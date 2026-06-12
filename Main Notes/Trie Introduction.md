30-03-2026  14:49

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Trie]]

# Trie Introduction

https://leetcode.com/problems/implement-trie-prefix-tree/

- Create 26 sized array for every character.
- When entering word into the trie, traverse over all the chars and create new links if not exists while traversing.
- For searching the word, traverse over all the chars and if a link is not found return false. At the last link return true if that link is marked.
- For prefix search, traverse over all the chars and if a link is not found return false.

```cpp
struct Node {
private:
    Node* links[26];
    bool flag = false;
	
public:
    bool containsKey(char ch) { 
        return links[ch - 'a'] != NULL; 
    }
	
    void set(char ch) { 
        links[ch - 'a'] = new Node(); 
    }
	
    Node* get(char ch) { 
        return links[ch - 'a']; 
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
    Trie() { root = new Node(); }
	
    // O(N)
    void insert(string word) {
        Node* curr = root;
		
        for (int i = 0; i < word.size(); i++) {
            if (!curr->containsKey(word[i])) {
                curr->set(word[i]);
            }
			
            curr = curr->get(word[i]);
        }
		
        curr->mark();
    }
	
    // O(N)
    bool search(string word) {
        Node* curr = root;
		
        for (int i = 0; i < word.size(); i++) {
            if (!curr->containsKey(word[i])) {
                return false;
            }
			
            curr = curr->get(word[i]);
        }
		
        return curr->isWord();
    }
	
    // O(N)
    bool startsWith(string prefix) {
        Node* curr = root;
		
        for (int i = 0; i < prefix.size(); i++) {
            if (!curr->containsKey(prefix[i])) {
                return false;
            }
			
            curr = curr->get(prefix[i]);
        }
		
        return true;
    }
};
```





# References