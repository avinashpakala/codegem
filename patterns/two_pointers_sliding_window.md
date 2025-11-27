# 👯 Two Pointers & 🪟 Sliding Window

These patterns are crucial for optimizing array/string problems from $O(N^2)$ to $O(N)$.

## 👯 Two Pointers

**Concept**: Use two pointers (indices) to traverse the data structure, typically from different ends or at different speeds.

### 1. Valid Palindrome
**Pattern**: Check if string reads same forward and backward.
**Trick**: `L` at start, `R` at end. Move towards center. Skip non-alphanumeric.

```python
def isPalindrome(s: str) -> bool:
    l, r = 0, len(s) - 1
    while l < r:
        while l < r and not alphaNum(s[l]): l += 1
        while r > l and not alphaNum(s[r]): r -= 1
        if s[l].lower() != s[r].lower(): return False
        l, r = l + 1, r - 1
    return True
    
def alphaNum(c):
    return (ord('A') <= ord(c) <= ord('Z') or 
            ord('a') <= ord(c) <= ord('z') or 
            ord('0') <= ord(c) <= ord('9'))
```

### 2. Two Sum II - Input Array Is Sorted
**Pattern**: Find two numbers that add up to target in a **sorted** array.
**Trick**: If sum > target, decrease `R`. If sum < target, increase `L`.

```python
def twoSum(numbers: List[int], target: int) -> List[int]:
    l, r = 0, len(numbers) - 1
    while l < r:
        curSum = numbers[l] + numbers[r]
        if curSum > target:
            r -= 1
        elif curSum < target:
            l += 1
        else:
            return [l + 1, r + 1] # 1-indexed
    return []
```

### 3. 3Sum
**Pattern**: Find three numbers that sum to 0.
**Trick**: Sort the array. Iterate `i` from 0 to `n-2`. For each `i`, use Two Pointers on the rest of the array. **Skip duplicates** to avoid duplicate triplets.

```python
def threeSum(nums: List[int]) -> List[List[int]]:
    res = []
    nums.sort()
    
    for i, a in enumerate(nums):
        if i > 0 and a == nums[i - 1]: # Skip duplicate start
            continue
            
        l, r = i + 1, len(nums) - 1
        while l < r:
            threeSum = a + nums[l] + nums[r]
            if threeSum > 0:
                r -= 1
            elif threeSum < 0:
                l += 1
            else:
                res.append([a, nums[l], nums[r]])
                l += 1
                while nums[l] == nums[l - 1] and l < r: # Skip duplicate L
                    l += 1
    return res
```

### 4. Container With Most Water
**Pattern**: Maximize area between two lines.
**Trick**: Area = `min(height[l], height[r]) * (r - l)`. Always move the pointer with the **smaller height** to try and find a taller line.

```python
def maxArea(height: List[int]) -> int:
    l, r = 0, len(height) - 1
    res = 0
    while l < r:
        area = (r - l) * min(height[l], height[r])
        res = max(res, area)
        
        if height[l] < height[r]:
            l += 1
        else:
            r -= 1
    return res
```

---

## 🪟 Sliding Window

**Concept**: Maintain a window (subset) of elements that satisfies a condition. Expand `R` to add elements, shrink `L` to remove elements when condition is violated.

### 1. Best Time to Buy and Sell Stock
**Pattern**: Maximize profit (difference between two numbers, smaller first).
**Trick**: `L` is buy day, `R` is sell day. If `price[R] < price[L]`, move `L` to `R` (found a new low).

```python
def maxProfit(prices: List[int]) -> int:
    l, r = 0, 1 # l=buy, r=sell
    maxP = 0
    
    while r < len(prices):
        if prices[l] < prices[r]:
            profit = prices[r] - prices[l]
            maxP = max(maxP, profit)
        else:
            l = r # Found a lower buy price
        r += 1
    return maxP
```

### 2. Longest Substring Without Repeating Characters
**Pattern**: Find longest valid substring.
**Trick**: Use a Set to track characters in current window. If `s[r]` is in set, remove `s[l]` and increment `l` until `s[r]` is no longer in set.

```python
def lengthOfLongestSubstring(s: str) -> int:
    charSet = set()
    l = 0
    res = 0
    
    for r in range(len(s)):
        while s[r] in charSet:
            charSet.remove(s[l])
            l += 1
        charSet.add(s[r])
        res = max(res, r - l + 1)
    return res
```

### 3. Longest Repeating Character Replacement
**Pattern**: Longest substring with same char after k replacements.
**Trick**: Valid window condition: `(window_len - max_freq_char) <= k`.
We don't need to shrink window size, just shift it, but standard template shrinks it.

```python
def characterReplacement(s: str, k: int) -> int:
    count = {}
    res = 0
    l = 0
    maxf = 0
    
    for r in range(len(s)):
        count[s[r]] = count.get(s[r], 0) + 1
        maxf = max(maxf, count[s[r]])
        
        # Invalid window: chars to replace > k
        while (r - l + 1) - maxf > k:
            count[s[l]] -= 1
            l += 1
        
        res = max(res, r - l + 1)
    return res
```

### 4. Minimum Window Substring (Hard)
**Pattern**: Find smallest substring containing all chars of `t`.
**Trick**: Use two hashmaps (or one). `have` count vs `need` count. Only update `have` when count matches `need`.

```python
def minWindow(s: str, t: str) -> str:
    if t == "": return ""
    
    countT, window = {}, {}
    for c in t: countT[c] = countT.get(c, 0) + 1
    
    have, need = 0, len(countT)
    res, resLen = [-1, -1], float("infinity")
    l = 0
    
    for r in range(len(s)):
        c = s[r]
        window[c] = window.get(c, 0) + 1
        
        if c in countT and window[c] == countT[c]:
            have += 1
            
        while have == need:
            # update result
            if (r - l + 1) < resLen:
                res = [l, r]
                resLen = (r - l + 1)
            
            # pop from left
            window[s[l]] -= 1
            if s[l] in countT and window[s[l]] < countT[s[l]]:
                have -= 1
            l += 1
            
    l, r = res
    return s[l : r + 1] if resLen != float("infinity") else ""

### 5. Sliding Window Maximum (Monotonic Queue)
**Pattern**: Max of every window of size k.
**Trick**: Use a `deque` to store **indices**. Keep it **decreasing** (monotonic). Remove indices out of window from left.

```python
def maxSlidingWindow(nums: List[int], k: int) -> List[int]:
    output = []
    q = collections.deque() # index
    l = r = 0
    
    while r < len(nums):
        # pop smaller values from q
        while q and nums[q[-1]] < nums[r]:
            q.pop()
        q.append(r)
        
        # remove left val from window
        if l > q[0]:
            q.popleft()
            
        if (r + 1) >= k:
            output.append(nums[q[0]])
            l += 1
        r += 1
    return output
```

---

## 🇳🇱 Dutch National Flag

### 1. Sort Colors
**Pattern**: Sort 0s, 1s, 2s in-place.
**Trick**: 3 Pointers. `low` (0s), `mid` (1s), `high` (2s).
- If `nums[mid] == 0`: swap(low, mid), low++, mid++
- If `nums[mid] == 1`: mid++
- If `nums[mid] == 2`: swap(mid, high), high--

```python
def sortColors(nums: List[int]) -> None:
    l, r = 0, len(nums) - 1
    i = 0
    
    def swap(i, j):
        tmp = nums[i]
        nums[i] = nums[j]
        nums[j] = tmp
        
    while i <= r:
        if nums[i] == 0:
            swap(l, i)
            l += 1
        elif nums[i] == 2:
            swap(i, r)
            r -= 1
            i -= 1 # re-check swapped value
        i += 1
```
```
