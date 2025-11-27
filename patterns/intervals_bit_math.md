# ⏰ Intervals & 🔢 Bit Manipulation & Math

Specialized topics that often appear in specific company interviews (e.g., Meta loves Intervals).

## ⏰ Intervals
**Concept**: Overlapping time ranges.
**Trick**: **Always sort by start time** ($O(N \log N)$).

### 1. Merge Intervals
**Pattern**: Combine overlapping.
**Trick**: Sort. If `current_start <= previous_end`, they overlap. Merge by taking `max(prev_end, cur_end)`.

```python
def merge(intervals: List[List[int]]) -> List[List[int]]:
    intervals.sort(key=lambda x: x[0])
    output = [intervals[0]]
    
    for start, end in intervals[1:]:
        lastEnd = output[-1][1]
        
        if start <= lastEnd:
            output[-1][1] = max(lastEnd, end)
        else:
            output.append([start, end])
    return output
```

### 2. Non-overlapping Intervals
**Pattern**: Remove min intervals to make rest non-overlapping.
**Trick**: Sort by start. If overlap, **remove the one with larger end time** (greedy approach) to minimize chance of future overlaps.

```python
def eraseOverlapIntervals(intervals: List[List[int]]) -> int:
    intervals.sort()
    res = 0
    prevEnd = intervals[0][1]
    
    for start, end in intervals[1:]:
        if start >= prevEnd:
            prevEnd = end
        else:
            res += 1
            prevEnd = min(prevEnd, end) # remove the one that ends later
    return res
```

---

## 🔢 Bit Manipulation
**Concept**: Binary operations.
**Trick**: XOR (`^`) is key. `x ^ x = 0`. `x ^ 0 = x`.

### 1. Single Number
**Pattern**: All appear twice except one.
**Trick**: XOR everything. Duplicates cancel out.

```python
def singleNumber(nums: List[int]) -> int:
    res = 0
    for n in nums:
        res = n ^ res
    return res
```

### 2. Number of 1 Bits (Hamming Weight)
**Pattern**: Count set bits.
**Trick**: `n = n & (n - 1)` removes the least significant bit. Count how many times you can do this until 0.

```python
def hammingWeight(n: int) -> int:
    res = 0
    while n:
        n &= (n - 1)
        res += 1
    return res
```

### 3. Missing Number
**Pattern**: Array `0..n` missing one.
**Trick**: XOR all indices `0..n` and all values. Result is missing number. (Or use Sum formula).

```python
def missingNumber(nums: List[int]) -> int:
    res = len(nums)
    for i in range(len(nums)):
        res += (i - nums[i])
    return res
```

### 4. Reverse Bits
**Pattern**: Reverse binary representation.
**Trick**: Iterate 32 times. Shift `res` left, add last bit of `n`, shift `n` right.

```python
def reverseBits(n: int) -> int:
    res = 0
    for i in range(32):
        bit = (n >> i) & 1
        res = res | (bit << (31 - i))
    return res
```

---

## 📐 Math & Geometry

### 1. Rotate Image
**Pattern**: Rotate matrix 90 deg clockwise in-place.
**Trick**: Transpose (swap `i,j` with `j,i`) then Reverse each row.
- `matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]`
- `row.reverse()`

```python
def rotate(matrix: List[List[int]]) -> None:
    l, r = 0, len(matrix) - 1
    while l < r:
        for i in range(r - l):
            top, bottom = l, r
            # save the topleft
            topLeft = matrix[top][l + i]

            # move bottom left into top left
            matrix[top][l + i] = matrix[bottom - i][l]

            # move bottom right into bottom left
            matrix[bottom - i][l] = matrix[bottom][r - i]

            # move top right into bottom right
            matrix[bottom][r - i] = matrix[top + i][r]

            # move top left into top right
            matrix[top + i][r] = topLeft
        r -= 1
        l += 1
```

### 2. Spiral Matrix
**Pattern**: Traverse in spiral.
**Trick**: Maintain boundaries: `left, right, top, bottom`. Loop while `left < right` and `top < bottom`.

```python
def spiralOrder(matrix: List[List[int]]) -> List[int]:
    res = []
    left, right = 0, len(matrix[0])
    top, bottom = 0, len(matrix)
    
    while left < right and top < bottom:
        # get every i in the top row
        for i in range(left, right):
            res.append(matrix[top][i])
        top += 1
        
        # get every i in the right col
        for i in range(top, bottom):
            res.append(matrix[i][right - 1])
        right -= 1
        
        if not (left < right and top < bottom):
            break
            
        # get every i in the bottom row
        for i in range(right - 1, left - 1, -1):
            res.append(matrix[bottom - 1][i])
        bottom -= 1
        
        # get every i in the left col
        for i in range(bottom - 1, top - 1, -1):
            res.append(matrix[i][left])
        left += 1
        
    return res
```
