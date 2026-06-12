26-03-2026  15:31

Status: #Revision-02

Tags: [[Tags/DSA|DSA]] [[Dynamic Programming]]

# Parsing A Boolean Expression

https://leetcode.com/problems/parsing-a-boolean-expression/
https://www.naukri.com/code360/problems/problem-name-boolean-evaluation_1214650

## Memoization

- Partition the expression at operators.
- For both partition we need to calculate the total expression which results in both true and false.
- If the operator is `&`,
	- The total true result will be `LT * RT`
	- The total False result will be `LT * RF + LF * RT + LF * LF`
- Else, if the operator is `|`,
	- The total true result will be `LT * RF + LF * RT + LT * LT`
	- The total False result will be `LF * RF`
- Else, if the operator is `^`,
	- The total true result will be `LT * RF + LF * RT`
	- The total False result will be `LT * RT + LF * RF`
- Return the total across all possible partitions.

```cpp
int mod = 1000000007;

long evaluate(int i, int j, int isTrue, string& exp, vector<vector<vector<int>>>& dp) {
    if(i > j) {
        return 0;
    } else if(i == j) {
        if(isTrue) {
            return exp[i] == 'T';
        }
        return exp[i] == 'F';
    }
	
    if(dp[i][j][isTrue] != -1) {
        return dp[i][j][isTrue];
    }
	
    long total = 0;
    for(int k = i + 1; k <= j - 1; k += 2) {
        long lT = evaluate(i, k - 1, 1, exp, dp);
        long lF = evaluate(i, k - 1, 0, exp, dp);
		
        long rT = evaluate(k + 1, j, 1, exp, dp);
        long rF = evaluate(k + 1, j, 0, exp, dp);
		
        char ch = exp[k];
		
        if(ch == '&') {
            if(isTrue) {
                total = total + (lT * rT) % mod;
            } else {
                total = total + ((lT * rF) + (lF * rT) + (lF * rF)) % mod;
            }
        } else if(ch == '|') {
            if(isTrue) {
                total = total + ((lT * rT) + (lT * rF) + (lF * rT)) % mod;
            } else {
                total = total + (lF * rF) % mod;
            }
        } else if(ch == '^') {
            if(isTrue) {
                total = total + ((lT * rF) + (lF * rT)) % mod;
            } else {
                total = total + ((lF * rF) + (lT * rT)) % mod;
            }
        }
    }
	
    return dp[i][j][isTrue] = total % mod;
}

int evaluateExp(string & exp) {
    int n = exp.size();
    vector<vector<vector<int>>> dp(n, vector<vector<int>>(n, vector<int>(2, -1)));
	
    return evaluate(0, n - 1, 1, exp, dp);
}
```

| **Time Complexity** |      **Space Complexity**       |
| :-----------------: | :-----------------------------: |
|    $O(N*N*2*N)$     | $O(N*N*2 + \text{Stack Space})$ |


## Tabulation

```cpp
int mod = 1000000007;

int evaluateExp(string & exp) {
    int n = exp.size();
    vector<vector<vector<int>>> dp(n, vector<vector<int>>(n, vector<int>(2)));
	
    for(int i = 0; i < n; i++) {
        dp[i][i][0] = exp[i] == 'F';
        dp[i][i][1] = exp[i] == 'T';
    }
	
    for(int i = n - 1; i >= 0; i--) {
        for(int j = i + 1; j < n; j++) {
            for(int isTrue = 0; isTrue <= 1; isTrue++) {
                long total = 0;
				
                for(int k = i + 1; k <= j - 1; k += 2) {
                    long lT = dp[i][k - 1][1];
                    long lF = dp[i][k - 1][0];
					
                    long rT = dp[k + 1][j][1];
                    long rF = dp[k + 1][j][0];
					
                    char ch = exp[k];
					
                    if(ch == '&') {
                        if(isTrue) {
                            total = total + (lT * rT) % mod;
                        } else {
                            total = total + ((lT * rF) + (lF * rT) + (lF * rF)) % mod;
                        }
                    } else if(ch == '|') {
                        if(isTrue) {
                            total = total + ((lT * rT) + (lT * rF) + (lF * rT)) % mod;
                        } else {
                            total = total + (lF * rF) % mod;
                        }
                    } else if(ch == '^') {
                        if(isTrue) {
                            total = total + ((lT * rF) + (lF * rT)) % mod;
                        } else {
                            total = total + ((lF * rF) + (lT * rT)) % mod;
                        }
                    }
                }
				
                dp[i][j][isTrue] = total % mod;
            }
        }
    }
	
    return dp[0][n - 1][1];
}
```

| **Time Complexity** | **Space Complexity** |
| :-----------------: | :------------------: |
|    $O(N*N*2*N)$     |      $O(N*N*2)$      |





# References