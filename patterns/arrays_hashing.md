# 🧩 Arrays & Hashing

Arrays and Hashing are the foundation of many interview questions. The key is to know when to trade space for time using Hash Maps (Dictionaries) or Sets.

## 💡 Key Techniques

### 1. Hash Map (Dictionary) for O(1) Lookups
**Pattern**: "Find if element exists", "Count frequencies", "Map value to index".
**Trick**: Use `dict` or `set` to avoid nested loops ($O(n^2) \to O(n)$).

#### Example: Two Sum
*Given an array of integers `nums` and an integer `target`, return indices of the two numbers such that they add up to `target`.*

```python
def twoSum(nums: List[int], target: int) -> List[int]:
    prevMap = {}  # val : index
    for i, n in enumerate(nums):
        diff = target - n
        if diff in prevMap:
            return [prevMap[diff], i]
        prevMap[n] = i
    return []
```

### 2. Frequency Counting with `Counter`
**Pattern**: "Find top k frequent", "Anagrams".
**Trick**: `collections.Counter` is your best friend.

#### Example: Valid Anagram
*Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`, and `false` otherwise.*

```python
from collections import Counter

def isAnagram(s: str, t: str) -> bool:
    return Counter(s) == Counter(t)
    # Or manually:
    # if len(s) != len(t): return False
    # countS, countT = {}, {}
    # for i in range(len(s)):
    #     countS[s[i]] = countS.get(s[i], 0) + 1
    #     countT[t[i]] = countT.get(t[i], 0) + 1
    # return countS == countT
```

### 3. Grouping Anagrams
**Pattern**: Group strings that are anagrams of each other.
**Trick**: Sort the string to use as a key OR use a character count tuple (26 chars) as a key for O(N) instead of O(N log N) sorting.

#### Example: Group Anagrams
```python
from collections import defaultdict

def groupAnagrams(strs: List[str]) -> List[List[str]]:
    res = defaultdict(list) # mapping charCount to list of Anagrams
    for s in strs:
        count = [0] * 26 # a ... z
        for c in s:
            count[ord(c) - ord("a")] += 1
        # Tuple is hashable, list is not
        res[tuple(count)].append(s) 
    return list(res.values())
```

### 4. Prefix Sums
**Pattern**: "Sum of subarray", "Product of subarray" (careful with zeros).
**Trick**: Precompute cumulative sums to calculate range sum in $O(1)$.
$Sum(i, j) = Prefix[j] - Prefix[i-1]$

#### Example: Subarray Sum Equals K
*Find total number of continuous subarrays whose sum equals to `k`.*

```python
def subarraySum(nums: List[int], k: int) -> int:
    res = 0
    curSum = 0
    prefixSums = {0: 1} # sum : count, initialized with 0 sum count 1
    
    for n in nums:
        curSum += n
        diff = curSum - k
        res += prefixSums.get(diff, 0)
        prefixSums[curSum] = prefixSums.get(curSum, 0) + 1
    return res
```

### 5. Product of Array Except Self
**Pattern**: Calculate product without division.
**Trick**: Use Prefix Product and Postfix Product arrays (or optimize space to O(1)).

```python
def productExceptSelf(nums: List[int]) -> List[int]:
    res = [1] * len(nums)
    
    prefix = 1
    for i in range(len(nums)):
        res[i] = prefix
        prefix *= nums[i]
        
    postfix = 1
    for i in range(len(nums) - 1, -1, -1):
        res[i] *= postfix
        postfix *= nums[i]
        
    return res
```

### 6. Longest Consecutive Sequence
**Pattern**: Find sequence in unsorted array.
**Trick**: Use a `set` for O(1) lookups. Only start counting if `n-1` is NOT in the set (meaning `n` is the start of a sequence).

```python
def longestConsecutive(nums: List[int]) -> int:
    numSet = set(nums)
    longest = 0
    
    for n in numSet:
        # check if its the start of a sequence
        if (n - 1) not in numSet:
            length = 0
            while (n + length) in numSet:
                length += 1
            longest = max(length, longest)
    return longest

### 7. Majority Element (Boyer-Moore Voting)
**Pattern**: Find element appearing > n/2 times.
**Trick**: Maintain `count`. If `count == 0`, set `candidate = n`. If `n == candidate`, `count++`, else `count--`.

```python
def majorityElement(nums: List[int]) -> int:
    res, count = 0, 0
    for n in nums:
        if count == 0:
            res = n
        count += (1 if n == res else -1)
    return res
```

### 8. Random Pick (Reservoir Sampling)
**Pattern**: Pick random element from stream of unknown size.
**Trick**: For $i$-th element, pick it with probability $1/i$.

```python
import random
def pick(target: int) -> int:
    res = -1
    count = 0
    for i, n in enumerate(nums):
        if n == target:
            count += 1
            if random.randint(1, count) == 1:
                res = i
    return res

### 9. Subarray Sums Divisible by K (Prefix Sum + Modulo)
**Pattern**: Count subarrays where sum is divisible by k.
**Trick**: `(P[j] - P[i]) % k == 0` => `P[j] % k == P[i] % k`.
Store `prefix_sum % k` in a HashMap. Handle negative sums in Python automatically, but in C++/Java use `(sum % k + k) % k`.

```python
def subarraysDivByK(nums: List[int], k: int) -> int:
    res = 0
    curSum = 0
    prefixCount = {0: 1} # remainder : count
    
    for n in nums:
        curSum += n
        remainder = curSum % k
        res += prefixCount.get(remainder, 0)
        prefixCount[remainder] = prefixCount.get(remainder, 0) + 1
    return res
```

### 10. Contiguous Array (0s and 1s)
**Pattern**: Longest subarray with equal number of 0s and 1s.
**Trick**: Transform `0` to `-1`. Problem becomes "Longest Subarray with Sum 0".
Use HashMap `sum -> first_index`.

```python
def findMaxLength(nums: List[int]) -> int:
    count = {0: -1} # sum : index
    res = 0
    curSum = 0
    
    for i, n in enumerate(nums):
        curSum += (1 if n == 1 else -1)
        if curSum in count:
            res = max(res, i - count[curSum])
        else:
            count[curSum] = i
    return res
```

### 11. 2D Prefix Sum (Range Sum Query 2D)
**Pattern**: Calculate sum of rectangle `(r1, c1)` to `(r2, c2)` efficiently.
**Trick**: Precompute 2D prefix sum `P[i][j]` = sum of rectangle from `(0,0)` to `(i,j)`.
`Sum = P[r2][c2] - P[r1-1][c2] - P[r2][c1-1] + P[r1-1][c1-1]`.

```python
class NumMatrix:
    def __init__(self, matrix: List[List[int]]):
        ROWS, COLS = len(matrix), len(matrix[0])
        self.sumMat = [[0] * (COLS + 1) for r in range(ROWS + 1)]
        
        for r in range(ROWS):
            for c in range(COLS):
                self.sumMat[r + 1][c + 1] = (self.sumMat[r][c + 1] + 
                                             self.sumMat[r + 1][c] - 
                                             self.sumMat[r][c] + 
                                             matrix[r][c])

    def sumRegion(self, r1: int, c1: int, r2: int, c2: int) -> int:
        return (self.sumMat[r2 + 1][c2 + 1] - 
                self.sumMat[r1][c2 + 1] - 
                self.sumMat[r2 + 1][c1] + 
                self.sumMat[r1][c1])
```

### 12. Difference Array (Range Updates)
**Pattern**: Increment range `[l, r]` by `val` multiple times. Query final array.
**Trick**: Instead of updating all elements, update boundaries.
`diff[l] += val`
`diff[r + 1] -= val`
Final array is the Prefix Sum of `diff`.

```python
def rangeUpdates(length: int, updates: List[List[int]]) -> List[int]:
    diff = [0] * (length + 1)
    
    for l, r, val in updates:
        diff[l] += val
        diff[r + 1] -= val
        
    res = []
    cur = 0
    for i in range(length):
        cur += diff[i]
        res.append(cur)
    return res
```
```
```
