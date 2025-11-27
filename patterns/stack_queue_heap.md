# 📚 Stack, Queue & Heap

Data structures that order element processing.

## 🥞 Stack (LIFO)
**Concept**: Last In, First Out.
**Python**: Use a `list`. `append()` to push, `pop()` to pop.

### 1. Valid Parentheses
**Pattern**: Matching pairs.
**Trick**: Map closing bracket to opening. If char is closing, check if stack top matches.

```python
def isValid(s: str) -> bool:
    stack = []
    closeToOpen = {")": "(", "]": "[", "}": "{"}
    
    for c in s:
        if c in closeToOpen:
            if stack and stack[-1] == closeToOpen[c]:
                stack.pop()
            else:
                return False
        else:
            stack.append(c)
    return True if not stack else False
```

### 2. Min Stack
**Pattern**: Retrieve minimum element in O(1).
**Trick**: Maintain a second stack `minStack` that tracks the minimum value *at that height* of the main stack.

```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.minStack = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        val = min(val, self.minStack[-1] if self.minStack else val)
        self.minStack.append(val)

    def pop(self) -> None:
        self.stack.pop()
        self.minStack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def getMin(self) -> int:
        return self.minStack[-1]
```

### 3. Daily Temperatures (Monotonic Stack)
**Pattern**: Find next greater element.
**Trick**: Keep stack **decreasing**. If current `t` > `stack.top`, we found the "next greater" for the stack top. Pop and record result.

```python
def dailyTemperatures(temperatures: List[int]) -> List[int]:
    res = [0] * len(temperatures)
    stack = [] # pair: [temp, index]
    
    for i, t in enumerate(temperatures):
        while stack and t > stack[-1][0]:
            stackT, stackInd = stack.pop()
            res[stackInd] = (i - stackInd)
        stack.append([t, i])
    return res

### 4. Largest Rectangle in Histogram (Hard)
**Pattern**: Find max area in histogram.
**Trick**: **Monotonic Increasing Stack**. Store `(index, height)`.
When `current_h < stack_top_h`, pop stack. The popped height can extend to the right (current index) and left (index in stack).
**Crucial**: Push `start_index` of the popped element, not current `i`, because the new bar can extend backwards to where the taller bars were.

```python
def largestRectangleArea(heights: List[int]) -> int:
    maxArea = 0
    stack = [] # pair: (index, height)
    
    for i, h in enumerate(heights):
        start = i
        while stack and stack[-1][1] > h:
            index, height = stack.pop()
            maxArea = max(maxArea, height * (i - index))
            start = index
        stack.append((start, h))
        
    for i, h in stack:
        maxArea = max(maxArea, h * (len(heights) - i))
    return maxArea
```

### 5. Asteroid Collision
**Pattern**: Moving objects colliding.
**Trick**: Stack stores survivors. Collision only if `stack[-1] > 0` (moving right) and `new < 0` (moving left).
Loop while collision is possible.

```python
def asteroidCollision(asteroids: List[int]) -> List[int]:
    stack = []
    for a in asteroids:
        while stack and a < 0 and stack[-1] > 0:
            diff = a + stack[-1]
            if diff < 0: # stack top destroyed, a continues
                stack.pop()
            elif diff > 0: # a destroyed
                a = 0
            else: # both destroyed
                a = 0
                stack.pop()
        if a:
            stack.append(a)
    return stack
```

### 6. Basic Calculator (Expression Parsing)
**Pattern**: Evaluate string with `+`, `-`, `()`.
**Trick**: Use `stack` to store `res` and `sign` when encountering `(`.
`res` accumulates sum. `sign` is 1 or -1.
When `(`, push `res` and `sign`, reset them.
When `)`, `res = res * pop_sign + pop_res`.

```python
def calculate(s: str) -> int:
    res, num, sign = 0, 0, 1
    stack = []
    
    for c in s:
        if c.isdigit():
            num = num * 10 + int(c)
        elif c in "+-":
            res += sign * num
            num = 0
            sign = 1 if c == "+" else -1
        elif c == "(":
            stack.append(res)
            stack.append(sign)
            sign = 1
            res = 0
        elif c == ")":
            res += sign * num
            res *= stack.pop() # sign
            res += stack.pop() # old res
            num = 0
    return res + sign * num
```

### 7. Remove K Digits
**Pattern**: Smallest number after removing k digits.
**Trick**: **Monotonic Increasing Stack**.
We want small digits at the start (high place value). If `current < stack[-1]`, pop stack (remove larger previous digit) and decrement k.

```python
def removeKdigits(num: str, k: int) -> str:
    stack = []
    for c in num:
        while k > 0 and stack and stack[-1] > c:
            k -= 1
            stack.pop()
        stack.append(c)
        
    stack = stack[:len(stack) - k] # remove remaining k from end
    res = "".join(stack)
    return str(int(res)) if res else "0"
```
```

---

## 🚶 Queue (FIFO)
**Concept**: First In, First Out.
**Python**: Use `collections.deque`. `append()` to push, `popleft()` to pop.

### 1. Implement Stack using Queues
**Trick**: Use one queue. When pushing `x`, append it, then rotate the queue `n-1` times so `x` is at the front.

---

## 🏔️ Heap (Priority Queue)
**Concept**: Keep track of Min or Max element efficiently.
**Python**: `heapq` module. Default is **Min Heap**. For **Max Heap**, negate values.

### 1. Kth Largest Element in a Stream
**Pattern**: Maintain top K elements.
**Trick**: Min Heap of size K. If size > K, pop min. The root is the Kth largest.

```python
import heapq

class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.minHeap = nums
        self.k = k
        heapq.heapify(self.minHeap)
        while len(self.minHeap) > k:
            heapq.heappop(self.minHeap)

    def add(self, val: int) -> int:
        heapq.heappush(self.minHeap, val)
        if len(self.minHeap) > self.k:
            heapq.heappop(self.minHeap)
        return self.minHeap[0]
```

### 2. Merge K Sorted Lists
**Pattern**: Merge multiple sorted streams.
**Trick**: Put the first node of each list into a Min Heap. Pop min, add its next node to heap.

```python
def mergeKLists(lists: List[Optional[ListNode]]) -> Optional[ListNode]:
    if not lists or len(lists) == 0: return None
    
    while len(lists) > 1:
        mergedLists = []
        for i in range(0, len(lists), 2):
            l1 = lists[i]
            l2 = lists[i + 1] if (i + 1) < len(lists) else None
            mergedLists.append(self.mergeList(l1, l2))
        lists = mergedLists
    return lists[0]

# Helper merge function (standard merge sort step)
```

### 3. Find Median from Data Stream
**Pattern**: Median of stream.
**Trick**: Two Heaps. `small` (Max Heap) for lower half, `large` (Min Heap) for upper half. Balance sizes.

```python
class MedianFinder:
    def __init__(self):
        self.small, self.large = [], [] # small is maxHeap, large is minHeap

    def addNum(self, num: int) -> None:
        heapq.heappush(self.small, -1 * num)
        
        # Make sure every num in small is <= every num in large
        if (self.small and self.large and 
            (-1 * self.small[0]) > self.large[0]):
            val = -1 * heapq.heappop(self.small)
            heapq.heappush(self.large, val)
            
        # Uneven size?
        if len(self.small) > len(self.large) + 1:
            val = -1 * heapq.heappop(self.small)
            heapq.heappush(self.large, val)
        if len(self.large) > len(self.small) + 1:
            val = heapq.heappop(self.large)
            heapq.heappush(self.small, -1 * val)

    def findMedian(self) -> float:
        if len(self.small) > len(self.large):
            return -1 * self.small[0]
        if len(self.large) > len(self.small):
            return self.large[0]
        return (-1 * self.small[0] + self.large[0]) / 2
```
