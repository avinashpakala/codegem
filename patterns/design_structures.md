# 🏗️ Design Data Structures

Custom data structures often asked in "Design X" questions.

## 🧠 Cache Designs

### 1. LRU Cache (Least Recently Used)
**Pattern**: O(1) get and put. Evict least recently used.
**Trick**: **HashMap + Doubly Linked List**.
- Map: `key -> Node`
- List: `Head <-> Node <-> Node <-> Tail`
- **Get**: Remove node, add to Right (Most Recent).
- **Put**: If exists, update val, move to Right. If new, add to Right. If over capacity, remove Left (Least Recent).

```python
class Node:
    def __init__(self, key, val):
        self.key, self.val = key, val
        self.prev, self.next = None, None

class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.cache = {} # map key to node
        self.left, self.right = Node(0, 0), Node(0, 0)
        self.left.next, self.right.prev = self.right, self.left

    def remove(self, node):
        prev, nxt = node.prev, node.next
        prev.next, nxt.prev = nxt, prev

    def insert(self, node): # insert at right
        prev, nxt = self.right.prev, self.right
        prev.next = nxt.prev = node
        node.next, node.prev = nxt, prev

    def get(self, key: int) -> int:
        if key in self.cache:
            self.remove(self.cache[key])
            self.insert(self.cache[key])
            return self.cache[key].val
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self.remove(self.cache[key])
        self.cache[key] = Node(key, value)
        self.insert(self.cache[key])
        
        if len(self.cache) > self.cap:
            lru = self.left.next
            self.remove(lru)
            del self.cache[lru.key]
```

### 2. LFU Cache (Least Frequently Used)
**Pattern**: Evict least frequent. Tie-break with LRU.
**Trick**: **Two HashMaps + N Doubly Linked Lists**.
- `vals`: key -> value, freq
- `lists`: freq -> DoublyLinkedList (LRU logic within freq bucket)
- `minFreq`: track minimum frequency to evict in O(1).

---

## 🌳 Advanced Trees

### 1. Segment Tree (Range Queries)
**Pattern**: Range Sum/Min/Max updates in $O(\log N)$.
**Trick**: Binary Tree where leaf = array element, parent = aggregate of children.

```python
class SegmentTree:
    def __init__(self, total, L, R):
        self.sum = total
        self.left = None
        self.right = None
        self.L = L
        self.R = R
        
    # O(N) build, O(log N) update/query
```

### 2. Fenwick Tree (Binary Indexed Tree)
**Pattern**: Prefix sums with updates.
**Trick**: Use binary representation of index. `i += i & (-i)` to go up, `i -= i & (-i)` to go down. Easier to implement than Segment Tree for sums.

### 3. Trie (Prefix Tree) with Wildcards
**Pattern**: Word Dictionary with `.` matching any char.
**Trick**: DFS when hitting `.`.

```python
def search(self, word: str) -> bool:
    def dfs(j, root):
        cur = root
        for i in range(j, len(word)):
            c = word[i]
            if c == ".":
                for child in cur.children.values():
                    if dfs(i + 1, child): return True
                return False
            else:
                if c not in cur.children: return False
                cur = cur.children[c]
        return cur.endOfWord
    return dfs(0, self.root)
```

---

## 🐦 Design Twitter (Feed)
**Pattern**: News feed from followees.
**Trick**: **Merge K Sorted Lists**.
- Store tweets as `(timestamp, tweetId)` in user-specific lists.
- Get Feed: Pull most recent tweets from all followees. Use Max Heap to merge.

```python
def getNewsFeed(self, userId: int) -> List[int]:
    res = []
    minHeap = []
    
    self.followMap[userId].add(userId)
    for followeeId in self.followMap[userId]:
        if followeeId in self.tweetMap:
            index = len(self.tweetMap[followeeId]) - 1
            count, tweetId = self.tweetMap[followeeId][index]
            heapq.heappush(minHeap, [count, tweetId, followeeId, index - 1])
            
    while minHeap and len(res) < 10:
        count, tweetId, followeeId, index = heapq.heappop(minHeap)
        res.append(tweetId)
        if index >= 0:
            count, tweetId = self.tweetMap[followeeId][index]
            heapq.heappush(minHeap, [count, tweetId, followeeId, index - 1])
    return res
```
