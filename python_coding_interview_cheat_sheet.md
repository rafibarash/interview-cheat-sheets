# Python Coding Interview Cheat Sheet

## Table of Contents

* [Data Structures](#data-structures)
  * [Arrays](#arrays)
  * [Strings](#strings)
  * [Linked Lists](#linked-lists)
  * [Stacks and Queues](#stacks-and-queues)
  * [Hash Tables](#hash-tables)
  * [Binary Trees](#binary-trees)
  * [Binary Search Trees](#binary-search-trees)
  * [Heaps](#heaps)
  * [Graphs](#graphs)
* [Algorithms](#algorithms)
  * [Searching](#searching)
  * [Sorting](#sorting)
  * [Recursion](#recursion)
  * [Dynamic Programming](#dynamic-programming)
  * [Greedy Algorithms](#greedy-algorithms)
  * [Backtracking](#backtracking)
  * [Bit Manipulation](#bit-manipulation)

## Data Structures

### Arrays

* Many array problems can be solved trivially in O(n) time with O(n) additional space
* Subtler solutions often available with O(1) space.
* Prefer iterating through list to repeatedly indexing (index out of bounds errors are all too easy to make).
* Many array problems can be solved with low/high pointers in a while loop:
    ```python
    low, high = 0, len(A) - 1
    while low <= high:
      if blah:
        blahblah()
        low += 1
      else:
        blahblahblah()
        high -= 1
    ```
* Others can be solved with sliding window:
    ```python
    left = 0
    for v, right in enumerate(A):
      if blah:
        blahblah()
      else:
        blahblahblah()
        left += 1
    ```

### Strings

* There are exactly `n(n + 1) / 2` possible unique substrings of a string.
* Thus, many string algorithms can initially be coded in O(n^2)
* Many string problems can be optimized with a frequency counter, hash table, or sorting.
* Useful code
    ```python
    from collections import Counter

    def build_frequency_counter(s):
      # s = 'hello'
      # returns Counter({'h': 1, 'e': 1, 'l': 2, 'o': 1})
      # run in O(n)
      return Counter(s)

    def is_anagram(s1, s2):
      # interview question: 'are two strings anagrams?'
      # Csci equiv: 'do these two strings have equal frequency counters?'
      # runs in O(n)
      return Counter(s1) == Counter(s2)

    def is_palindrome(s):
      # check if reversed string equal to string
      # s[::-1] means: from beg to end, return string in reverse order
      # not most efficient code, but still runs in O(n)
      return s == s[::-1]

    def print_all_substrings(s):
      # runs in O(n^2)
      for i in range(len(s)):
        for j in range(i+1, len(s)+1):
          print(s[i:j])

    def concat_strings(*parts):
      # returning s1 + s2 + ... + sn with + runs in O(n^2)
      # join() pre-allocates and concatenates in O(n)
      return ''.join(parts)

    def sort_string(s):
      # sorted() returns sorted array of chars
      # additional join() required to merge char array back to string
      # runs in O(nlog(n))
      return ''.join(sorted(s))
    ```

### Linked Lists

Some useful solutions to reference:

```python
def reverseList(head):
  prev, curr = None, head
  while curr:
    # Note the tuple unpacking order.
    # curr.next is evaluated first, then assigned to prev.
    curr.next, curr, prev = prev, curr.next, curr
  return prev

def mergeTwoLists(list1, list2):
  # It can be helpful to use a dummy (head) node to avoid edge cases.
  head = runner = ListNode()
  while list1 and list2:
    if list1.val < list2.val:
      runner.next, list1 = list1, list1.next
    else:
      runner.next, list2 = list2, list2.next
    runner = runner.next
  runner.next = list1 or list2
  return head.next

def hasCycle(head):
    slow = fast = head
    while fast and fast.next:
        slow, fast = slow.next, fast.next.next
        if slow == fast:
            return True
    return False
```

### Stacks and Queues

* Stacks use the `list()` data structure with `append()` and `pop()` methods, both in O(1) time.
* Queues use the `collections.deque()` data structure with `append()` and `popleft()` methods, both in O(1) time.

### Hash Tables

* Build a frequency counter from string 's' in one line with `freqCounter = collections.Counter(s)`

### Binary Trees

Traversals

```python
def traverse(tree):
    if tree:
        print('Preorder Traversal:', tree.data)     # Preorder
        traverse(tree.left)
        print('Inorder Traversal:', tree.data)      # Inorder
        traverse(tree.right)
        print('Postorder Traversal:', tree.data)    # Postorder
```

Searching

```python
# Notice how bfs uses a queue and dfs uses a stack.
# Otherwise, these algorithms are the same

def bfs(root):
    q, ans = collections.deque([root]), []
    while q:
        cur = q.popleft()
        if cur:
            ans.append(cur.data)
            q.extend([cur.left, cur.right])
    return ans

def dfs(root):
    stack, ans = [root], []
    while stack:
        cur = stack.pop()
        if cur:
            ans.append(cur.data)
            stack.extend([cur.left, cur.right])
    return ans
```

### Binary Search Trees

* Inorder traversal of a BST yields sorted order — useful for validation and kth-smallest problems.
* Average case O(log n) search/insert/delete, worst case O(n) if unbalanced.

```python
def search_bst(root, target):
    while root:
        if target == root.val:
            return root
        root = root.left if target < root.val else root.right
    return None

def insert_bst(root, val):
    if not root:
        return TreeNode(val)
    if val < root.val:
        root.left = insert_bst(root.left, val)
    else:
        root.right = insert_bst(root.right, val)
    return root

def is_valid_bst(root, lo=float('-inf'), hi=float('inf')):
    if not root:
        return True
    if not (lo < root.val < hi):
        return False
    return (is_valid_bst(root.left, lo, root.val) and
            is_valid_bst(root.right, root.val, hi))
```


### Heaps

* Python's `heapq` is a **min-heap**. For a max-heap, negate values.
* Use heaps for top-k, k-way merge, and median-tracking problems.

```python
import heapq

# Basic operations
nums = [3, 1, 4, 1, 5]
heapq.heapify(nums)           # O(n) — convert list to heap in-place
heapq.heappush(nums, 2)       # O(log n)
smallest = heapq.heappop(nums) # O(log n) — pops smallest element

# Top-k largest elements
def top_k_largest(nums, k):
    return heapq.nlargest(k, nums)  # O(n log k)

# Top-k smallest elements
def top_k_smallest(nums, k):
    return heapq.nsmallest(k, nums) # O(n log k)

# Max-heap via negation
max_heap = []
for val in [3, 1, 4]:
    heapq.heappush(max_heap, -val)
largest = -heapq.heappop(max_heap)  # 4
```


### Graphs

Depth-First Search

```python
def dfs(graph, start):
    visited, stack = set(), [start]
    while stack:
        vertex = stack.pop()
        if vertex not in visited:
            visited.add(vertex)
            stack.extend(graph[vertex] - visited)
    return visited
 ```

Breadth-First Search

```python
def bfs(graph, start):
    visited, queue = set(), collections.deque([start])
    while queue:
        vertex = queue.popleft()  # O(1) vs O(n) for list.pop(0)
        if vertex not in visited:
            visited.add(vertex)
            queue.extend(graph[vertex] - visited)
    return visited
```

## Algorithms

### Searching

Binary Search — O(log n). Use for sorted arrays or when the search space can be halved.

```python
def binary_search(nums, target):
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2  # avoids overflow in other languages
        if nums[mid] == target:
            return mid
        elif nums[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    return -1
```

Binary search on answer — use when the problem asks "find the minimum/maximum value that satisfies a condition".

```python
def binary_search_on_answer(lo, hi, condition):
    while lo < hi:
        mid = lo + (hi - lo) // 2
        if condition(mid):
            hi = mid      # mid could be the answer
        else:
            lo = mid + 1  # mid is too small
    return lo
```

`bisect` module — useful for maintaining sorted order.

```python
import bisect

sorted_list = [1, 3, 5, 7]
bisect.insort(sorted_list, 4)  # [1, 3, 4, 5, 7]
idx = bisect.bisect_left(sorted_list, 5)  # 3 — leftmost insertion point
```

### Sorting

| Algorithm      | Best       | Average    | Worst      | Space  | Stable? |
|----------------|------------|------------|------------|--------|---------|
| Timsort (Python's `sorted()`) | O(n) | O(n log n) | O(n log n) | O(n) | Yes |
| Merge Sort     | O(n log n) | O(n log n) | O(n log n) | O(n)   | Yes     |
| Quick Sort     | O(n log n) | O(n log n) | O(n²)      | O(log n) | No    |
| Heap Sort      | O(n log n) | O(n log n) | O(n log n) | O(1)   | No      |
| Counting Sort  | O(n + k)   | O(n + k)   | O(n + k)   | O(k)   | Yes     |

```python
# Python's built-in sort is Timsort — use it unless asked otherwise
nums.sort()                        # in-place, O(n log n)
sorted_nums = sorted(nums)         # returns new list

# Custom sort key
intervals.sort(key=lambda x: x[0])           # sort by first element
words.sort(key=lambda w: (-len(w), w))        # sort by length desc, then alphabetically

# Counting sort — O(n + k), useful when value range k is small
def counting_sort(nums):
    if not nums:
        return []
    lo, hi = min(nums), max(nums)
    counts = [0] * (hi - lo + 1)
    for n in nums:
        counts[n - lo] += 1
    return [n + lo for n, c in enumerate(counts) for _ in range(c)]
```

### Recursion

* The two elements of a recursive function
  1. A base case
  2. Meat of the function that also invokes the recursive call, eventually converging to the base case.
* Use tail recursion to speed up your algorithm (Feel free to explain what tail recursion is and make a pull request).

### Dynamic Programming

* **When to use DP:** The problem has overlapping subproblems and optimal substructure.
* **Top-down (memoization):** Recursive + cache. Easier to write, can be slower due to recursion overhead.
* **Bottom-up (tabulation):** Iterative. Usually faster, sometimes uses less space.

Top-down template (memoization)

```python
from functools import lru_cache

def solve(nums):
    @lru_cache(maxsize=None)
    def dp(i):
        if i >= len(nums):   # base case
            return 0
        return max(
            dp(i + 1),                   # skip
            nums[i] + dp(i + 2),         # take (example: house robber)
        )
    return dp(0)
```

Bottom-up template (tabulation)

```python
def solve(nums):
    n = len(nums)
    dp = [0] * (n + 1)
    for i in range(n - 1, -1, -1):
        dp[i] = max(dp[i + 1], nums[i] + dp[min(i + 2, n)])
    return dp[0]
```

Common DP patterns:
* **1D DP:** Fibonacci, climbing stairs, house robber
* **2D DP:** Grid paths, longest common subsequence, edit distance
* **Knapsack:** Subset sum, coin change, partition equal subset
* **Interval DP:** Matrix chain multiplication, burst balloons

Classic example — coin change (minimum coins to make amount)

```python
def coin_change(coins, amount):
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for i in range(1, amount + 1):
        for coin in coins:
            if coin <= i:
                dp[i] = min(dp[i], dp[i - coin] + 1)
    return dp[amount] if dp[amount] != float('inf') else -1
```

### Greedy Algorithms

* **When to use:** Making the locally optimal choice at each step leads to the globally optimal solution.
* **How to verify:** If you can prove that a greedy choice doesn't eliminate better solutions, greedy works.
* Common greedy problems: interval scheduling, activity selection, Huffman coding, jump game.

```python
# Interval scheduling — maximum non-overlapping intervals
def max_non_overlapping(intervals):
    intervals.sort(key=lambda x: x[1])  # sort by end time
    count, end = 0, float('-inf')
    for s, e in intervals:
        if s >= end:
            count += 1
            end = e
    return count

# Jump game — can you reach the last index?
def can_jump(nums):
    farthest = 0
    for i, jump in enumerate(nums):
        if i > farthest:
            return False
        farthest = max(farthest, i + jump)
    return True
```

### Backtracking

* Backtracking = DFS + pruning. Build candidates incrementally, abandon a path as soon as it can't lead to a valid solution.
* Common problems: permutations, combinations, subsets, N-queens, word search.

General template

```python
def backtrack(result, path, choices):
    if is_solution(path):
        result.append(path[:])
        return
    for choice in choices:
        if is_valid(choice):
            path.append(choice)
            backtrack(result, path, next_choices)  # recurse
            path.pop()                             # undo choice
```

Permutations

```python
def permutations(nums):
    res = []
    def backtrack(path, remaining):
        if not remaining:
            res.append(path[:])
            return
        for i, num in enumerate(remaining):
            path.append(num)
            backtrack(path, remaining[:i] + remaining[i+1:])
            path.pop()
    backtrack([], nums)
    return res
```

Subsets

```python
def subsets(nums):
    res = []
    def backtrack(start, path):
        res.append(path[:])
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    backtrack(0, [])
    return res
```

### Bit Manipulation

* Useful for problems involving sets, toggling states, or power-of-2 checks.

```python
# Common operations
x & 1           # check if odd
x & (x - 1)     # clear lowest set bit (0 means x is power of 2)
x | (1 << i)    # set bit i
x ^ (1 << i)    # toggle bit i
x & (1 << i)    # check bit i
bin(x).count('1') # count set bits (popcount)

# XOR tricks
# a ^ a = 0, a ^ 0 = a
# Find the single unique number in a list where every other appears twice
def single_number(nums):
    result = 0
    for n in nums:
        result ^= n
    return result
```
