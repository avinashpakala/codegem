# 🔙 Backtracking

Systematic way to iterate through all possible configurations of a search space.

## 💡 Key Concepts
- **Choice**: What decision do I make at this step?
- **Constraints**: Is this choice valid?
- **Goal**: Have I reached a solution?
- **Pruning**: Stop early if current path cannot lead to solution.

### 1. Subsets (Power Set)
**Pattern**: Find all distinct subsets.
**Trick**: Include `nums[i]` or don't include `nums[i]`.
**Time**: $O(2^N)$

```python
def subsets(nums: List[int]) -> List[List[int]]:
    res = []
    subset = []
    
    def dfs(i):
        if i >= len(nums):
            res.append(subset.copy())
            return
        
        # decision to include nums[i]
        subset.append(nums[i])
        dfs(i + 1)
        
        # decision NOT to include nums[i]
        subset.pop()
        dfs(i + 1)
        
    dfs(0)
    return res
```

### 2. Permutations
**Pattern**: All orderings.
**Trick**: Iterate through all numbers. If not in current path, add it.
**Time**: $O(N!)$

```python
def permute(nums: List[int]) -> List[List[int]]:
    res = []
    
    # Or use library: return list(itertools.permutations(nums))
    
    def backtrack(curr):
        if len(curr) == len(nums):
            res.append(curr[:])
            return
        
        for n in nums:
            if n not in curr:
                curr.append(n)
                backtrack(curr)
                curr.pop()
                
    backtrack([])
    return res
```

### 3. Combination Sum
**Pattern**: Choose numbers to sum to target (reuse allowed).
**Trick**: Two choices: Include `nums[i]` and stay at `i` (to reuse), OR skip `nums[i]` and move to `i+1`.

```python
def combinationSum(candidates: List[int], target: int) -> List[List[int]]:
    res = []
    
    def dfs(i, cur, total):
        if total == target:
            res.append(cur.copy())
            return
        if i >= len(candidates) or total > target:
            return
        
        # Include candidates[i]
        cur.append(candidates[i])
        dfs(i, cur, total + candidates[i])
        
        # Skip candidates[i]
        cur.pop()
        dfs(i + 1, cur, total)
        
    dfs(0, [], 0)
    return res
```

### 4. Word Search
**Pattern**: Find word in grid.
**Trick**: DFS on grid. Mark visited by changing board char to `#`, then revert (backtrack).

```python
def exist(board: List[List[str]], word: str) -> bool:
    ROWS, COLS = len(board), len(board[0])
    path = set()
    
    def dfs(r, c, i):
        if i == len(word): return True
        if (r < 0 or c < 0 or r >= ROWS or c >= COLS or 
            word[i] != board[r][c] or (r, c) in path):
            return False
        
        path.add((r, c))
        res = (dfs(r + 1, c, i + 1) or
               dfs(r - 1, c, i + 1) or
               dfs(r, c + 1, i + 1) or
               dfs(r, c - 1, i + 1))
        path.remove((r, c)) # Backtrack
        return res
    
    for r in range(ROWS):
        for c in range(COLS):
            if dfs(r, c, 0): return True
    return False
```

### 5. N-Queens
**Pattern**: Place N queens so none attack each other.
**Trick**: Track `cols`, `posDiag` (r+c), `negDiag` (r-c) sets.

```python
def solveNQueens(n: int) -> List[List[str]]:
    col = set()
    posDiag = set() # (r + c)
    negDiag = set() # (r - c)
    res = []
    board = [["."] * n for i in range(n)]
    
    def backtrack(r):
        if r == n:
            copy = ["".join(row) for row in board]
            res.append(copy)
            return
            
        for c in range(n):
            if c in col or (r + c) in posDiag or (r - c) in negDiag:
                continue
                
            col.add(c)
            posDiag.add(r + c)
            negDiag.add(r - c)
            board[r][c] = "Q"
            
            backtrack(r + 1)
            
            col.remove(c)
            posDiag.remove(r + c)
            negDiag.remove(r - c)
            board[r][c] = "."
            
    backtrack(0)
    return res
```
