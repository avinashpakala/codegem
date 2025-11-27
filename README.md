# ⚡ Algorithm Tricks & Mnemonics
> Comprehensive guide for Blind 75, Meta, and Salesforce interview patterns.

## 📚 Pattern Library

| Category | Topics Covered |
|----------|----------------|
| [**Arrays & Hashing**](patterns/arrays_hashing.md) | `HashMap`, `HashSet`, `Counter`, Prefix Sums, Anagrams |
| [**Two Pointers & Sliding Window**](patterns/two_pointers_sliding_window.md) | Palindromes, 3Sum, Stock, Longest Substring |
| [**Stack, Queue & Heap**](patterns/stack_queue_heap.md) | Parentheses, Monotonic Stack, Top K Elements, Merge K Lists |
| [**Trees & Graphs**](patterns/trees_graphs.md) | DFS/BFS, LCA, Tries, Islands, Topological Sort |
| [**DP & Greedy**](patterns/dp_greedy.md) | 1D/2D DP, Climbing Stairs, Jump Game, Kadane's |
| [**Intervals, Bit & Math**](patterns/intervals_bit_math.md) | Merge Intervals, XOR tricks, Matrix Rotation |
| [**Backtracking**](patterns/backtracking.md) | Subsets, Permutations, N-Queens, Word Search |
| [**Advanced Graphs**](patterns/advanced_graphs.md) | Dijkstra, Bellman-Ford, Prim/Kruskal, Union-Find |
| [**Advanced DP**](patterns/advanced_dp.md) | Knapsack, Edit Distance, Burst Balloons, Bitmask |
| [**Design Structures**](patterns/design_structures.md) | LRU Cache, Segment Tree, Trie, Design Twitter |
| [**Google Specifics**](patterns/google_specifics.md) | KMP, Rolling Hash, Convex Hull, Robot Cleaner |
| [**Pitfalls & Edge Cases**](patterns/pitfalls_edge_cases.md) | Empty/Null, Overflow, Cycles, Off-by-one |

## 📝 Problem Sets
> Curated lists of problems linked to the patterns above.

| Category | Problem List |
|----------|--------------|
| **Arrays & Hashing** | [View Problems](problems/arrays_hashing_problems.md) |
| **Two Pointers & Sliding Window** | [View Problems](problems/two_pointers_sliding_window_problems.md) |
| **Stack, Queue & Heap** | [View Problems](problems/stack_queue_heap_problems.md) |
| **Trees & Graphs** | [View Problems](problems/trees_graphs_problems.md) |
| **DP & Greedy** | [View Problems](problems/dp_greedy_problems.md) |
| **Advanced & Google** | [View Problems](problems/advanced_problems.md) |

---

## 🧠 General Problem Solving Framework
1.  **Listen & Clarify**: Constraints? Input size? (e.g., `n=10^5` -> `O(n)` or `O(n log n)`).
2.  **Example**: Walk through a non-trivial case.
3.  **Brute Force**: State it, then optimize.
4.  **Optimize**: Use the patterns above.

---

## 🏢 Company Specific Tricks

### Meta (Facebook)
-   **Focus**: Arrays, Strings, Trees, Graphs.
-   **Speed**: You need to solve 2 questions in 45 mins. Memorize standard algorithms.
-   **Common Patterns**:
    -   **Subarray Sum equals K**: Use Prefix Sum + HashMap.
    -   **Valid Palindrome II**: Two pointers, skip one char.
    -   **Lowest Common Ancestor**: Recursion.
    -   **Merge Intervals**: Sort by start time.

### Salesforce
-   **Focus**: Data Structures, SQL, System Design (for senior).
-   **Common Patterns**:
    -   **LRU Cache**: Doubly Linked List + HashMap.
    -   **Valid Parentheses**: Stack.
    -   **SQL**: Joins, Group By, Having.
    -   **Integration**: Know REST/SOAP concepts if asked about system design.

---

## ⚡ Quick Mnemonics
-   **Stack**: LIFO (Last In, First Out) -> "Pancakes".
-   **Queue**: FIFO (First In, First Out) -> "Line at Starbucks".
-   **Heap**: "Priority Queue" -> Keep track of Min/Max efficiently.
-   **Binary Search**: "Sorted? Cut in half." -> `O(log n)`.
