11-12-2025  18:51

Status: #Revision-03

Tags: [[Tags/DSA|DSA]] [[Graphs]]

# Word Ladder II

### Interview Approach

https://leetcode.com/problems/word-ladder-ii/

- For every level find the next sequence and store the entire path into the queue.
- After the entire level is completed, erase used elements from set. 
- Add the path to the ans if it the shortest one, else break.

```cpp
class Solution {
public:
    vector<vector<string>> findLadders(string beginWord, string endWord, vector<string>& wordList) {
        vector<vector<string>> ans;
        set<string> st(wordList.begin(), wordList.end());
        queue<vector<string>> q;
        vector<string> usedLevel;
        int level = 1;
		
        q.push({beginWord});
        usedLevel.push_back(beginWord);
		
        while(!q.empty()) {
            vector<string> sequence = q.front();
            q.pop();
			
			// Delete all the previously used elements from set
            if(sequence.size() > level) {
                for(auto it: usedLevel) {
                    st.erase(it);
                }
				
                usedLevel.clear();
                level++;
            }
			
            string el = sequence.back();
			
			// Only add shortest paths
            if(el == endWord) {
                if(!ans.size()) {
                    ans.push_back(sequence);
                } else if(sequence.size() == ans[0].size()) {
                    ans.push_back(sequence);
                } else {
                    break;
                }
				
                continue;
            }
			
			// Word length * 26 
            for(int i = 0; i < el.size(); i++) {
                char original = el[i];
				
                for(char j = 'a'; j <= 'z'; j++) {
                    el[i] = j;
					
                    if(st.find(el) != st.end()) {
                        sequence.push_back(el);
                        q.push(sequence);
                        sequence.pop_back();
						
                        usedLevel.push_back(el);
                    }
                }
				
                el[i] = original;
            }
        }
        
        return ans;
    }
};
```

|      **Time Complexity**      | **Space Complexity** |
| :---------------------------: | :------------------: |
| $x * n * 26$, n = word length |     $O(2x + 2N)$     |
x = total shortest paths



### Optimized Approach

- Hash all the words with its level in the path.
- Backtrack from the end word and construct the path.

```cpp
class Solution {
public:
    void construct(string word, vector<string>& path, map<string, int>& mp, vector<vector<string>>& ans, string beginWord) {
        if(word == beginWord) {
            reverse(path.begin(), path.end());
            ans.push_back(path);
            reverse(path.begin(), path.end());
            return;
        }
		
        int level = mp[word];
		
        for(int i = 0; i < word.size(); i++) {
            char original = word[i];
			
            for(char j = 'a'; j <= 'z'; j++) {
                word[i] = j;
				
				// Find the previous level word
                if(mp.find(word) != mp.end() && mp[word] < level) {
                    path.push_back(word);
                    construct(word, path, mp, ans, beginWord);
                    path.pop_back();
                }
            }
			
            word[i] = original;
        }
    }
	
    vector<vector<string>> findLadders(string beginWord, string endWord, vector<string>& wordList) {
        set<string> st(wordList.begin(), wordList.end());
        map<string, int> mp;
        queue<string> q;
		
        q.push(beginWord);
        st.erase(beginWord);
        mp[beginWord] = 1;
		
        while(!q.empty()) {
            string word = q.front();
            q.pop();
			
            int level = mp[word];
			
            if(word == endWord) {
                break;
            }
			
            for(int i = 0; i < word.size(); i++) {
                char original = word[i];
				
                for(char j = 'a'; j <= 'z'; j++) {
                    word[i] = j;
					
                    if(st.find(word) != st.end()) {
                        q.push(word);
                        mp[word] = level + 1;
                        st.erase(word);
                    }
                }
				
                word[i] = original;
            }
        }
		
        vector<vector<string>> ans;
		
        if(mp.find(endWord) != mp.end()) {
            vector<string> path({endWord});
            construct(endWord, path, mp, ans, beginWord);
        }
		
        return ans;
    }
};
```





# References