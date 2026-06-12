30-03-2026  16:33

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Trie]]

# Implement Trie II

https://www.naukri.com/code360/problems/implement-trie_1387095

- Maintain a count for words and prefix count for counting the words that has similar prefix.
- When adding the word to the trie, increase prefix count of all the nodes that come in the way. At the end node increase the count.
- When erasing the word, decrease prefix count of all the nodes that come in the way. At the end node decrease the count.

```cpp
#include <bits/stdc++.h> 

struct Node {
private:
    Node* links[26];
    int count = 0;
    int prefixCount = 0;
	
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
        count++;
    }
	
    void erase() {
        count--;
    }
	
    int getCount() {
        return count;
    }
	
    void increasePrefix() {
        prefixCount++;
    }
	
    void decreasePrefix() {
        prefixCount--;
    }
	
    int getPrefix() {
        return prefixCount;
    }
};

class Trie{
private:
    Node* root;
	
public:
    Trie(){
        root = new Node();
    }
	
    void insert(string &word){
        Node* curr = root;
		
        for(int i = 0; i < word.size(); i++) {
            if(!curr->containsKey(word[i])) {
                curr->set(word[i]);
            }
			
            curr = curr->get(word[i]);
            curr->increasePrefix();
        }
		
        curr->mark();
    }
	
    int countWordsEqualTo(string &word){
        Node* curr = root;
		
        for(int i = 0; i < word.size(); i++) {
            if(!curr->containsKey(word[i])) {
                return 0;
            }
			
            curr = curr->get(word[i]);
        }
		
        return curr->getCount();
    }
	
    int countWordsStartingWith(string &word){
        Node* curr = root;
		
        for(int i = 0; i < word.size(); i++) {
            if(!curr->containsKey(word[i])) {
                return 0;
            }
			
            curr = curr->get(word[i]);
        }
		
        return curr->getPrefix();
    }
	
    void erase(string &word){
        Node* curr = root;
		
        for(int i = 0; i < word.size(); i++) {
            if(!curr->containsKey(word[i])) {
                return;
            }
			
            curr = curr->get(word[i]);
            curr->decreasePrefix();
        }
		
        curr->erase();
    }
};
```

Time Complexity: $O(N)$





# References