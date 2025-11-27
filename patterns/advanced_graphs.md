# 🌐 Advanced Graphs

Shortest paths, Minimum Spanning Trees, and more.

## 🛣️ Shortest Path Algorithms

### 1. Dijkstra's Algorithm
**Pattern**: Shortest path in weighted graph (non-negative weights).
**Trick**: Min-Heap `(dist, node)`. Relax edges.
**Time**: $O(E \log V)$

```python
import heapq

def networkDelayTime(times: List[List[int]], n: int, k: int) -> int:
    edges = collections.defaultdict(list)
    for u, v, w in times:
        edges[u].append((v, w))
        
    minHeap = [(0, k)] # (dist, node)
    visit = set()
    t = 0
    
    while minHeap:
        w1, n1 = heapq.heappop(minHeap)
        if n1 in visit: continue
        visit.add(n1)
        t = max(t, w1)
        
        for n2, w2 in edges[n1]:
            if n2 not in visit:
                heapq.heappush(minHeap, (w1 + w2, n2))
                
    return t if len(visit) == n else -1
```

### 2. Bellman-Ford Algorithm
**Pattern**: Shortest path with **negative weights**. Detect negative cycles.
**Trick**: Relax all edges `V-1` times. If can relax again, negative cycle exists.
**Time**: $O(V \cdot E)$

```python
def findCheapestPrice(n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
    prices = [float("inf")] * n
    prices[src] = 0
    
    for i in range(k + 1):
        tmpPrices = prices[:]
        for s, d, p in flights: # s=source, d=dest, p=price
            if prices[s] == float("inf"): continue
            if prices[s] + p < tmpPrices[d]:
                tmpPrices[d] = prices[s] + p
        prices = tmpPrices
        
    return -1 if prices[dst] == float("inf") else prices[dst]
```

---

## 🌲 Minimum Spanning Tree (MST)

### 1. Prim's Algorithm
**Pattern**: Grow MST from a start node.
**Trick**: Similar to Dijkstra. Min-Heap of `(weight, node)`. Add smallest edge to unvisited node.

### 2. Kruskal's Algorithm (Union-Find)
**Pattern**: Sort edges by weight, add if not connected.
**Trick**: Use Union-Find to check connectivity.

```python
def minCostConnectPoints(points: List[List[int]]) -> int:
    N = len(points)
    adj = { i:[] for i in range(N) }
    # Precompute all edges (Manhattan dist)
    edges = []
    for i in range(N):
        for j in range(i + 1, N):
            dist = abs(points[i][0] - points[j][0]) + abs(points[i][1] - points[j][1])
            edges.append((dist, i, j))
            
    edges.sort() # Sort by weight
    
    res = 0
    uf = UnionFind(N) # Assume standard UF class
    edges_count = 0
    
    for dist, u, v in edges:
        if uf.union(u, v):
            res += dist
            edges_count += 1
            if edges_count == N - 1: break
            
    return res
```

---

## 🔄 Other Graph Tricks

### 1. Union-Find (Disjoint Set Union) Template
**Pattern**: Connected components, Cycle detection.
**Trick**: Path Compression + Rank Optimization.

```python
class UnionFind:
    def __init__(self, n):
        self.par = [i for i in range(n)]
        self.rank = [1] * n

    def find(self, n):
        p = self.par[n]
        while p != self.par[p]:
            self.par[p] = self.par[self.par[p]] # Path compression
            p = self.par[p]
        return p

    def union(self, n1, n2):
        p1, p2 = self.find(n1), self.find(n2)
        if p1 == p2: return False
        
        if self.rank[p1] > self.rank[p2]:
            self.par[p2] = p1
            self.rank[p1] += self.rank[p2]
        else:
            self.par[p1] = p2
            self.rank[p2] += self.rank[p1]
        return True
```

### 2. Hierholzer's Algorithm (Eulerian Path)
**Pattern**: Visit every edge exactly once.
**Trick**: "Reconstruct Itinerary". DFS post-order traversal.

```python
def findItinerary(tickets: List[List[str]]) -> List[str]:
    adj = collections.defaultdict(list)
    tickets.sort(reverse=True) # Sort for lexical order
    for src, dst in tickets:
        adj[src].append(dst)
        
    res = []
    def dfs(src):
        while adj[src]:
            dst = adj[src].pop()
            dfs(dst)
        res.append(src)
        
    dfs("JFK")
    return res[::-1]
```
