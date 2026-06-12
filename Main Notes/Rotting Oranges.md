24-11-2025  18:50

Status: #Revision-03

Tags: [[Tags/DSA|DSA]] [[Graphs]]

# Rotting Oranges

https://leetcode.com/problems/rotting-oranges/

- Initially push all the rotten oranges to the q with time being 0.
- Now push neighboring unvisited fresh oranges to the queue with the current time, till it's not empty.
- At last check if the no. of fresh oranges == rotten oranges, if not then return -1.
- Else return the maximum time.

```cpp
class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
		
        vector<vector<int>> visited(m, vector<int>(n));
        queue<pair<pair<int, int>, int>> q;
		
        int freshOranges = 0;
		
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                if(grid[i][j] == 2) {
                    q.push({{i, j}, 0});
                } else if(grid[i][j] == 1) {
                    freshOranges++;
                }
            }
        }
		
        int rottedOranges = 0;
        int time = 0;
		
        while(!q.empty()) {
            pair<pair<int, int>, int> orange = q.front();
            q.pop();
			
            int i = orange.first.first;
            int j = orange.first.second;
            int t = orange.second;
			
            time = max(time, t);
			
            int dy[] = {1, -1, 0, 0};
            int dx[] = {0, 0, -1, 1};
			
            for(int k = 0; k < 4; k++) {
                int nrow = i + dy[k];
                int ncol = j + dx[k];
				
                if(nrow >= 0 && nrow < m && ncol >= 0 && ncol < n && 
                visited[nrow][ncol] != 2 && grid[nrow][ncol] == 1) {
                    q.push({{nrow, ncol}, t + 1});
                    visited[nrow][ncol] = 2;
                    rottedOranges++;
                }
            }
        }
		
        if(freshOranges != rottedOranges) {
            return -1;
        }
		
        return time;
    }
};
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|    $O(2(N * M))$    |    $O(2(M * N))$     |





# References