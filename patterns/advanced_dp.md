# 🎒 Advanced Dynamic Programming

Complex optimization problems requiring state compression or multi-dimensional DP.

## 🎒 Knapsack Patterns

### 1. 0/1 Knapsack
**Pattern**: Items with weight/value. Max value with capacity W. Each item once.
**Trick**: `dp[i][w] = max(dp[i-1][w], val + dp[i-1][w - weight])`.
**Space Opt**: Iterate backwards.

```python
# Weights, Values, Capacity
def knapsack(W, wt, val, n):
    dp = [0 for i in range(W + 1)] 
  
    for i in range(1, n + 1): 
        for w in range(W, 0, -1): 
            if wt[i-1] <= w: 
                dp[w] = max(dp[w], dp[w-wt[i-1]] + val[i-1]) 
    return dp[W] 
```

### 2. Unbounded Knapsack (Coin Change II)
**Pattern**: Infinite supply of items.
**Trick**: Iterate forwards. `dp[a] += dp[a - coin]`.

```python
def change(amount: int, coins: List[int]) -> int:
    dp = [0] * (amount + 1)
    dp[0] = 1
    for c in coins:
        for a in range(c, amount + 1):
            dp[a] += dp[a - c]
    return dp[amount]
```

---

## 🎭 String DP

### 1. Edit Distance (Levenshtein)
**Pattern**: Min ops to convert word1 to word2.
**Trick**:
- If match: `dp[i][j] = dp[i-1][j-1]`
- If mismatch: `1 + min(insert, delete, replace)`
  - Insert: `dp[i][j-1]`
  - Delete: `dp[i-1][j]`
  - Replace: `dp[i-1][j-1]`

```python
def minDistance(word1: str, word2: str) -> int:
    dp = [[float("inf")] * (len(word2) + 1) for i in range(len(word1) + 1)]
    
    for i in range(len(word1) + 1): dp[i][len(word2)] = len(word1) - i
    for j in range(len(word2) + 1): dp[len(word1)][j] = len(word2) - j
    
    for i in range(len(word1) - 1, -1, -1):
        for j in range(len(word2) - 1, -1, -1):
            if word1[i] == word2[j]:
                dp[i][j] = dp[i + 1][j + 1]
            else:
                dp[i][j] = 1 + min(dp[i + 1][j], dp[i][j + 1], dp[i + 1][j + 1])
    return dp[0][0]
```

### 2. Longest Palindromic Substring
**Pattern**: Expand around center.
**Trick**: Handle odd (`bab`) and even (`baab`) centers.

```python
def longestPalindrome(s: str) -> str:
    res = ""
    for i in range(len(s)):
        # odd length
        l, r = i, i
        while l >= 0 and r < len(s) and s[l] == s[r]:
            if (r - l + 1) > len(res): res = s[l:r+1]
            l -= 1; r += 1
        
        # even length
        l, r = i, i + 1
        while l >= 0 and r < len(s) and s[l] == s[r]:
            if (r - l + 1) > len(res): res = s[l:r+1]
            l -= 1; r += 1
    return res
```

---

## 🧩 Other Advanced DP

### 1. Burst Balloons (Matrix Chain Multiplication)
**Pattern**: Optimal order of operations.
**Trick**: `dp[l][r]` = max coins from bursting range `l` to `r`. Iterate `k` between `l` and `r` as the *last* balloon to burst.

```python
def maxCoins(nums: List[int]) -> int:
    nums = [1] + nums + [1]
    dp = {}
    
    def dfs(l, r):
        if l > r: return 0
        if (l, r) in dp: return dp[(l, r)]
        
        dp[(l, r)] = 0
        for i in range(l, r + 1):
            coins = nums[l-1] * nums[i] * nums[r+1]
            coins += dfs(l, i - 1) + dfs(i + 1, r)
            dp[(l, r)] = max(dp[(l, r)], coins)
        return dp[(l, r)]
        
    return dfs(1, len(nums) - 2)
```

### 2. Bitmask DP (Small N <= 20)
**Pattern**: Assign N workers to N jobs.
**Trick**: Use integer bits to represent set of available/used items.
`if (mask >> i) & 1`: check if i-th bit is set.
`mask | (1 << i)`: set i-th bit.

```python
# Example: Partition to K Equal Sum Subsets (can be solved with backtracking too)
# DP state: dp[mask] = remainder
```
