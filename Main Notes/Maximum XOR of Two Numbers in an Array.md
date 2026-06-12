01-04-2026  16:32

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Trie]]

# Maximum XOR of Two Numbers in an Array

https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/

- Create a trie from binary representation  of every number from `nums`.
- Define a method that finds the maximum xor for given `x` in trie.
	- To do this iterate over all the bits of the given `x` and,
	- Find the opposite bit in the trie and activate the bit in the xor answer.
	- If you can't find the opposite bit go to the same bit.
- For every number in `nums` find the maximum xor and return the maximal one.

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
	
    int getMaxXOR(int num) {
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
    int findMaximumXOR(vector<int>& nums) {
        Trie t;
		
        for(int i = 0; i < nums.size(); i++) {
            t.insert(nums[i]);
        }
		
        int maxi = 0;
        for(int i = 1; i < nums.size(); i++) {
            maxi = max(maxi, t.getMaxXOR(nums[i]));
        }
		
        return maxi;
    }
};
```

| **Time Complexity**  |
| :------------------: |
| $O(N * 32 + N * 32)$ |





# References