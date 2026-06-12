11-11-2025  15:27

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Strings]]

# Rotate String

https://leetcode.com/problems/rotate-string/

### Brute Force

- Rotate string at different index i.
- If any rotation is can create goal, return true.

```cpp
class Solution {
public:
    bool rotateString(string s, string goal) {
        if(s.size() != goal.size()) {
            return false;
        }
		
        for(int i = 0; i < s.size(); i++) {
            string rotated = s.substr(i) + s.substr(0, i);
			
			// O(N)
            if(rotated == goal) {
                return true;
            }
        }
		
        return false;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|      $O(N^2)$       |        $O(N)$        |


### Optimal

- Double the original string.
- Find the goal inside the doubled string.

```cpp
class Solution {
public:
    bool rotateString(string s, string goal) {
        if(s.size() != goal.size()) {
            return false;
        }
		
        string doubled = s + s;
		
        return doubled.find(goal) != string::npos;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|       $O(N)$        |        $O(N)$        |





# References