# ⚠️ Common Pitfalls & Edge Cases

A checklist of things to verify **before** you say "I'm done".

## 🛑 Universal Edge Cases
Always test your code mentally against these:
1.  **Empty Input**: `nums = []`, `s = ""`, `root = None`.
2.  **Single Element**: `nums = [1]`, `s = "a"`.
3.  **Two Elements**: `nums = [1, 2]`.
4.  **Duplicates**: `nums = [1, 1, 1]`. (Breaks binary search/sorting logic often).
5.  **Negative Numbers**: `nums = [-1, -2]`. (Breaks some sliding window/prefix sum logic).
6.  **Integer Overflow**: In Python, integers are arbitrary precision, but in Java/C++, `2^31 - 1` is the limit.
    - *Trick*: Use `l + (r - l) // 2` instead of `(l + r) // 2`.

---

## 🏗️ Data Structure Specifics

### Arrays & Strings
- **Index Out of Bounds**: Checking `i < len(nums)` *before* accessing `nums[i]`.
- **Off-by-One**: Loop ranges `range(n)` vs `range(n-1)`.
- **Sliding Window**:
    - Window size 0.
    - Window size > Array length.
    - Shrinking logic: `while invalid(): l += 1`. Ensure `l` doesn't cross `r`.

### Linked Lists
- **Null Head**: Always check `if not head: return`.
- **Single Node**: Deleting the only node? Reversing a single node?
- **Cycles**: Infinite loops if you don't detect cycles.
- **Lost Head**: Keep a `dummy` node pointing to `head` to return `dummy.next`.

### Trees
- **Null Root**: Base case `if not root: return`.
- **Leaf Nodes**: Processing logic for nodes with no children.
- **Skewed Tree**: Tree is a straight line (Linked List). Recursion depth $O(N)$ might stack overflow.
- **Unbalanced**: BST operations become $O(N)$ instead of $O(\log N)$.

### Graphs
- **Disconnected Graph**: Start BFS/DFS from *every* unvisited node, not just node 0.
- **Cycles**: Use `visited` set to avoid infinite loops.
- **Self-Loops**: Edge `(u, u)`.
- **Parallel Edges**: Multiple edges between `u` and `v`.

---

## 🧮 Algorithm Specifics

### Binary Search
- **Infinite Loop**: `mid = (l + r) // 2`. If `l=0, r=1`, `mid=0`. If you set `l = mid`, loop continues forever.
    - *Fix*: `l = mid + 1` or use `mid = (l + r + 1) // 2`.
- **Termination**: `while l < r` vs `while l <= r`.
    - `l <= r`: Search space is empty when `l > r`.
    - `l < r`: Search space is empty when `l == r`.

### Dynamic Programming
- **Base Cases**: `dp[0]` initialization.
- **Bounds**: `dp[i-1]` or `dp[i-2]` when `i < 2`.
- **State Space**: Is the array large enough? `dp` size `N+1` vs `N`.

### Backtracking
- **Deep Copy**: `res.append(path[:])` or `path.copy()`. If you append `path`, it will be empty at the end because you backtrack.
- **Base Case Return**: Forget to `return` after finding a solution? You might continue exploring invalid states.

---

## 📦 Amazon & System Design Specifics
- **Scalability**: What if input is 10TB? (Can't fit in memory -> External Sort / MapReduce).
- **Concurrency**: Thread safety? (Locks, Atomic operations).
- **Real-world IDs**: IDs are not always `0..N-1`. Use HashMaps, not Arrays.

---

## 🚨 Problem-Specific Watchlist

| Problem | The Trap / Edge Case | The Fix |
|---------|----------------------|---------|
| **Two Sum** | Array is not sorted. | Use HashMap, not Two Pointers. |
| **3Sum** | Duplicate triplets in result. | Skip duplicates: `while l < r and nums[l] == nums[l-1]: l += 1`. |
| **Container With Most Water** | Moving the taller line. | Always move the **shorter** line to try and find a taller one. |
| **Merge Intervals** | Touching intervals `[1,2], [2,3]`. | Condition is `start <= last_end`, not `<`. |
| **Insert Interval** | New interval is before/after all. | Handle edge cases where `newInterval` is added at start or end. |
| **Reverse Linked List** | Cycle created. | Set `head.next = None` after recursion or loop. |
| **Number of Islands** | Infinite recursion. | Mark node as visited **before** DFS calls. |
| **Word Search** | Reusing cell in same path. | Mark board cell `#` before DFS, revert after (Backtrack). |
| **Coin Change** | DP initialization. | Init with `amount + 1` (infinity), not `-1` or `0`. |
| **Longest Increasing Subsequence** | Result is not at `dp[n-1]`. | Result is `max(dp)`, because LIS can end anywhere. |
| **Climbing Stairs** | `n=1`. | Handle base case `if n <= 2: return n`. |
| **Product Except Self** | Division by zero. | Don't use division. Use Prefix/Postfix arrays. |
| **Search in Rotated Array** | Duplicates `[1,0,1,1,1]`. | Worst case $O(N)$ if duplicates exist. |
| **Valid Parentheses** | Stack not empty at end. | Return `len(stack) == 0`, not just `True`. |
| **Kth Largest Element** | Heap size. | Min Heap of size K. Root is Kth largest. (Don't use Max Heap unless popping N-K times). |
| **Top K Frequent** | Bucket Sort index. | Frequencies can be `0..N`. Bucket array size `N+1`. |
| **Median of Data Stream** | Heap balancing. | `len(small) == len(large)` or `len(small) == len(large) + 1`. |
| **Course Schedule** | Disconnected graph. | Run DFS on **all** nodes `0..N-1`, not just node 0. |
| **Clone Graph** | Infinite loop. | Use HashMap `old -> new` to return existing clone if visited. |
| **Subarray Sum Equals K** | `k=0` or negative numbers. | Sliding window doesn't work. Must use Prefix Sum + HashMap. |
