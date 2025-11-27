# 🔎 Google Specific Patterns

Google interviews often involve harder algorithmic challenges, string processing, and geometry.

## 🧵 Advanced String Algorithms

### 1. KMP Algorithm (Knuth-Morris-Pratt)
**Pattern**: Find substring `needle` in `haystack` in $O(N)$.
**Trick**: Build `LPS` (Longest Prefix Suffix) array. If mismatch, jump back using LPS to avoid re-scanning.

```python
def strStr(haystack: str, needle: str) -> int:
    if needle == "": return 0
    lps = [0] * len(needle)
    
    # Build LPS
    prevLPS, i = 0, 1
    while i < len(needle):
        if needle[i] == needle[prevLPS]:
            lps[i] = prevLPS + 1
            prevLPS += 1
            i += 1
        elif prevLPS == 0:
            lps[i] = 0
            i += 1
        else:
            prevLPS = lps[prevLPS - 1]
            
    # Search
    i = j = 0 # i -> haystack, j -> needle
    while i < len(haystack):
        if haystack[i] == needle[j]:
            i += 1
            j += 1
        else:
            if j == 0: i += 1
            else: j = lps[j - 1]
        if j == len(needle): return i - len(needle)
    return -1
```

### 2. Rolling Hash (Rabin-Karp)
**Pattern**: Find multiple patterns or repeated DNA sequences.
**Trick**: Hash window. Update hash in $O(1)$ by removing leading char and adding trailing char.
`Hash = (Hash - char_out * 26^(L-1)) * 26 + char_in`

---

## 📐 Geometry

### 1. Convex Hull (Monotone Chain)
**Pattern**: Find boundary of points (Erect the Fence).
**Trick**: Sort points. Build "Lower Hull" and "Upper Hull" using Cross Product.
**Cross Product**: `(b.x - a.x)*(c.y - a.y) - (b.y - a.y)*(c.x - a.x)`. Positive = Left turn, Negative = Right turn.

```python
def outerTrees(trees: List[List[int]]) -> List[List[int]]:
    def cross_product(p1, p2, p3):
        return (p2[0] - p1[0]) * (p3[1] - p1[1]) - (p2[1] - p1[1]) * (p3[0] - p1[0])

    trees.sort(key=lambda x: (x[0], x[1]))
    
    # Build lower hull
    lower = []
    for p in trees:
        while len(lower) >= 2 and cross_product(lower[-2], lower[-1], p) < 0:
            lower.pop()
        lower.append(tuple(p))
        
    # Build upper hull
    upper = []
    for p in reversed(trees):
        while len(upper) >= 2 and cross_product(upper[-2], upper[-1], p) < 0:
            upper.pop()
        upper.append(tuple(p))
        
    return list(set(lower + upper))
```

---

## 🏛️ Google Classics (Hard)

### 1. Word Search II
**Pattern**: Find list of words in grid.
**Trick**: **Trie + Backtracking**. Store words in Trie. DFS on grid. If node has `word`, add to result. Prune Trie branches after finding words to optimize.

### 2. Count Smaller Numbers After Self
**Pattern**: For each `nums[i]`, count smaller numbers to right.
**Trick**: **Merge Sort** (count jumps during merge) or **Fenwick Tree** (add ranks).

```python
# Merge Sort approach logic:
# When merging right_part into left_part, if right_part[j] < left_part[i],
# it means right_part[j] jumps over left_part[i].
# Since right_part is sorted, ALL remaining elements in right_part are also smaller? 
# Actually, standard inversion count logic.
```

### 3. Split Array Largest Sum
**Pattern**: Split array into `m` subarrays to minimize largest sum.
**Trick**: **Binary Search on Answer**. Range `[max(nums), sum(nums)]`.
Check if `mid` is possible by greedily splitting.

```python
def splitArray(nums: List[int], m: int) -> int:
    def canSplit(largest):
        subarray = 0
        curSum = 0
        for n in nums:
            curSum += n
            if curSum > largest:
                subarray += 1
                curSum = n
        return subarray + 1 <= m

    l, r = max(nums), sum(nums)
    res = r
    while l <= r:
        mid = l + ((r - l) // 2)
        if canSplit(mid):
            res = mid
            r = mid - 1
        else:
            l = mid + 1
    return res
```

### 4. Robot Room Cleaner
**Pattern**: Clean unknown grid with API `move()`, `turnLeft()`.
**Trick**: **Backtracking with Relative Coordinates**.
Map `(row, col)` relative to start `(0,0)`.
Always turn right 4 times to explore all directions.
Go back: `turnRight, turnRight, move, turnRight, turnRight`.

```python
def cleanRoom(self, robot):
    visited = set()
    directions = [(-1, 0), (0, 1), (1, 0), (0, -1)] # Up, Right, Down, Left
    
    def go_back():
        robot.turnRight()
        robot.turnRight()
        robot.move()
        robot.turnRight()
        robot.turnRight()
        
    def backtrack(r, c, d):
        visited.add((r, c))
        robot.clean()
        
        for i in range(4):
            new_d = (d + i) % 4
            new_r = r + directions[new_d][0]
            new_c = c + directions[new_d][1]
            
            if (new_r, new_c) not in visited and robot.move():
                backtrack(new_r, new_c, new_d)
                go_back()
                
            robot.turnRight()
            
    backtrack(0, 0, 0)
```
