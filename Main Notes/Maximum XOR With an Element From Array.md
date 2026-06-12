01-04-2026  17:49

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Trie]]

# Maximum XOR With an Element From Array

https://leetcode.com/problems/maximum-xor-with-an-element-from-array/

## Brute Force

- Traverse over all the queries.
- Perform xor with all the elements which are `<= m`.
- Add the maximum xor to the ans.

```cpp
class Solution {
public:
    vector<int> maximizeXor(vector<int>& nums, vector<vector<int>>& queries) {
        vector<int> ans;
		
        for(int i = 0; i < queries.size(); i++) {
            int x = queries[i][0];
            int m = queries[i][1];
            int xr = -1;
			
            for(int j = 0; j < nums.size(); j++) {
                if(nums[j] <= m) {
                    xr = max(xr, x ^ nums[j]);
                }
            }
			
            ans.push_back(xr);
        }
		
        return ans;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|     $O(Q * N)$      |        $O(Q)$        |


## Optimal

- We need to form a trie which only contains elements that are `<= m` for every query.
- So we can sort both `queries` (based on `m`) and `nums`.
- Now for every query add all the elements that are `<= m` from `nums` and find the maximum xor from the trie.
- Add the ans to the original index of the query (maintain original index before sorting).

```cpp
struct Node {
    Node* links[2];
	
    bool containsKey(int bit) {
        return links[bit] != NULL;
    }
	
    void set(int bit) {
        links[bit] = new Node();
    }
	
    Node* get(int bit) {
        return links[bit];
    }
};

class Trie {
private:
    Node* root;
	
public:
    Trie() {
        root = new Node();
    }
	
    void insert(int num) {
        Node* curr = root;
		
        for(int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if(!curr->containsKey(bit)) {
                curr->set(bit);
            }
			
            curr = curr->get(bit);
        }
    }
	
    int findMaxXOR(int num) {
        Node* curr = root;
        int xr = 0;
		
        for(int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if(curr->containsKey(1 - bit)) {
                xr = xr | (1 << i);
                curr = curr->get(1 - bit);
            } else {
                curr = curr->get(bit);
            }
        }
		
        return xr;
    }
};

class Solution {
public:
    vector<int> maximizeXor(vector<int>& nums, vector<vector<int>>& queries) {
        int q = queries.size();
        int n = nums.size();
        vector<int> ans(q);
		
        vector<pair<int, pair<int, int>>> offlineQueries;
        Trie t;
		
        for(int i = 0; i < q; i++) {
            offlineQueries.push_back({queries[i][1], {queries[i][0], i}});
        }
		
        sort(offlineQueries.begin(), offlineQueries.end());
        sort(nums.begin(), nums.end());
		
        int j = 0;
        for(int i = 0; i < q; i++) {
            int x = offlineQueries[i].second.first;
            int m = offlineQueries[i].first;
            int idx = offlineQueries[i].second.second;
			
            while(j < n && nums[j] <= m) {
                t.insert(nums[j]);
                j++;
            }
			
            if(j == 0) {
                ans[idx] = -1;
            } else {
                ans[idx] = t.findMaxXOR(x);
            }
        }
		
        return ans;
    }
};
```

|                   **Time Complexity**                   |
| :-----------------------------------------------------: |
| $O(Q) + O(Q \log Q) + O(N \log N) + O(Q * 32 + N * 32)$ |





# References