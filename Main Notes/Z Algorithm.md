11-04-2026  15:49

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Strings]]

# Z Algorithm

https://www.naukri.com/code360/problems/z-algorithm_1112619

- Z Algorithm is a pattern matching algorithm, that finds the length the longest substring starting from `i`, that matches the prefix of that string. 
- The algorithm stores the lengths in the `z` array.
- For every `i` in the string,
	- If that `i` is inside calculated subarray range (`l -> r`), update `z[i]` to previously computed length(limit the `z[i]` to range boundary).
	- Now increase the length count, while matching the string.
	- Update the boundary variables, if the new boundary is larger.
- For finding total no. of patterns in the string, we can add the pattern as prefix in the string and add a trash char like (`$`) after it.
- An `i` with Z score of `pattern.size()` is a pattern inside the string.

```cpp
vector<int> zMatch(string s) {
    int n = s.size();
	
    vector<int> z(n);
    int l = 0;
    int r =  0;
	
	// O(2N)
    for(int i = 1; i < n; i++) {
        // Inside the calculated boundary
        if(i < r) {
        	z[i] = min(z[i - l], r - i);
        }
		
        // Exapand until matching
        while(i + z[i] < n && s[z[i]] == s[i + z[i]]) {
            z[i]++;
        }
		
        // Update the boundary
        if(i + z[i] > r) {
            l = i;
            r = i + z[i];
        }
    }
	
    return z;
}

int zAlgorithm(string s, string p, int n, int m)
{
	string str = p + "$" + s;
    vector<int> z = zMatch(str);
	
    int total = 0;
    for(int i = 0; i < z.size(); i++) {
        if(z[i] == p.size()) {
            total++;
        }
    }
	
    return total;
}
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|       $O(N)$        |        $O(N)$        |





# References