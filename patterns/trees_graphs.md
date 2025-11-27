# 🌳 Trees & 🕸️ Graphs

Hierarchical and connected data structures.

## 🌳 Trees

### 1. Invert Binary Tree
**Pattern**: Visit every node and swap children.
**Trick**: Recursive DFS. `temp = left; left = right; right = temp`.

```python
def invertTree(root: Optional[TreeNode]) -> Optional[TreeNode]:
    if not root: return None
    root.left, root.right = root.right, root.left
    invertTree(root.left)
    invertTree(root.right)
    return root
```

### 2. Maximum Depth of Binary Tree
**Pattern**: Height of tree.
**Trick**: `1 + max(dfs(left), dfs(right))`.

### 3. Level Order Traversal (BFS)
**Pattern**: Process level by level.
**Trick**: Use a `deque`. Loop `range(len(q))` to process one full level at a time.

```python
def levelOrder(root: Optional[TreeNode]) -> List[List[int]]:
    res = []
    q = collections.deque()
    if root: q.append(root)
    
    while q:
        val = []
        for i in range(len(q)):
            node = q.popleft()
            val.append(node.val)
            if node.left: q.append(node.left)
            if node.right: q.append(node.right)
        res.append(val)
    return res
```

### 4. Lowest Common Ancestor (BST)
**Pattern**: Find split point.
**Trick**: In BST, if both `p` and `q` < `root`, go left. If both > `root`, go right. Else, `root` is the split point (LCA).

```python
def lowestCommonAncestor(root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
    cur = root
    while cur:
        if p.val > cur.val and q.val > cur.val:
            cur = cur.right
        elif p.val < cur.val and q.val < cur.val:
            cur = cur.left
        else:
            return cur
```

### 5. Trie (Prefix Tree)
**Pattern**: Prefix search.
**Trick**: Tree where each node is a character. Mark end of word.

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.endOfWord = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        cur = self.root
        for c in word:
            if c not in cur.children:
                cur.children[c] = TrieNode()
            cur = cur.children[c]
        cur.endOfWord = True
```

---

## 🕸️ Graphs

### 1. Number of Islands
**Pattern**: Connected components in grid.
**Trick**: Iterate grid. If '1', increment count and run BFS/DFS to mark all connected '1's as '0' (visited).

```python
def numIslands(grid: List[List[str]]) -> int:
    if not grid: return 0
    rows, cols = len(grid), len(grid[0])
    visit = set()
    islands = 0
    
    def bfs(r, c):
        q = collections.deque()
        visit.add((r, c))
        q.append((r, c))
        while q:
            row, col = q.popleft()
            directions = [[1, 0], [-1, 0], [0, 1], [0, -1]]
            for dr, dc in directions:
                r, c = row + dr, col + dc
                if (r in range(rows) and c in range(cols) and
                    grid[r][c] == "1" and (r, c) not in visit):
                    q.append((r, c))
                    visit.add((r, c))

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == "1" and (r, c) not in visit:
                bfs(r, c)
                islands += 1
    return islands
```

### 2. Clone Graph
**Pattern**: Deep copy graph.
**Trick**: Use a HashMap `oldNode -> newNode` to track visited/cloned nodes.

```python
def cloneGraph(node: 'Node') -> 'Node':
    oldToNew = {}
    
    def dfs(node):
        if node in oldToNew:
            return oldToNew[node]
        
        copy = Node(node.val)
        oldToNew[node] = copy
        for nei in node.neighbors:
            copy.neighbors.append(dfs(nei))
        return copy
        
    return dfs(node) if node else None
```

### 3. Course Schedule (Topological Sort)
**Pattern**: Detect cycle in directed graph.
**Trick**: DFS with 3 states: `visiting` (current path), `visited` (fully processed), `unvisited`. If we see `visiting` node, cycle exists.

```python
def canFinish(numCourses: int, prerequisites: List[List[int]]) -> bool:
    preMap = { i:[] for i in range(numCourses) }
    for crs, pre in prerequisites:
        preMap[crs].append(pre)
        
    visitSet = set() # visiting
    
    def dfs(crs):
        if crs in visitSet: return False # Cycle
        if preMap[crs] == []: return True
        
        visitSet.add(crs)
        for pre in preMap[crs]:
            if not dfs(pre): return False
        visitSet.remove(crs)
        preMap[crs] = [] # optimization
        return True
        
    for crs in range(numCourses):
        if not dfs(crs): return False
    return True
```
