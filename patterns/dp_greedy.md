# 🎒 Dynamic Programming & 🤑 Greedy

Optimization problems.

## 🎒 Dynamic Programming
**Concept**: Break down into subproblems.
**Trick**: Always start with **Recursion + Memoization**. Then convert to Iterative if needed.

### 1. Climbing Stairs (1D DP)
**Pattern**: Ways to reach N.
**Trick**: `dp[i] = dp[i-1] + dp[i-2]`. Same as Fibonacci.

```python
def climbStairs(n: int) -> int:
    one, two = 1, 1
    for i in range(n - 1):
        temp = one
        one = one + two
        two = temp
    return one
```

### 2. House Robber
**Pattern**: Max sum non-adjacent.
**Trick**: `rob = max(arr[0] + rob[2:], rob[1:])`.
Iterative: `newRob = max(n + rob1, rob2)`.

```python
def rob(nums: List[int]) -> int:
    rob1, rob2 = 0, 0
    
    for n in nums:
        temp = max(n + rob1, rob2)
        rob1 = rob2
        rob2 = temp
    return rob2
```

### 3. Longest Common Subsequence (2D DP)
**Pattern**: Match two strings.
**Trick**: 2D Grid.
- If `s1[i] == s2[j]`: `1 + diag`
- Else: `max(right, down)`

```python
def longestCommonSubsequence(text1: str, text2: str) -> int:
    dp = [[0 for j in range(len(text2) + 1)] for i in range(len(text1) + 1)]
    
    for i in range(len(text1) - 1, -1, -1):
        for j in range(len(text2) - 1, -1, -1):
            if text1[i] == text2[j]:
                dp[i][j] = 1 + dp[i + 1][j + 1]
            else:
                dp[i][j] = max(dp[i][j + 1], dp[i + 1][j])
    return dp[0][0]
```

### 4. Longest Increasing Subsequence
**Pattern**: Subsequence order matters.
**Trick**: `dp[i] = 1 + max(dp[j])` for all `j > i` where `nums[i] < nums[j]`.

```python
def lengthOfLIS(nums: List[int]) -> int:
    LIS = [1] * len(nums)
    
    for i in range(len(nums) - 1, -1, -1):
        for j in range(i + 1, len(nums)):
            if nums[i] < nums[j]:
                LIS[i] = max(LIS[i], 1 + LIS[j])
    return max(LIS)
```

---

## 🤑 Greedy
**Concept**: Local optimal choice leads to global optimum.

### 1. Maximum Subarray (Kadane's Algorithm)
**Pattern**: Max contiguous sum.
**Trick**: If current prefix sum is negative, reset it to 0.

```python
def maxSubArray(nums: List[int]) -> int:
    maxSub = nums[0]
    curSum = 0
    
    for n in nums:
        if curSum < 0:
            curSum = 0
        curSum += n
        maxSub = max(maxSub, curSum)
    return maxSub
```

### 2. Jump Game
**Pattern**: Can reach end?
**Trick**: Work backwards. Shift `goal` to `i` if `i + nums[i] >= goal`. If `goal == 0` at end, true.

```python
def canJump(nums: List[int]) -> bool:
    goal = len(nums) - 1
    
    for i in range(len(nums) - 1, -1, -1):
        if i + nums[i] >= goal:
            goal = i
            
    return True if goal == 0 else False
```
