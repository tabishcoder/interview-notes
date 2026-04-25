# Data Structures and Algorithms in Python — Complete Interview Study Guide

> **How to use this guide:** Study Part 1 first — complexity analysis is the language of every interview. Work through Parts 2–16 in order, running each code example. Use Part 17 (patterns) and Part 18 (strategy) before mock interviews. Revise using the final cheat sheet.

---

## Table of Contents

1. [Part 1 — Algorithm Analysis](#part-1--algorithm-analysis)
2. [Part 2 — Arrays](#part-2--arrays)
3. [Part 3 — Strings](#part-3--strings)
4. [Part 4 — Hash Tables](#part-4--hash-tables)
5. [Part 5 — Linked Lists](#part-5--linked-lists)
6. [Part 6 — Stacks](#part-6--stacks)
7. [Part 7 — Queues](#part-7--queues)
8. [Part 8 — Recursion and Backtracking](#part-8--recursion-and-backtracking)
9. [Part 9 — Trees](#part-9--trees)
10. [Part 10 — Heaps and Priority Queues](#part-10--heaps-and-priority-queues)
11. [Part 11 — Tries](#part-11--tries)
12. [Part 12 — Graphs](#part-12--graphs)
13. [Part 13 — Greedy Algorithms](#part-13--greedy-algorithms)
14. [Part 14 — Dynamic Programming](#part-14--dynamic-programming)
15. [Part 15 — Bit Manipulation](#part-15--bit-manipulation)
16. [Part 16 — Binary Search](#part-16--binary-search)
17. [Part 17 — Problem-Solving Patterns](#part-17--problem-solving-patterns)
18. [Part 18 — Interview Strategy](#part-18--interview-strategy)
19. [Complexity Cheat Sheet](#complexity-cheat-sheet)
20. [Final Revision Sheet](#final-revision-sheet)

---

# Part 1 — Algorithm Analysis

## Definition

Algorithm analysis is the process of measuring how much **time** and **memory** an algorithm uses as the input size grows. It tells you whether your solution will work on large inputs or will be too slow.

---

## Big O, Big Theta, Big Omega

| Notation | Meaning | Use |
|---|---|---|
| **O(f(n))** | Upper bound — worst case | Most common in interviews |
| **Ω(f(n))** | Lower bound — best case | Least useful for interviews |
| **Θ(f(n))** | Tight bound — exact growth | When best = worst case |

**In interviews, always talk about Big O (worst case).**

### Common Complexities — Ranked Best to Worst

| Complexity | Name | Example |
|---|---|---|
| O(1) | Constant | Array index access, hash lookup |
| O(log n) | Logarithmic | Binary search |
| O(n) | Linear | Single loop over array |
| O(n log n) | Log-linear | Merge sort, heap sort |
| O(n²) | Quadratic | Nested loops, bubble sort |
| O(2ⁿ) | Exponential | Recursive subsets |
| O(n!) | Factorial | Permutations |

```python
# O(1) — constant regardless of input size
def get_first(arr):
    return arr[0]

# O(log n) — input halved each step
def binary_search(arr, target):
    lo, hi = 0, len(arr) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if arr[mid] == target: return mid
        elif arr[mid] < target: lo = mid + 1
        else: hi = mid - 1
    return -1

# O(n) — one pass through input
def find_max(arr):
    return max(arr)

# O(n²) — nested loop
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n - i - 1):
            if arr[j] > arr[j+1]:
                arr[j], arr[j+1] = arr[j+1], arr[j]
```

---

## Simplification Rules

- **Drop constants:** O(2n) → O(n)
- **Drop lower-order terms:** O(n² + n) → O(n²)
- **Different inputs = different variables:** Two arrays of size n and m → O(n + m), not O(n)
- **Nested loops multiply:** loop inside loop → O(n × m)

---

## Space Complexity

Space complexity measures **extra memory** used by the algorithm (not counting input size).

```python
# O(1) space — only a few variables
def sum_array(arr):
    total = 0          # O(1)
    for x in arr:
        total += x
    return total

# O(n) space — creates new array of size n
def double_array(arr):
    return [x * 2 for x in arr]  # new list of size n

# O(n) space — recursion call stack depth n
def factorial(n):
    if n <= 1: return 1
    return n * factorial(n - 1)  # n frames on stack
```

---

## Amortized Analysis

Some operations are occasionally slow but cheap on average. **Dynamic arrays (Python lists)** are the best example.

- Python `list.append()` is usually O(1).
- When the array is full, it doubles in size — O(n) for that one operation.
- But this doubling happens rarely. Averaged over many appends, each append is O(1) **amortized**.

```
Append 1:  [1]           — O(1)
Append 2:  [1,2]         — O(1)
Append 3:  [1,2,3,_]     — O(n): resize, copy 2 elements
Append 4:  [1,2,3,4]     — O(1)
Append 5:  [1,2,3,4,5,_,_,_] — O(n): resize, copy 4 elements

Total cost for n appends ≈ n + (1 + 2 + 4 + ... + n/2) = n + n = 2n = O(n)
Amortized cost per append = O(n) / n = O(1)
```

---

## Recursion Tree Analysis

Used to find the complexity of divide-and-conquer algorithms.

**Example: Merge Sort**
- At each level, the array is split in half → log n levels.
- At each level, n total work is done (merging).
- Total: O(n log n)

```
                  [1,5,3,8,2]        — level 0: 1 call, n work
               /              \
         [1,5,3]            [8,2]    — level 1: 2 calls, n work total
          /    \             / \
       [1,5]  [3]          [8] [2]   — level 2: 4 calls, n work total
       /   \
      [1]  [5]                       — level 3 (log n levels)

Total work = n × log n = O(n log n)
```

### Common Recurrences

| Recurrence | Algorithm | Complexity |
|---|---|---|
| T(n) = T(n/2) + O(1) | Binary search | O(log n) |
| T(n) = 2T(n/2) + O(n) | Merge sort | O(n log n) |
| T(n) = T(n-1) + O(1) | Linear recursion | O(n) |
| T(n) = 2T(n-1) + O(1) | Fibonacci (naive) | O(2ⁿ) |

---

## Common Interview Q&A

**Q: What is the difference between time complexity and running time?**
> Time complexity describes how an algorithm *scales* with input size — it ignores constants and hardware. Running time is the actual seconds it takes on a specific machine. O(n) on a slow machine may be slower than O(n²) on a fast one for small n, but for large n, O(n) always wins.

**Q: Why do we ignore constants in Big O?**
> Because we care about *scalability*. O(1000n) and O(n) both grow linearly — they behave the same as n → ∞. Constants depend on hardware and implementation details, not the algorithm's fundamental efficiency.

**Q: When is O(n log n) better than O(n²)?**
> For any n > ~30, n log n is dramatically smaller. For n = 1,000,000: n log n ≈ 20,000,000 operations vs n² = 1,000,000,000,000. This is why merge sort beats bubble sort on large inputs.

---

## Key LeetCode Problems for Analysis Practice
- Big O of your own solutions — analyze every solution you write.

## Key Takeaways
- Always state the time AND space complexity of your solution in interviews.
- O(n log n) is the best you can achieve for comparison-based sorting.
- Amortized O(1) operations (like list append) are O(1) in interview analysis.

> **Summary:** Algorithm analysis = how time/memory grow with input size. Use Big O for worst case. Know the complexity hierarchy: O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(2ⁿ). Always analyze your interview solutions before submitting.

---

# Part 2 — Arrays

## Definition

An array is a **contiguous block of memory** storing elements of the same type, accessible in O(1) by index. Python's `list` is a dynamic array — it automatically resizes.

## Internal Working

- Python lists store **pointers** to objects (not the objects themselves) in contiguous memory.
- When a list is full and you append, Python allocates a larger block (~1.125× to 2× the old size), copies all pointers, and discards the old block.
- This is why appending is O(1) amortized but O(n) worst case.

## Complexity Table

| Operation | Time | Notes |
|---|---|---|
| Access by index | O(1) | Direct memory address |
| Search (unsorted) | O(n) | Linear scan |
| Search (sorted) | O(log n) | Binary search |
| Insert at end | O(1) amortized | Append |
| Insert at index | O(n) | Shift elements right |
| Delete at end | O(1) | Pop |
| Delete at index | O(n) | Shift elements left |

---

## Key Technique 1: Sliding Window

**Intuition:** Use two pointers to maintain a "window" over the array. Slide the window right instead of recomputing from scratch.

**Fixed-size window (sum of k consecutive elements):**

```python
def max_sum_subarray(arr, k):
    n = len(arr)
    if n < k:
        return -1
    
    # Build initial window
    window_sum = sum(arr[:k])
    max_sum = window_sum
    
    # Slide window: add next, remove first
    for i in range(k, n):
        window_sum += arr[i] - arr[i - k]  # slide
        max_sum = max(max_sum, window_sum)
    
    return max_sum

# Dry run: arr = [2, 1, 5, 1, 3, 2], k = 3
# Initial: sum([2,1,5]) = 8
# i=3: sum = 8 + 1 - 2 = 7
# i=4: sum = 7 + 3 - 1 = 9  ← max
# i=5: sum = 9 + 2 - 5 = 6
# Answer: 9
```

**Variable-size window (longest subarray with sum ≤ target):**

```python
def longest_subarray_sum(arr, target):
    left = 0
    current_sum = 0
    max_len = 0
    
    for right in range(len(arr)):
        current_sum += arr[right]           # expand window
        
        while current_sum > target:         # shrink window
            current_sum -= arr[left]
            left += 1
        
        max_len = max(max_len, right - left + 1)
    
    return max_len
```

**When to use sliding window:** "subarray", "substring", "contiguous", "window of size k" → think sliding window.

---

## Key Technique 2: Prefix Sum

**Intuition:** Precompute cumulative sums so any range sum `arr[i..j]` can be answered in O(1).

```python
def build_prefix(arr):
    prefix = [0] * (len(arr) + 1)
    for i, val in enumerate(arr):
        prefix[i + 1] = prefix[i] + val
    return prefix

def range_sum(prefix, left, right):
    # Sum of arr[left..right] (inclusive)
    return prefix[right + 1] - prefix[left]

# Example: arr = [3, 1, 4, 1, 5]
# prefix =  [0, 3, 4, 8, 9, 14]
# range_sum(prefix, 1, 3) = prefix[4] - prefix[1] = 9 - 3 = 6
# Verify: arr[1]+arr[2]+arr[3] = 1+4+1 = 6 ✓
```

**2D prefix sum (for grid problems):**

```python
def build_2d_prefix(grid):
    rows, cols = len(grid), len(grid[0])
    prefix = [[0] * (cols + 1) for _ in range(rows + 1)]
    for r in range(rows):
        for c in range(cols):
            prefix[r+1][c+1] = (grid[r][c] + prefix[r][c+1]
                                 + prefix[r+1][c] - prefix[r][c])
    return prefix
```

---

## Key Technique 3: Kadane's Algorithm

**Problem:** Find the maximum sum subarray (contiguous).

**Intuition:** At each index, decide: start a new subarray here, or extend the current one?

```python
def max_subarray(nums):
    max_sum = nums[0]
    current_sum = nums[0]
    
    for i in range(1, len(nums)):
        # Either extend current subarray or start fresh
        current_sum = max(nums[i], current_sum + nums[i])
        max_sum = max(max_sum, current_sum)
    
    return max_sum

# Dry run: nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
# i=1: cur=max(1, -2+1)=1,  max=1
# i=2: cur=max(-3,1-3)=-2,  max=1
# i=3: cur=max(4,-2+4)=4,   max=4
# i=4: cur=max(-1,4-1)=3,   max=4
# i=5: cur=max(2,3+2)=5,    max=5
# i=6: cur=max(1,5+1)=6,    max=6
# i=7: cur=max(-5,6-5)=1,   max=6
# i=8: cur=max(4,1+4)=5,    max=6
# Answer: 6 (subarray [4,-1,2,1])
```

---

## Key Technique 4: Two Pointers

**Intuition:** Use two pointers moving toward each other (or in the same direction) to reduce O(n²) to O(n).

```python
# Two Sum in sorted array — O(n)
def two_sum_sorted(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        s = arr[left] + arr[right]
        if s == target:
            return [left, right]
        elif s < target:
            left += 1
        else:
            right -= 1
    return []

# Remove duplicates in-place — O(n)
def remove_duplicates(nums):
    if not nums:
        return 0
    slow = 0
    for fast in range(1, len(nums)):
        if nums[fast] != nums[slow]:
            slow += 1
            nums[slow] = nums[fast]
    return slow + 1
```

---

## Common Mistakes
- Off-by-one errors in window boundaries.
- Forgetting to handle empty arrays.
- Not returning the correct index vs value.
- Using O(n) `list.insert(0, x)` when O(1) `deque.appendleft()` is needed.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Maximum Subarray (Kadane) | #53 | Medium |
| Best Time to Buy and Sell Stock | #121 | Easy |
| Longest Substring Without Repeating Characters | #3 | Medium |
| Container With Most Water | #11 | Medium |
| Product of Array Except Self | #238 | Medium |
| Minimum Size Subarray Sum | #209 | Medium |

## Key Takeaways
- Sliding window converts O(n²) brute force into O(n) for contiguous subarray problems.
- Prefix sum converts O(n) per range query into O(1) with O(n) preprocessing.
- Two pointers work when the array is sorted or when you need pairs/triplets.

> **Summary:** Arrays are O(1) access, O(n) search. The four core techniques — sliding window, prefix sum, Kadane's, two pointers — solve the majority of array interview problems. Recognizing which pattern to apply is the key skill.

---

# Part 3 — Strings

## Definition

A string is an **immutable sequence of characters**. In Python, strings are objects with rich built-in methods. Because they are immutable, every "modification" creates a new string.

## Complexity Table

| Operation | Time | Notes |
|---|---|---|
| Access by index | O(1) | |
| Length | O(1) | Cached |
| Concatenation `+` | O(n) | Creates new string |
| Slicing `s[i:j]` | O(j-i) | Creates new string |
| Search `in` | O(n·m) | n = string, m = pattern |
| Split / Join | O(n) | |
| `''.join(list)` | O(n) | Preferred over repeated `+` |

**Important:** Build strings with `''.join(list)` instead of `result += char` in a loop. The latter is O(n²) due to repeated string creation.

---

## Common String Techniques

### Anagram Check

```python
from collections import Counter

def is_anagram(s, t):
    return Counter(s) == Counter(t)  # O(n)

# Manual approach — same idea
def is_anagram_v2(s, t):
    if len(s) != len(t):
        return False
    count = {}
    for c in s:
        count[c] = count.get(c, 0) + 1
    for c in t:
        count[c] = count.get(c, 0) - 1
        if count[c] < 0:
            return False
    return True
```

### Palindrome Check

```python
def is_palindrome(s):
    left, right = 0, len(s) - 1
    while left < right:
        if s[left] != s[right]:
            return False
        left += 1
        right -= 1
    return True

# Pythonic (but O(n) space)
def is_palindrome_v2(s):
    return s == s[::-1]
```

### Sliding Window on Strings

```python
def longest_substring_no_repeat(s):
    char_index = {}
    left = 0
    max_len = 0
    
    for right, char in enumerate(s):
        if char in char_index and char_index[char] >= left:
            left = char_index[char] + 1  # shrink window past duplicate
        char_index[char] = right
        max_len = max(max_len, right - left + 1)
    
    return max_len

# Dry run: s = "abcabcbb"
# r=0,a: window=[a], len=1
# r=1,b: window=[ab], len=2
# r=2,c: window=[abc], len=3
# r=3,a: a seen at 0, left=1, window=[bca], len=3
# r=4,b: b seen at 1, left=2, window=[cab], len=3
# r=5,c: c seen at 2, left=3, window=[abc], len=3
# Answer: 3
```

### Group Anagrams

```python
from collections import defaultdict

def group_anagrams(strs):
    groups = defaultdict(list)
    for s in strs:
        key = tuple(sorted(s))  # canonical form
        groups[key].append(s)
    return list(groups.values())
```

### String Encoding Trick (Interview Pattern)

```python
# Encode multiple strings into one (separator trick)
def encode(strs):
    return ''.join(f"{len(s)}#{s}" for s in strs)

def decode(s):
    result = []
    i = 0
    while i < len(s):
        j = s.index('#', i)
        length = int(s[i:j])
        result.append(s[j+1:j+1+length])
        i = j + 1 + length
    return result
```

---

## Common Mistakes
- Using `string += char` in a loop (O(n²)) — use `list.append()` then `''.join()`.
- Forgetting strings are immutable — you cannot modify them in place.
- Confusing `s.find()` (returns -1) with `s.index()` (raises exception) on miss.
- Case sensitivity — always clarify: lowercase only? Use `.lower()` to normalize.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Valid Anagram | #242 | Easy |
| Group Anagrams | #49 | Medium |
| Longest Substring Without Repeating Chars | #3 | Medium |
| Valid Palindrome | #125 | Easy |
| Minimum Window Substring | #76 | Hard |
| Longest Repeating Character Replacement | #424 | Medium |

## Key Takeaways
- Always use `''.join(list)` to build strings, never `+=` in a loop.
- Frequency maps (Counter or dict) solve most anagram and character-count problems.
- Sliding window applies to strings exactly as it does to arrays.

> **Summary:** Strings are immutable character sequences. Build with `join`. Use Counter for frequency problems. Sliding window handles most substring problems. Know palindrome and anagram patterns cold.

---

# Part 4 — Hash Tables

## Definition

A hash table maps **keys to values** using a hash function. It provides average O(1) for insertion, deletion, and lookup. Python's `dict` and `set` are hash tables.

## Internal Working

1. A key is passed through a hash function → produces an integer index.
2. The value is stored at that index in an underlying array.
3. **Collision:** Two keys produce the same index.
4. Python uses **open addressing with pseudo-random probing** to handle collisions.

### Collision Handling Methods

| Method | Idea | Pros | Cons |
|---|---|---|---|
| **Chaining** | Each bucket holds a linked list | Simple, handles high load | Extra memory for pointers |
| **Open Addressing** | Probe for next empty slot | Cache-friendly | Clustering, complex deletion |
| **Python's approach** | Open addressing + random probe | Fast in practice | Load factor must stay low |

**Load factor** = number of entries / table size. When it exceeds ~0.7, Python resizes the table (doubles it) to maintain O(1) performance.

## Complexity Table

| Operation | Average | Worst (hash collision) |
|---|---|---|
| Insert | O(1) | O(n) |
| Search | O(1) | O(n) |
| Delete | O(1) | O(n) |
| Iterate | O(n) | O(n) |

---

## Python dict and set

```python
# dict — key-value store
d = {}
d["apple"] = 1        # insert/update: O(1)
val = d.get("apple", 0)  # safe get with default: O(1)
"apple" in d          # key check: O(1)
del d["apple"]        # delete: O(1)

# Counting frequencies — most common pattern
from collections import Counter, defaultdict

freq = Counter("abracadabra")
# Counter({'a': 5, 'b': 2, 'r': 2, 'c': 1, 'd': 1})
freq.most_common(2)   # [('a', 5), ('b', 2)]

# defaultdict avoids KeyError
graph = defaultdict(list)
graph["A"].append("B")  # no need to initialize

# set — unique elements, O(1) lookup
s = {1, 2, 3}
s.add(4)              # O(1)
s.remove(2)           # O(1), raises KeyError if missing
s.discard(99)         # O(1), safe — no error
3 in s                # O(1)
```

---

## Pattern 1: Two Sum (Classic Hash Table Problem)

```python
def two_sum(nums, target):
    seen = {}  # value → index
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    return []

# Dry run: nums = [2,7,11,15], target = 9
# i=0, num=2: complement=7, not in seen. seen={2:0}
# i=1, num=7: complement=2, 2 in seen! return [0, 1] ✓
```

## Pattern 2: Frequency Count

```python
from collections import Counter

def top_k_frequent(nums, k):
    freq = Counter(nums)
    # bucket sort by frequency
    buckets = [[] for _ in range(len(nums) + 1)]
    for num, count in freq.items():
        buckets[count].append(num)
    
    result = []
    for i in range(len(buckets) - 1, 0, -1):
        result.extend(buckets[i])
        if len(result) >= k:
            return result[:k]
```

## Pattern 3: Subarray Sum Equals K (Prefix Sum + Hash)

```python
def subarray_sum(nums, k):
    # prefix_sum → count of times it appeared
    prefix_count = {0: 1}
    prefix_sum = 0
    count = 0
    
    for num in nums:
        prefix_sum += num
        # If (prefix_sum - k) was seen before, there's a subarray summing to k
        count += prefix_count.get(prefix_sum - k, 0)
        prefix_count[prefix_sum] = prefix_count.get(prefix_sum, 0) + 1
    
    return count
```

---

## Common Mistakes
- Using a list where a set would give O(1) lookup.
- Not handling hash collisions manually (Python handles them, but understand the concept).
- Modifying a dict while iterating over it (use `list(d.keys())` or iterate a copy).
- Forgetting that dict keys must be **hashable** — lists cannot be keys, but tuples can.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Two Sum | #1 | Easy |
| Valid Anagram | #242 | Easy |
| Group Anagrams | #49 | Medium |
| Top K Frequent Elements | #347 | Medium |
| Subarray Sum Equals K | #560 | Medium |
| Longest Consecutive Sequence | #128 | Medium |

## Key Takeaways
- Hash tables give O(1) average for insert/lookup/delete — use them to trade space for speed.
- The two-sum pattern (store complement, check on next element) is used in dozens of problems.
- `Counter`, `defaultdict`, and `set` are your go-to Python tools.

> **Summary:** Hash tables = O(1) average lookup. Python `dict` and `set` are hash tables. The core pattern: use a dict to remember what you have seen, then check on each new element. Prefix sum + hash solves many subarray sum problems.

---

# Part 5 — Linked Lists

## Definition

A linked list is a **sequence of nodes** where each node stores a value and a pointer to the next node. Unlike arrays, elements are NOT stored contiguously — they can be anywhere in memory.

## Types

| Type | Structure | Extra pointer |
|---|---|---|
| Singly | Each node → next | None |
| Doubly | Each node → next + prev | `prev` |
| Circular | Last node → head | None (or both ways) |

## Complexity Table

| Operation | Singly LL | Array |
|---|---|---|
| Access by index | O(n) | O(1) |
| Insert at head | O(1) | O(n) |
| Insert at tail (with tail ptr) | O(1) | O(1) amortized |
| Insert at middle | O(n) | O(n) |
| Delete at head | O(1) | O(n) |
| Search | O(n) | O(n) |

---

## Python Implementation

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

# Build a linked list from array
def build_list(arr):
    dummy = ListNode(0)
    cur = dummy
    for val in arr:
        cur.next = ListNode(val)
        cur = cur.next
    return dummy.next

# Convert linked list to array (for debugging)
def to_array(head):
    result = []
    while head:
        result.append(head.val)
        head = head.next
    return result

# Reverse a linked list — O(n), O(1) space
def reverse_list(head):
    prev = None
    cur = head
    while cur:
        next_node = cur.next  # save next
        cur.next = prev       # reverse pointer
        prev = cur            # move prev forward
        cur = next_node       # move cur forward
    return prev  # new head

# Dry run: 1->2->3->None
# prev=None, cur=1: next=2, 1→None, prev=1, cur=2
# prev=1,   cur=2: next=3, 2→1,    prev=2, cur=3
# prev=2,   cur=3: next=None, 3→2, prev=3, cur=None
# return 3 → 3->2->1->None ✓
```

---

## Fast and Slow Pointer (Floyd's Algorithm)

**Intuition:** Two pointers move at different speeds. Fast moves 2 steps, slow moves 1 step.

### Detect Cycle

```python
def has_cycle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```

### Find Middle of Linked List

```python
def find_middle(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow  # slow is at middle when fast reaches end
```

### Find Kth Node from End

```python
def kth_from_end(head, k):
    fast = slow = head
    # Move fast k steps ahead
    for _ in range(k):
        fast = fast.next
    # Move both until fast reaches end
    while fast:
        slow = slow.next
        fast = fast.next
    return slow
```

### Merge Two Sorted Lists

```python
def merge_sorted(l1, l2):
    dummy = ListNode(0)
    cur = dummy
    while l1 and l2:
        if l1.val <= l2.val:
            cur.next = l1
            l1 = l1.next
        else:
            cur.next = l2
            l2 = l2.next
        cur = cur.next
    cur.next = l1 or l2
    return dummy.next
```

---

## Common Mistakes
- Not using a dummy head node — makes edge cases (empty list, single node) cleaner.
- Losing `next` reference before updating it in reversal.
- Off-by-one in the kth-from-end pattern — test with k=1 and k=n.
- Not checking `fast and fast.next` before accessing `fast.next.next`.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Reverse Linked List | #206 | Easy |
| Merge Two Sorted Lists | #21 | Easy |
| Linked List Cycle | #141 | Easy |
| Find Middle of Linked List | #876 | Easy |
| Remove Nth Node From End | #19 | Medium |
| Reorder List | #143 | Medium |

## Key Takeaways
- Use a `dummy` head node to simplify edge cases — `dummy.next` is always the result head.
- Fast/slow pointer detects cycles, finds midpoints, and finds kth from end.
- Drawing the pointer states step by step always prevents bugs.

> **Summary:** Linked lists are O(n) access but O(1) head insertion. The dummy node trick eliminates edge cases. Fast/slow pointers solve cycle detection, middle, and kth-from-end in one pass. Reverse and merge are the two most practiced operations.

---

# Part 6 — Stacks

## Definition

A stack is a **Last-In, First-Out (LIFO)** data structure. The last element added is the first one removed — like a stack of plates.

## Python Implementation

```python
# Python list used as stack — O(1) push/pop from end
stack = []
stack.append(5)   # push: O(1)
stack.append(3)
top = stack[-1]   # peek: O(1)
val = stack.pop() # pop: O(1)
is_empty = len(stack) == 0
```

## Complexity Table

| Operation | Time |
|---|---|
| Push | O(1) |
| Pop | O(1) |
| Peek | O(1) |
| Search | O(n) |

---

## Pattern 1: Balanced Brackets

```python
def is_valid_brackets(s):
    stack = []
    mapping = {')': '(', ']': '[', '}': '{'}
    
    for char in s:
        if char in mapping:                   # closing bracket
            top = stack.pop() if stack else '#'
            if mapping[char] != top:
                return False
        else:
            stack.append(char)               # opening bracket
    
    return len(stack) == 0  # stack must be empty at end

# Dry run: s = "()[]{}"
# '(' → push. stack=['(']
# ')' → mapping[')']='(', pop '(', match. stack=[]
# '[' → push. stack=['[']
# ']' → mapping[']']='[', pop '[', match. stack=[]
# '{' → push. stack=['{']
# '}' → mapping['}]='{', pop '{', match. stack=[]
# Empty → True ✓
```

---

## Pattern 2: Monotonic Stack

**Intuition:** A monotonic stack maintains elements in increasing or decreasing order. When you push a new element, pop all elements that violate the order. Used for "next greater/smaller element" problems.

```python
# Next Greater Element to the right
def next_greater_element(nums):
    n = len(nums)
    result = [-1] * n
    stack = []  # stores indices; stack maintains decreasing values
    
    for i in range(n):
        # Pop indices whose element is smaller than current
        while stack and nums[stack[-1]] < nums[i]:
            idx = stack.pop()
            result[idx] = nums[i]  # nums[i] is the next greater for idx
        stack.append(i)
    
    return result

# Dry run: nums = [2, 1, 2, 4, 3]
# i=0, val=2: stack=[], push 0. stack=[0]
# i=1, val=1: nums[0]=2 > 1, no pop. push 1. stack=[0,1]
# i=2, val=2: nums[1]=1 < 2, pop 1 → result[1]=2. nums[0]=2 = 2, no pop. push 2. stack=[0,2]
# i=3, val=4: nums[2]=2 < 4, pop 2 → result[2]=4. nums[0]=2 < 4, pop 0 → result[0]=4. push 3. stack=[3]
# i=4, val=3: nums[3]=4 > 3, no pop. push 4. stack=[3,4]
# Remaining: result[3]=result[4]=-1
# Result: [4, 2, 4, -1, -1] ✓
```

```python
# Daily Temperatures — find days until warmer temperature
def daily_temperatures(temperatures):
    n = len(temperatures)
    result = [0] * n
    stack = []  # indices of days waiting for warmer day
    
    for i, temp in enumerate(temperatures):
        while stack and temperatures[stack[-1]] < temp:
            idx = stack.pop()
            result[idx] = i - idx
        stack.append(i)
    
    return result
```

---

## Pattern 3: Expression Evaluation

```python
def evaluate_rpn(tokens):
    """Evaluate Reverse Polish Notation: ["2","1","+","3","*"] = 9"""
    stack = []
    ops = {'+', '-', '*', '/'}
    
    for token in tokens:
        if token in ops:
            b, a = stack.pop(), stack.pop()
            if token == '+': stack.append(a + b)
            elif token == '-': stack.append(a - b)
            elif token == '*': stack.append(a * b)
            else: stack.append(int(a / b))  # truncate toward zero
        else:
            stack.append(int(token))
    
    return stack[0]
```

---

## Common Mistakes
- Popping from an empty stack — always check `if stack` first.
- Confusing what "monotonic increasing" means — pop when current > top (keep smaller on stack).

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Valid Parentheses | #20 | Easy |
| Min Stack | #155 | Medium |
| Daily Temperatures | #739 | Medium |
| Largest Rectangle in Histogram | #84 | Hard |
| Evaluate Reverse Polish Notation | #150 | Medium |

## Key Takeaways
- Stacks are LIFO — use Python list with `.append()` and `.pop()`.
- Monotonic stack solves "next greater/smaller" problems in O(n).
- Balanced brackets is the classic stack interview problem — know it perfectly.

> **Summary:** Stacks are LIFO. Python list works perfectly as a stack. Monotonic stack is the key advanced pattern — it solves next greater element, daily temperatures, and histogram problems in O(n). Always check for empty stack before popping.

---

# Part 7 — Queues

## Definition

A queue is a **First-In, First-Out (FIFO)** data structure. The first element added is the first one removed — like a line at a store.

## Python Implementations

```python
from collections import deque
import heapq

# Standard queue using deque — O(1) append and popleft
queue = deque()
queue.append(1)       # enqueue: O(1)
queue.append(2)
front = queue[0]      # peek: O(1)
val = queue.popleft() # dequeue: O(1)

# deque as double-ended queue
dq = deque()
dq.appendleft(0)      # add to front: O(1)
dq.append(5)          # add to back: O(1)
dq.popleft()          # remove from front: O(1)
dq.pop()              # remove from back: O(1)
```

---

## Circular Queue (Interview Implementation)

```python
class CircularQueue:
    def __init__(self, k):
        self.size = k
        self.queue = [0] * k
        self.head = 0
        self.tail = -1
        self.count = 0
    
    def enqueue(self, val):
        if self.is_full():
            return False
        self.tail = (self.tail + 1) % self.size
        self.queue[self.tail] = val
        self.count += 1
        return True
    
    def dequeue(self):
        if self.is_empty():
            return False
        self.head = (self.head + 1) % self.size
        self.count -= 1
        return True
    
    def front(self):
        return self.queue[self.head] if not self.is_empty() else -1
    
    def rear(self):
        return self.queue[self.tail] if not self.is_empty() else -1
    
    def is_empty(self): return self.count == 0
    def is_full(self):  return self.count == self.size
```

---

## Priority Queue (Min Heap)

```python
import heapq

# Min heap — smallest element at top
pq = []
heapq.heappush(pq, 3)
heapq.heappush(pq, 1)
heapq.heappush(pq, 4)
smallest = heapq.heappop(pq)  # returns 1

# Max heap — negate values for max behavior
max_pq = []
heapq.heappush(max_pq, -3)
heapq.heappush(max_pq, -1)
largest = -heapq.heappop(max_pq)  # returns 3

# heapify — convert list to heap in O(n)
arr = [3, 1, 4, 1, 5, 9]
heapq.heapify(arr)

# nlargest / nsmallest
import heapq
heapq.nlargest(3, arr)   # [9, 5, 4]
heapq.nsmallest(3, arr)  # [1, 1, 3]
```

---

## Sliding Window Maximum (Deque Monotonic Queue)

```python
from collections import deque

def sliding_window_max(nums, k):
    """Maximum in each window of size k — O(n)"""
    dq = deque()  # stores indices; front = max of current window
    result = []
    
    for i, num in enumerate(nums):
        # Remove indices outside the window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        
        # Remove indices with smaller values (they'll never be the max)
        while dq and nums[dq[-1]] < num:
            dq.pop()
        
        dq.append(i)
        
        if i >= k - 1:  # window is full
            result.append(nums[dq[0]])
    
    return result
```

---

## Common Mistakes
- Using `list.pop(0)` for a queue — O(n). Use `deque.popleft()` — O(1).
- Forgetting Python's `heapq` is a min heap — negate for max heap.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Implement Queue Using Stacks | #232 | Easy |
| Design Circular Queue | #622 | Medium |
| Sliding Window Maximum | #239 | Hard |
| Kth Largest Element in a Stream | #703 | Easy |
| Task Scheduler | #621 | Medium |

## Key Takeaways
- Use `collections.deque` for queues, not `list` — O(1) on both ends.
- Python's `heapq` module provides a min heap. Negate values for max heap.
- Monotonic deque solves sliding window maximum in O(n).

> **Summary:** Queues are FIFO. Use `deque` for O(1) operations on both ends. `heapq` gives you a priority queue (min heap). The monotonic deque is an advanced pattern for sliding window maximum problems.

---

# Part 8 — Recursion and Backtracking

## Definition

**Recursion** is when a function calls itself to solve a smaller version of the same problem. **Backtracking** is a recursive technique that builds a solution incrementally and abandons ("backtracks") paths that violate constraints.

## Recursive Thinking Framework

Every recursive solution needs:
1. **Base case** — when to stop.
2. **Recursive case** — how to reduce the problem.
3. **Combine** — how to build the answer from subproblem results.

```python
# Template
def solve(problem):
    if base_case(problem):          # 1. Stop condition
        return base_result
    
    smaller = reduce(problem)       # 2. Shrink problem
    sub_result = solve(smaller)     # 3. Recurse
    return combine(sub_result)      # 4. Combine
```

---

## Classic Recursion Examples

```python
# Fibonacci — O(2^n) naive, O(n) with memoization
def fib(n, memo={}):
    if n <= 1: return n
    if n in memo: return memo[n]
    memo[n] = fib(n-1, memo) + fib(n-2, memo)
    return memo[n]

# Power — O(log n) using divide and conquer
def power(base, exp):
    if exp == 0: return 1
    if exp % 2 == 0:
        half = power(base, exp // 2)
        return half * half
    return base * power(base, exp - 1)

# Binary search — recursive
def binary_search_rec(arr, target, lo, hi):
    if lo > hi: return -1
    mid = (lo + hi) // 2
    if arr[mid] == target: return mid
    if arr[mid] < target: return binary_search_rec(arr, target, mid+1, hi)
    return binary_search_rec(arr, target, lo, mid-1)
```

---

## Backtracking Template

```python
def backtrack(path, choices):
    if is_solution(path):       # base case: valid complete solution
        result.append(path[:]) # add a COPY
        return
    
    for choice in choices:
        if is_valid(choice, path):   # constraint check
            path.append(choice)      # choose
            backtrack(path, choices) # explore
            path.pop()               # unchoose (backtrack)
```

---

## Pattern 1: Generate All Subsets

```python
def subsets(nums):
    result = []
    
    def backtrack(start, path):
        result.append(path[:])   # every path is a valid subset
        for i in range(start, len(nums)):
            path.append(nums[i])
            backtrack(i + 1, path)
            path.pop()
    
    backtrack(0, [])
    return result

# Dry run: nums = [1,2,3]
# backtrack(0,[])  → add []
#   add 1: backtrack(1,[1])  → add [1]
#     add 2: backtrack(2,[1,2]) → add [1,2]
#       add 3: backtrack(3,[1,2,3]) → add [1,2,3]
#     add 3: backtrack(3,[1,3]) → add [1,3]
#   add 2: backtrack(2,[2]) → add [2]
#     add 3: backtrack(3,[2,3]) → add [2,3]
#   add 3: backtrack(3,[3]) → add [3]
# Result: [[], [1], [1,2], [1,2,3], [1,3], [2], [2,3], [3]]
```

---

## Pattern 2: Generate All Permutations

```python
def permutations(nums):
    result = []
    
    def backtrack(path, used):
        if len(path) == len(nums):
            result.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]: continue
            used[i] = True
            path.append(nums[i])
            backtrack(path, used)
            path.pop()
            used[i] = False
    
    backtrack([], [False] * len(nums))
    return result
```

---

## Pattern 3: N-Queens

```python
def solve_n_queens(n):
    result = []
    cols = set()
    diag1 = set()  # row - col (same for left diagonal)
    diag2 = set()  # row + col (same for right diagonal)
    
    board = [['.' for _ in range(n)] for _ in range(n)]
    
    def backtrack(row):
        if row == n:
            result.append([''.join(r) for r in board])
            return
        for col in range(n):
            if col in cols or (row-col) in diag1 or (row+col) in diag2:
                continue
            cols.add(col); diag1.add(row-col); diag2.add(row+col)
            board[row][col] = 'Q'
            backtrack(row + 1)
            board[row][col] = '.'
            cols.remove(col); diag1.remove(row-col); diag2.remove(row+col)
    
    backtrack(0)
    return result
```

---

## Common Mistakes
- Forgetting to add a **copy** of the path (`path[:]`) — lists are mutable references.
- No proper base case — causes infinite recursion / stack overflow.
- Not undoing the choice before backtracking — the "undo" step is as important as the "do" step.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Subsets | #78 | Medium |
| Subsets II (with duplicates) | #90 | Medium |
| Permutations | #46 | Medium |
| Combination Sum | #39 | Medium |
| N-Queens | #51 | Hard |
| Word Search | #79 | Medium |

## Key Takeaways
- Every backtracking problem: choose → explore → unchoose.
- Always append a copy of the path, not the path itself.
- State space tree visualizes all choices — draw it to understand recursion.

> **Summary:** Recursion = solve smaller subproblem + combine. Backtracking = recursion + undo. The template is: choose, recurse, unchoose. The four canonical problems are: subsets, permutations, combinations, N-queens.

---

# Part 9 — Trees

## Definition

A tree is a **hierarchical data structure** with a root node, where each node has zero or more children. A **binary tree** is a tree where each node has at most two children (left and right).

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

## Binary Tree vs Binary Search Tree

| Property | Binary Tree | Binary Search Tree (BST) |
|---|---|---|
| Structure | Each node has ≤ 2 children | Same |
| Ordering | No constraint | left < root < right (all subtrees) |
| Search | O(n) | O(h) — O(log n) balanced, O(n) worst |
| Insert | O(n) | O(h) |
| Inorder traversal | No order | **Sorted order** |

---

## Tree Traversals

```python
# DFS Traversals — O(n) time, O(h) space (h = height)

def inorder(root):          # Left → Root → Right
    if not root: return []
    return inorder(root.left) + [root.val] + inorder(root.right)

def preorder(root):         # Root → Left → Right
    if not root: return []
    return [root.val] + preorder(root.left) + preorder(root.right)

def postorder(root):        # Left → Right → Root
    if not root: return []
    return postorder(root.left) + postorder(root.right) + [root.val]

# Iterative inorder (common in interviews)
def inorder_iterative(root):
    result, stack = [], []
    cur = root
    while cur or stack:
        while cur:                # go left as far as possible
            stack.append(cur)
            cur = cur.left
        cur = stack.pop()         # process node
        result.append(cur.val)
        cur = cur.right           # move to right subtree
    return result

# BFS — Level Order Traversal
from collections import deque

def level_order(root):
    if not root: return []
    result = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):    # process one level at a time
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result
```

---

## BST Operations

```python
def search_bst(root, target):
    if not root: return None
    if root.val == target: return root
    if target < root.val: return search_bst(root.left, target)
    return search_bst(root.right, target)

def insert_bst(root, val):
    if not root: return TreeNode(val)
    if val < root.val: root.left  = insert_bst(root.left,  val)
    else:              root.right = insert_bst(root.right, val)
    return root

def validate_bst(root, lo=float('-inf'), hi=float('inf')):
    if not root: return True
    if not (lo < root.val < hi): return False
    return (validate_bst(root.left,  lo, root.val) and
            validate_bst(root.right, root.val, hi))
```

---

## Common Tree Problems

```python
# Height of binary tree
def height(root):
    if not root: return 0
    return 1 + max(height(root.left), height(root.right))

# Diameter of binary tree (longest path between any two nodes)
def diameter(root):
    max_diameter = [0]
    
    def depth(node):
        if not node: return 0
        left  = depth(node.left)
        right = depth(node.right)
        max_diameter[0] = max(max_diameter[0], left + right)
        return 1 + max(left, right)
    
    depth(root)
    return max_diameter[0]

# Lowest Common Ancestor (LCA)
def lca(root, p, q):
    if not root or root == p or root == q:
        return root
    left  = lca(root.left,  p, q)
    right = lca(root.right, p, q)
    if left and right: return root  # p and q in different subtrees
    return left or right            # both in same subtree

# Maximum path sum (any path, not just root-to-leaf)
def max_path_sum(root):
    max_sum = [float('-inf')]
    
    def gain(node):
        if not node: return 0
        left  = max(gain(node.left),  0)  # ignore negative paths
        right = max(gain(node.right), 0)
        max_sum[0] = max(max_sum[0], node.val + left + right)
        return node.val + max(left, right)
    
    gain(root)
    return max_sum[0]
```

---

## Balanced Trees (Concept)

A **balanced BST** keeps height O(log n) to guarantee O(log n) search/insert.

| Type | Balance condition | Notes |
|---|---|---|
| **AVL Tree** | |height(left) - height(right)| ≤ 1 | Strict balance, more rotations |
| **Red-Black Tree** | No path > 2× any other | Python's `sortedcontainers.SortedList` |
| **B-Tree** | Used in databases | Multi-level, disk-friendly |

For interviews: understand that Python's `heapq` and Java's `TreeMap` use balanced structures. You rarely implement AVL/RB from scratch — but know WHY they exist.

---

## Common Mistakes
- Not handling `None` nodes — always check `if not root` first.
- Confusing BFS (level-order, uses queue) with DFS (uses stack/recursion).
- In diameter: the longest path does NOT have to pass through the root.
- Validating BST by only checking immediate children — must pass `lo/hi` bounds through recursion.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Invert Binary Tree | #226 | Easy |
| Maximum Depth of Binary Tree | #104 | Easy |
| Diameter of Binary Tree | #543 | Easy |
| Lowest Common Ancestor of BST | #235 | Medium |
| Binary Tree Level Order Traversal | #102 | Medium |
| Validate Binary Search Tree | #98 | Medium |
| Binary Tree Maximum Path Sum | #124 | Hard |

## Key Takeaways
- Know all four traversals (inorder, preorder, postorder, BFS) cold — both recursive and iterative.
- BST inorder traversal gives sorted order — use this property constantly.
- Most tree problems are solved with a recursive DFS helper that returns information from children to the parent.

> **Summary:** Trees are hierarchical. Binary trees have ≤ 2 children. BST adds ordering: left < root < right. Know inorder (sorted), preorder, postorder, and BFS. Most hard tree problems use a recursive DFS that threads state through return values. Validate BST with min/max bounds.


# Part 10 — Heaps and Priority Queues

## Definition

A heap is a **complete binary tree** stored as an array that satisfies the heap property:
- **Min heap:** Every parent is ≤ its children. Root = minimum.
- **Max heap:** Every parent is ≥ its children. Root = maximum.

Python's `heapq` module implements a **min heap**.

## Internal Working

```
Min heap array: [1, 3, 6, 5, 9, 8]
Tree structure:
        1
       / \
      3   6
     / \ /
    5  9 8

For index i:
  parent      = (i - 1) // 2
  left child  = 2*i + 1
  right child = 2*i + 2
```

## Complexity Table

| Operation | Time |
|---|---|
| Insert (heappush) | O(log n) |
| Get min/max | O(1) |
| Delete min/max (heappop) | O(log n) |
| Build heap from array (heapify) | O(n) |
| Search | O(n) |

---

## Python heapq Operations

```python
import heapq

# Min heap
heap = []
heapq.heappush(heap, 5)
heapq.heappush(heap, 1)
heapq.heappush(heap, 3)
print(heap[0])              # peek min: 1
print(heapq.heappop(heap))  # pop min: 1

# heapify — O(n) build from existing list
arr = [5, 3, 8, 1, 4]
heapq.heapify(arr)          # in-place, O(n)

# Max heap — negate values
max_heap = []
heapq.heappush(max_heap, -5)
heapq.heappush(max_heap, -1)
max_val = -heapq.heappop(max_heap)  # 5

# Push and pop in one operation
heapq.heappushpop(heap, 2)   # push 2 then pop min
heapq.heapreplace(heap, 2)   # pop min then push 2 (faster if heap non-empty)

# Heap with tuples — sorted by first element
tasks = []
heapq.heappush(tasks, (1, "low priority task"))
heapq.heappush(tasks, (0, "high priority task"))
priority, task = heapq.heappop(tasks)  # (0, "high priority task")
```

---

## Pattern 1: Kth Largest Element

```python
import heapq

def find_kth_largest(nums, k):
    # Min heap of size k — kth largest is the root
    heap = nums[:k]
    heapq.heapify(heap)  # O(k)
    
    for num in nums[k:]:
        if num > heap[0]:          # larger than current kth largest
            heapq.heapreplace(heap, num)  # O(log k)
    
    return heap[0]

# Why min heap of size k?
# It keeps the k largest elements seen so far.
# The smallest of those k is the kth largest — that's heap[0].
```

---

## Pattern 2: Top K Frequent Elements

```python
from collections import Counter
import heapq

def top_k_frequent(nums, k):
    freq = Counter(nums)
    # Push (frequency, element) tuples — heapq uses min heap on first element
    return heapq.nlargest(k, freq.keys(), key=freq.get)
```

---

## Pattern 3: Merge K Sorted Lists

```python
import heapq

def merge_k_sorted(lists):
    """Merge k sorted linked lists into one sorted list"""
    heap = []
    # Push (value, list_index, node) for each list's head
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))
    
    dummy = ListNode(0)
    cur = dummy
    
    while heap:
        val, i, node = heapq.heappop(heap)
        cur.next = node
        cur = cur.next
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    
    return dummy.next
# Time: O(N log k) where N = total nodes, k = number of lists
```

---

## Pattern 4: Median of Data Stream

```python
import heapq

class MedianFinder:
    """Two heaps: max heap for lower half, min heap for upper half"""
    def __init__(self):
        self.low  = []  # max heap (negate) — lower half
        self.high = []  # min heap — upper half
    
    def add_num(self, num):
        heapq.heappush(self.low, -num)          # push to low
        # Balance: smallest of high >= largest of low
        if self.high and -self.low[0] > self.high[0]:
            heapq.heappush(self.high, -heapq.heappop(self.low))
        # Balance sizes: low can have one more than high
        if len(self.low) > len(self.high) + 1:
            heapq.heappush(self.high, -heapq.heappop(self.low))
        elif len(self.high) > len(self.low):
            heapq.heappush(self.low, -heapq.heappop(self.high))
    
    def find_median(self):
        if len(self.low) > len(self.high):
            return -self.low[0]
        return (-self.low[0] + self.high[0]) / 2
```

---

## Common Mistakes
- Using `heap[0]` to peek — correct for min heap, but remember it is the MINIMUM, not maximum.
- Not negating for max heap.
- Using `heap.sort()` thinking it maintains heap property — use `heapq` functions only.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Kth Largest Element in an Array | #215 | Medium |
| Top K Frequent Elements | #347 | Medium |
| Merge K Sorted Lists | #23 | Hard |
| Find Median from Data Stream | #295 | Hard |
| Task Scheduler | #621 | Medium |
| K Closest Points to Origin | #973 | Medium |

## Key Takeaways
- Python `heapq` = min heap. Negate values for max heap. Use tuples for compound keys.
- "Kth largest" → min heap of size k. The root is the answer.
- Two heaps (max + min) solve the "median of a stream" problem.

> **Summary:** A heap is a complete binary tree guaranteeing O(1) min/max access and O(log n) insert/delete. Python's `heapq` is a min heap. The kth-largest pattern (min heap of size k) and the two-heap pattern (median stream) are the two most important heap interview patterns.

---

# Part 11 — Tries

## Definition

A **Trie** (prefix tree) is a tree data structure used to store strings where each node represents one character. All strings with a common prefix share the same path from the root.

```
Inserting: "cat", "car", "card", "care", "bat"

         root
        /    \
       c      b
       |      |
       a      a
      / \     |
     t   r    t
         |
         d (end) / e (end)
```

## Complexity Table

| Operation | Time | Space |
|---|---|---|
| Insert | O(m) | O(m) per word |
| Search | O(m) | O(1) |
| Starts With | O(m) | O(1) |
| Delete | O(m) | O(1) |

*m = length of the word*

---

## Implementation

```python
class TrieNode:
    def __init__(self):
        self.children = {}   # char → TrieNode
        self.is_end = False  # marks end of a complete word

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end = True
    
    def search(self, word):
        """Returns True if word exists exactly in trie"""
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end
    
    def starts_with(self, prefix):
        """Returns True if any word starts with prefix"""
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
    
    def search_with_wildcard(self, word):
        """'.' matches any single character"""
        def dfs(node, i):
            if i == len(word):
                return node.is_end
            char = word[i]
            if char == '.':
                return any(dfs(child, i+1) for child in node.children.values())
            if char not in node.children:
                return False
            return dfs(node.children[char], i+1)
        return dfs(self.root, 0)

# Usage
trie = Trie()
for word in ["apple", "app", "apply", "bat"]:
    trie.insert(word)

trie.search("app")       # True
trie.search("ap")        # False (not a complete word)
trie.starts_with("app")  # True
trie.starts_with("xyz")  # False
```

---

## Word Search II (Trie + DFS on Grid)

```python
def find_words(board, words):
    """Find all words from 'words' list that exist in the board"""
    trie = Trie()
    for word in words:
        trie.insert(word)
    
    rows, cols = len(board), len(board[0])
    result = set()
    
    def dfs(node, r, c, path):
        if node.is_end:
            result.add(path)
        if r < 0 or r >= rows or c < 0 or c >= cols:
            return
        char = board[r][c]
        if char not in node.children:
            return
        board[r][c] = '#'   # mark visited
        next_node = node.children[char]
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            dfs(next_node, r+dr, c+dc, path+char)
        board[r][c] = char  # restore
    
    for r in range(rows):
        for c in range(cols):
            dfs(trie.root, r, c, "")
    
    return list(result)
```

---

## When to Use a Trie

- Autocomplete / prefix search.
- Word validation against a dictionary.
- Searching for multiple words simultaneously in a grid.
- IP routing (prefix matching).

---

## Common Mistakes
- Forgetting to set `is_end = True` on the last character of an inserted word.
- Confusing `search` (exact match) with `starts_with` (prefix).
- Not marking cells as visited in grid DFS, causing infinite loops.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Implement Trie | #208 | Medium |
| Design Add and Search Words | #211 | Medium |
| Word Search II | #212 | Hard |
| Replace Words | #648 | Medium |
| Longest Word in Dictionary | #720 | Medium |

## Key Takeaways
- Trie = prefix tree. O(m) insert and search, where m = word length.
- Each node has a `children` dict and an `is_end` flag.
- Tries outperform hash sets when prefix-matching or autocomplete is needed.

> **Summary:** Tries store strings character by character, sharing prefixes. Operations are O(m) for word length m. The two key operations: exact search (check `is_end`) and prefix search (traverse without checking `is_end`). Combine with DFS for word-search-in-grid problems.


# Part 12 — Graphs

## Definition

A graph is a collection of **nodes (vertices)** connected by **edges**. Graphs model relationships: social networks, maps, dependencies, networks.

## Key Terminology

| Term | Meaning |
|---|---|
| Directed | Edges have direction (A → B, not B → A) |
| Undirected | Edges go both ways |
| Weighted | Edges have costs/distances |
| Cyclic | Contains cycles |
| DAG | Directed Acyclic Graph — no cycles |
| Connected | Every node reachable from every other |

## Representation

```python
# Adjacency List — O(V + E) space — preferred for sparse graphs
graph = {
    0: [1, 2],
    1: [0, 3],
    2: [0, 4],
    3: [1],
    4: [2]
}

# Build from edge list
from collections import defaultdict
def build_graph(n, edges, directed=False):
    graph = defaultdict(list)
    for u, v in edges:
        graph[u].append(v)
        if not directed:
            graph[v].append(u)
    return graph

# Adjacency Matrix — O(V²) space — for dense graphs or weighted edges
n = 5
matrix = [[0] * n for _ in range(n)]
matrix[0][1] = 1  # edge from 0 to 1
```

---

## BFS — Breadth-First Search

**Use for:** Shortest path in unweighted graph, level-by-level traversal.

```python
from collections import deque

def bfs(graph, start):
    visited = set([start])
    queue   = deque([start])
    order   = []
    
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return order

# Shortest path (unweighted)
def bfs_shortest_path(graph, start, end):
    if start == end: return 0
    visited = {start}
    queue   = deque([(start, 0)])  # (node, distance)
    
    while queue:
        node, dist = queue.popleft()
        for neighbor in graph[node]:
            if neighbor == end: return dist + 1
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
    return -1  # not reachable
```

---

## DFS — Depth-First Search

**Use for:** Cycle detection, connected components, topological sort, path finding.

```python
def dfs_iterative(graph, start):
    visited = set()
    stack   = [start]
    order   = []
    
    while stack:
        node = stack.pop()
        if node in visited: continue
        visited.add(node)
        order.append(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                stack.append(neighbor)
    return order

def dfs_recursive(graph, node, visited=None):
    if visited is None: visited = set()
    visited.add(node)
    for neighbor in graph[node]:
        if neighbor not in visited:
            dfs_recursive(graph, neighbor, visited)
    return visited

# Count connected components
def count_components(n, edges):
    graph = build_graph(n, edges)
    visited = set()
    count = 0
    
    def dfs(node):
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)
    
    for node in range(n):
        if node not in visited:
            dfs(node)
            count += 1
    return count
```

---

## Topological Sort

**Use for:** Dependency ordering, course scheduling, build systems. Only valid for DAGs.

```python
# Kahn's Algorithm (BFS-based) — also detects cycles
from collections import deque

def topo_sort_kahn(n, prerequisites):
    graph  = defaultdict(list)
    in_deg = [0] * n
    
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_deg[course] += 1
    
    queue = deque([i for i in range(n) if in_deg[i] == 0])
    order = []
    
    while queue:
        node = queue.popleft()
        order.append(node)
        for neighbor in graph[node]:
            in_deg[neighbor] -= 1
            if in_deg[neighbor] == 0:
                queue.append(neighbor)
    
    # If order has all n nodes, no cycle exists
    return order if len(order) == n else []

# DFS-based topological sort
def topo_sort_dfs(n, edges):
    graph = build_graph(n, edges, directed=True)
    visited = set()
    result  = []
    
    def dfs(node):
        visited.add(node)
        for neighbor in graph[node]:
            if neighbor not in visited:
                dfs(neighbor)
        result.append(node)  # append AFTER visiting all children
    
    for node in range(n):
        if node not in visited:
            dfs(node)
    
    return result[::-1]  # reverse for topological order
```

---

## Union Find (Disjoint Set Union)

**Use for:** Detecting cycles in undirected graphs, grouping connected components, Kruskal's MST.

```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))  # each node is its own parent
        self.rank   = [0] * n         # used for balancing
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # path compression
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py: return False  # already connected — cycle!
        # Union by rank
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True
    
    def connected(self, x, y):
        return self.find(x) == self.find(y)

# Detect cycle in undirected graph
def has_cycle_undirected(n, edges):
    uf = UnionFind(n)
    for u, v in edges:
        if not uf.union(u, v):  # already in same set → cycle
            return True
    return False
```

---

## Dijkstra's Algorithm (Shortest Path — Weighted, Non-negative)

```python
import heapq

def dijkstra(graph, start):
    """graph: {node: [(neighbor, weight), ...]}"""
    dist = {node: float('inf') for node in graph}
    dist[start] = 0
    heap = [(0, start)]  # (distance, node)
    
    while heap:
        d, node = heapq.heappop(heap)
        if d > dist[node]: continue  # stale entry
        
        for neighbor, weight in graph[node]:
            new_dist = dist[node] + weight
            if new_dist < dist[neighbor]:
                dist[neighbor] = new_dist
                heapq.heappush(heap, (new_dist, neighbor))
    
    return dist

# Time: O((V + E) log V)
```

---

## Bellman-Ford (Shortest Path — Handles Negative Weights)

```python
def bellman_ford(n, edges, start):
    """edges: [(u, v, weight), ...]"""
    dist = [float('inf')] * n
    dist[start] = 0
    
    # Relax all edges V-1 times
    for _ in range(n - 1):
        for u, v, w in edges:
            if dist[u] != float('inf') and dist[u] + w < dist[v]:
                dist[v] = dist[u] + w
    
    # Detect negative cycle — if we can still relax, cycle exists
    for u, v, w in edges:
        if dist[u] != float('inf') and dist[u] + w < dist[v]:
            return None  # negative cycle detected
    
    return dist
# Time: O(V * E)
```

---

## Complexity Summary for Graphs

| Algorithm | Time | Space | Use case |
|---|---|---|---|
| BFS | O(V + E) | O(V) | Shortest path (unweighted) |
| DFS | O(V + E) | O(V) | Cycles, components, topo sort |
| Topological Sort | O(V + E) | O(V) | Dependency ordering (DAG) |
| Union Find | O(α(n)) ≈ O(1) | O(V) | Cycle detection, grouping |
| Dijkstra | O((V+E) log V) | O(V) | Shortest path (non-negative weights) |
| Bellman-Ford | O(V × E) | O(V) | Shortest path (negative weights) |

---

## Common Mistakes
- Not marking nodes as visited — causes infinite loops in cyclic graphs.
- Using Dijkstra with negative weights — it produces wrong results; use Bellman-Ford.
- Confusing directed and undirected graph representation.
- Topological sort on a graph with cycles — always check if all nodes are in result.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Number of Islands | #200 | Medium |
| Clone Graph | #133 | Medium |
| Course Schedule | #207 | Medium |
| Course Schedule II | #210 | Medium |
| Number of Connected Components | #323 | Medium |
| Network Delay Time (Dijkstra) | #743 | Medium |
| Cheapest Flights Within K Stops | #787 | Medium |

## Key Takeaways
- BFS = shortest path (unweighted). DFS = everything else (cycles, components, paths).
- Topological sort (Kahn's) = BFS on nodes with in-degree 0. If result length < n, there is a cycle.
- Union Find with path compression is near O(1) per operation — use it for cycle detection and grouping.

> **Summary:** Graphs model relationships. BFS for shortest unweighted paths. DFS for cycles, components, and ordering. Kahn's algorithm for topological sort. Union Find for connected components and cycle detection. Dijkstra for weighted shortest paths. Always use an adjacency list for sparse graphs.


# Part 13 — Greedy Algorithms

## Definition

A greedy algorithm makes the **locally optimal choice at each step**, hoping to reach a globally optimal solution. It never reconsiders past decisions.

## When Greedy Works

A problem is suitable for greedy if it has:
1. **Greedy choice property:** A locally optimal choice leads to a globally optimal solution.
2. **Optimal substructure:** An optimal solution contains optimal solutions to its subproblems.

**Greedy vs DP:** Greedy makes one choice and never backtracks. DP explores all choices and remembers results. When greedy is applicable, it is faster (usually O(n) or O(n log n) vs DP's O(n²)).

---

## Pattern 1: Interval Scheduling / Meeting Rooms

```python
def can_attend_all(intervals):
    """Can one person attend all meetings?"""
    intervals.sort(key=lambda x: x[0])  # sort by start time
    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i-1][1]:  # overlap
            return False
    return True

def min_meeting_rooms(intervals):
    """Minimum rooms needed for all meetings"""
    import heapq
    intervals.sort(key=lambda x: x[0])
    heap = []  # end times of ongoing meetings
    for start, end in intervals:
        if heap and heap[0] <= start:
            heapq.heapreplace(heap, end)  # reuse a room
        else:
            heapq.heappush(heap, end)     # need a new room
    return len(heap)

def merge_intervals(intervals):
    """Merge all overlapping intervals"""
    if not intervals: return []
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:         # overlaps with last
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged
```

---

## Pattern 2: Jump Game

```python
def can_jump(nums):
    """Can you reach the last index?"""
    max_reach = 0
    for i, jump in enumerate(nums):
        if i > max_reach: return False  # can't reach index i
        max_reach = max(max_reach, i + jump)
    return True

def jump_game_ii(nums):
    """Minimum jumps to reach last index"""
    jumps = 0
    current_end = 0
    farthest = 0
    
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        if i == current_end:       # must jump now
            jumps += 1
            current_end = farthest
    return jumps
```

---

## Pattern 3: Gas Station

```python
def can_complete_circuit(gas, cost):
    total_tank = 0
    current_tank = 0
    start = 0
    
    for i in range(len(gas)):
        gain = gas[i] - cost[i]
        total_tank += gain
        current_tank += gain
        if current_tank < 0:   # can't reach next station from current start
            start = i + 1      # try starting from next station
            current_tank = 0
    
    return start if total_tank >= 0 else -1
```

---

## Common Mistakes
- Assuming greedy always works — verify with the greedy choice property.
- Forgetting to sort intervals by start time before processing.
- Using greedy when DP is required (e.g., 0/1 knapsack cannot use greedy).

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Jump Game | #55 | Medium |
| Jump Game II | #45 | Medium |
| Gas Station | #134 | Medium |
| Meeting Rooms | #252 | Easy |
| Meeting Rooms II | #253 | Medium |
| Merge Intervals | #56 | Medium |

## Key Takeaways
- Greedy = always pick the locally best option without going back.
- Sort first: most greedy problems require sorting by start time, end time, or value.
- If greedy fails on a test case, switch to DP.

> **Summary:** Greedy works when the locally optimal choice leads to global optimum. Classic problems: interval scheduling (sort by end time), jump game (track farthest reach), meeting rooms (min heap of end times). Always sort before greedy.

---

# Part 14 — Dynamic Programming

## Definition

Dynamic Programming (DP) solves problems by **breaking them into overlapping subproblems** and storing results to avoid recomputation. It is the right choice when:
- Problem has **optimal substructure** (optimal solution uses optimal sub-solutions).
- Problem has **overlapping subproblems** (same subproblems are solved multiple times).

## Two Approaches

```python
# MEMOIZATION (Top-Down) — recursion + cache
from functools import lru_cache

@lru_cache(maxsize=None)
def fib_memo(n):
    if n <= 1: return n
    return fib_memo(n-1) + fib_memo(n-2)

# TABULATION (Bottom-Up) — iterative, fills table
def fib_tab(n):
    if n <= 1: return n
    dp = [0] * (n + 1)
    dp[1] = 1
    for i in range(2, n + 1):
        dp[i] = dp[i-1] + dp[i-2]
    return dp[n]

# SPACE OPTIMIZED — only keep last two values
def fib_opt(n):
    if n <= 1: return n
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    return b
```

---

## Pattern 1: 1D DP — House Robber

```python
def house_robber(nums):
    """Max money without robbing adjacent houses"""
    if not nums: return 0
    if len(nums) == 1: return nums[0]
    
    # dp[i] = max money from houses 0..i
    prev2, prev1 = 0, 0
    for num in nums:
        curr = max(prev1, prev2 + num)  # skip or rob current
        prev2, prev1 = prev1, curr
    return prev1

# State definition: dp[i] = max rob from first i houses
# Transition: dp[i] = max(dp[i-1], dp[i-2] + nums[i])
```

---

## Pattern 2: 2D DP — Unique Paths

```python
def unique_paths(m, n):
    """Count paths from top-left to bottom-right (only right/down moves)"""
    dp = [[1] * n for _ in range(m)]  # first row and col = 1
    
    for r in range(1, m):
        for c in range(1, n):
            dp[r][c] = dp[r-1][c] + dp[r][c-1]  # from above + from left
    
    return dp[m-1][n-1]

# Minimum path sum
def min_path_sum(grid):
    m, n = len(grid), len(grid[0])
    for r in range(m):
        for c in range(n):
            if r == 0 and c == 0: continue
            from_top  = grid[r-1][c] if r > 0 else float('inf')
            from_left = grid[r][c-1] if c > 0 else float('inf')
            grid[r][c] += min(from_top, from_left)
    return grid[m-1][n-1]
```

---

## Pattern 3: 0/1 Knapsack

```python
def knapsack(weights, values, capacity):
    n = len(weights)
    # dp[i][w] = max value using first i items with capacity w
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            # Don't take item i
            dp[i][w] = dp[i-1][w]
            # Take item i (if it fits)
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i][w], dp[i-1][w - weights[i-1]] + values[i-1])
    
    return dp[n][capacity]

# Space-optimized (1D)
def knapsack_1d(weights, values, capacity):
    dp = [0] * (capacity + 1)
    for i in range(len(weights)):
        for w in range(capacity, weights[i] - 1, -1):  # traverse right to left!
            dp[w] = max(dp[w], dp[w - weights[i]] + values[i])
    return dp[capacity]
```

---

## Pattern 4: Longest Common Subsequence (LCS)

```python
def lcs(text1, text2):
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1      # chars match
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])  # take best of skipping either
    
    return dp[m][n]
```

---

## Pattern 5: Longest Increasing Subsequence (LIS)

```python
def lis(nums):
    """O(n²) DP solution"""
    n = len(nums)
    dp = [1] * n  # each element is a subsequence of length 1
    
    for i in range(1, n):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    
    return max(dp)

def lis_nlogn(nums):
    """O(n log n) using binary search"""
    import bisect
    tails = []
    for num in nums:
        pos = bisect.bisect_left(tails, num)
        if pos == len(tails):
            tails.append(num)
        else:
            tails[pos] = num
    return len(tails)
```

---

## Pattern 6: Coin Change (Unbounded Knapsack)

```python
def coin_change(coins, amount):
    """Minimum coins to make amount"""
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    
    for a in range(1, amount + 1):
        for coin in coins:
            if coin <= a:
                dp[a] = min(dp[a], dp[a - coin] + 1)
    
    return dp[amount] if dp[amount] != float('inf') else -1

def coin_change_ways(coins, amount):
    """Number of ways to make amount"""
    dp = [0] * (amount + 1)
    dp[0] = 1
    for coin in coins:
        for a in range(coin, amount + 1):
            dp[a] += dp[a - coin]
    return dp[amount]
```

---

## DP Problem-Solving Framework

1. **Define the state:** What does `dp[i]` or `dp[i][j]` represent?
2. **Write the recurrence:** How does `dp[i]` relate to previous states?
3. **Set base cases:** What are the smallest subproblems?
4. **Determine traversal order:** Top-down (memoization) or bottom-up (tabulation)?
5. **Space optimize:** Can you reduce from O(n²) to O(n)?

## Common Mistakes
- Not defining the state clearly before writing code.
- Off-by-one errors in table dimensions (use `n+1` for 0-indexed).
- In knapsack 1D: traversing left-to-right uses an item multiple times — use right-to-left for 0/1.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Climbing Stairs | #70 | Easy |
| House Robber | #198 | Medium |
| Coin Change | #322 | Medium |
| Longest Common Subsequence | #1143 | Medium |
| Longest Increasing Subsequence | #300 | Medium |
| 0-1 Knapsack (variations) | #416, #494 | Medium |
| Edit Distance | #72 | Hard |
| Word Break | #139 | Medium |

## Key Takeaways
- DP = recursion + memoization OR iterative tabulation. Both give the same result.
- Always define `dp[i]` clearly before writing the recurrence.
- Most DP problems fall into 5 patterns: 1D linear, 2D grid, knapsack, LCS/LIS, interval DP.

> **Summary:** DP solves problems with overlapping subproblems. Use memoization (top-down) or tabulation (bottom-up). The 5 key patterns: 1D (house robber), 2D grid (unique paths), 0/1 knapsack, LCS (sequence alignment), LIS (patience sorting). Always define state → recurrence → base case → order.


# Part 15 — Bit Manipulation

## Definition

Bit manipulation operates directly on **binary representations** of integers. It is extremely fast (single CPU instruction) and often produces elegant solutions to otherwise complex problems.

## Binary Refresher

```
Decimal 13 = Binary 1101
Position:    3 2 1 0
Bits:        1 1 0 1

Value = 1*8 + 1*4 + 0*2 + 1*1 = 13
```

## Core Bitwise Operators

| Operator | Symbol | Example | Result |
|---|---|---|---|
| AND | `&` | `5 & 3` = `101 & 011` | `001` = 1 |
| OR | `|` | `5 | 3` = `101 | 011` | `111` = 7 |
| XOR | `^` | `5 ^ 3` = `101 ^ 011` | `110` = 6 |
| NOT | `~` | `~5` | `-6` (two's complement) |
| Left shift | `<<` | `5 << 1` | `10` = 10 (multiply by 2) |
| Right shift | `>>` | `5 >> 1` | `2` (divide by 2) |

---

## Essential Bit Tricks

```python
n = 13  # 1101 in binary

# Check if bit i is set
def is_bit_set(n, i):
    return (n >> i) & 1 == 1

# Set bit i
def set_bit(n, i):
    return n | (1 << i)

# Clear bit i
def clear_bit(n, i):
    return n & ~(1 << i)

# Toggle bit i
def toggle_bit(n, i):
    return n ^ (1 << i)

# Check if n is a power of 2
def is_power_of_two(n):
    return n > 0 and (n & (n - 1)) == 0
# n=8: 1000 & 0111 = 0000 → True
# n=6: 0110 & 0101 = 0100 → False

# Count set bits (Brian Kernighan's algorithm)
def count_bits(n):
    count = 0
    while n:
        n &= (n - 1)  # removes lowest set bit
        count += 1
    return count
# n=13=1101: 1101&1100=1100, 1100&1011=1000, 1000&0111=0000 → count=3

# Get lowest set bit
def lowest_set_bit(n):
    return n & (-n)

# Remove lowest set bit
def remove_lowest_set_bit(n):
    return n & (n - 1)
```

---

## XOR Tricks

XOR has two powerful properties:
- `a ^ a = 0` (any number XORed with itself = 0)
- `a ^ 0 = a` (any number XORed with 0 = itself)

```python
# Single Number — find the element that appears only once
def single_number(nums):
    result = 0
    for num in nums:
        result ^= num   # duplicates cancel out: a^a=0
    return result
# [4,1,2,1,2]: 4^1^2^1^2 = 4^0^0 = 4

# Swap without temp variable
a, b = 5, 3
a ^= b   # a = 5^3 = 6
b ^= a   # b = 3^6 = 5
a ^= b   # a = 6^5 = 3
# a=3, b=5 (swapped)

# Find the one number missing from 1..n
def missing_number(nums):
    n = len(nums)
    result = n
    for i, num in enumerate(nums):
        result ^= i ^ num
    return result
```

---

## Bit Masking (Subsets)

```python
# Enumerate all subsets of a set using bitmask
def all_subsets(arr):
    n = len(arr)
    result = []
    for mask in range(1 << n):     # 2^n masks
        subset = []
        for i in range(n):
            if mask & (1 << i):    # bit i is set → include arr[i]
                subset.append(arr[i])
        result.append(subset)
    return result
# For arr=[1,2,3]: masks 0..7 generate all 8 subsets
```

---

## Common Mistakes
- Confusing `~n` in Python (arbitrary precision → always negative for positive n).
- Not using parentheses around bit operations in conditions — precedence issues.
- Applying right shift to negative numbers — Python uses arithmetic shift (sign-extended).

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Single Number | #136 | Easy |
| Number of 1 Bits | #191 | Easy |
| Counting Bits | #338 | Easy |
| Missing Number | #268 | Easy |
| Reverse Bits | #190 | Easy |
| Power of Two | #231 | Easy |
| Sum of Two Integers (no +) | #371 | Medium |

## Key Takeaways
- XOR cancels duplicates: `a^a=0`, `a^0=a`. Single number and missing number use this.
- `n & (n-1)` removes the lowest set bit — count bits with a loop.
- `n & (-n)` isolates the lowest set bit.
- Bitmask can represent subsets of up to 32 elements compactly.

> **Summary:** Bit manipulation gives O(1) solutions to problems involving parity, powers of two, and duplicate detection. XOR is the most powerful tool — it cancels pairs. `n & (n-1)` removes lowest set bit. Know these 10 tricks cold — they appear constantly in easy/medium problems.

---

# Part 16 — Binary Search

## Definition

Binary search eliminates **half the search space** at each step by comparing the target with the midpoint. It works on any **monotonic** (sorted or has a clear "yes/no" boundary) search space.

**Template — Standard Binary Search:**

```python
def binary_search(arr, target):
    lo, hi = 0, len(arr) - 1
    
    while lo <= hi:
        mid = lo + (hi - lo) // 2  # avoid integer overflow (good habit)
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            lo = mid + 1
        else:
            hi = mid - 1
    
    return -1  # not found
```

---

## Lower Bound / Upper Bound

```python
import bisect

# Lower bound: first index where arr[i] >= target
def lower_bound(arr, target):
    lo, hi = 0, len(arr)
    while lo < hi:
        mid = (lo + hi) // 2
        if arr[mid] < target: lo = mid + 1
        else: hi = mid
    return lo  # insert position

# Python built-in equivalents
bisect.bisect_left(arr, target)   # lower bound
bisect.bisect_right(arr, target)  # upper bound (first > target)

# Count occurrences of target in sorted array
def count_occurrences(arr, target):
    left  = bisect.bisect_left(arr, target)
    right = bisect.bisect_right(arr, target)
    return right - left
```

---

## Binary Search on Rotated Array

```python
def search_rotated(nums, target):
    lo, hi = 0, len(nums) - 1
    
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target: return mid
        
        # Left half is sorted
        if nums[lo] <= nums[mid]:
            if nums[lo] <= target < nums[mid]:
                hi = mid - 1      # target in left half
            else:
                lo = mid + 1      # target in right half
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[hi]:
                lo = mid + 1      # target in right half
            else:
                hi = mid - 1      # target in left half
    
    return -1

# Dry run: nums=[4,5,6,7,0,1,2], target=0
# lo=0, hi=6, mid=3, nums[3]=7
# left sorted (4<=7): target 0 NOT in [4,7), so lo=4
# lo=4, hi=6, mid=5, nums[5]=1
# right sorted (1<=2): target 0 NOT in (1,2], so hi=4
# lo=4, hi=4, mid=4, nums[4]=0 == target → return 4 ✓
```

---

## Binary Search on Answer (Search Space)

**Key insight:** If you can check "is X achievable?" in O(n), binary search on X gives O(n log n).

```python
# Minimize maximum — split array into k subarrays to minimize the largest sum
def split_array(nums, k):
    def can_split(max_sum):
        parts = 1
        current = 0
        for num in nums:
            if current + num > max_sum:
                parts += 1
                current = num
                if parts > k: return False
            else:
                current += num
        return True
    
    lo, hi = max(nums), sum(nums)
    while lo < hi:
        mid = (lo + hi) // 2
        if can_split(mid):
            hi = mid          # might do better
        else:
            lo = mid + 1      # must increase limit
    return lo

# Koko eating bananas — find minimum eating speed
def min_eating_speed(piles, h):
    import math
    def can_finish(speed):
        return sum(math.ceil(p / speed) for p in piles) <= h
    
    lo, hi = 1, max(piles)
    while lo < hi:
        mid = (lo + hi) // 2
        if can_finish(mid): hi = mid
        else: lo = mid + 1
    return lo
```

---

## Binary Search Template Summary

```python
# Template 1: Find exact target
lo, hi = 0, n - 1
while lo <= hi:
    mid = (lo + hi) // 2
    if check(mid): return mid
    elif go_right: lo = mid + 1
    else: hi = mid - 1

# Template 2: Find leftmost valid position (lower bound)
lo, hi = 0, n
while lo < hi:
    mid = (lo + hi) // 2
    if condition(mid): hi = mid      # valid — try smaller
    else: lo = mid + 1               # invalid — try larger
return lo

# Template 3: Find rightmost valid position (upper bound)
lo, hi = 0, n
while lo < hi:
    mid = (lo + hi + 1) // 2        # +1 to avoid infinite loop
    if condition(mid): lo = mid      # valid — try larger
    else: hi = mid - 1               # invalid — try smaller
return lo
```

---

## Common Mistakes
- Using `(lo + hi) // 2` instead of `lo + (hi - lo) // 2` — potential overflow in other languages; good habit in Python too.
- Using `lo < hi` vs `lo <= hi` incorrectly — Template 1 uses `<=`, Templates 2/3 use `<`.
- Not moving mid in Template 3: `mid = (lo + hi + 1) // 2` avoids infinite loop when `lo = hi - 1`.

## Key LeetCode Problems

| Problem | Number | Difficulty |
|---|---|---|
| Binary Search | #704 | Easy |
| Search in Rotated Sorted Array | #33 | Medium |
| Find Minimum in Rotated Array | #153 | Medium |
| Koko Eating Bananas | #875 | Medium |
| Split Array Largest Sum | #410 | Hard |
| Search a 2D Matrix | #74 | Medium |
| Time Based Key-Value Store | #981 | Medium |

## Key Takeaways
- Binary search works on any monotonic search space, not just sorted arrays.
- "Search on answer" applies when: check function is monotonic, answer range is bounded.
- Memorize the three templates: exact match, leftmost valid, rightmost valid.

> **Summary:** Binary search halves the search space each step — O(log n). Use it on sorted arrays, rotated arrays, and search-on-answer problems. Three templates cover all cases. When a brute force check function is O(n) and the answer space is bounded, binary search gives O(n log n) solution.


# Part 17 — Problem-Solving Patterns

Recognizing which **pattern** applies to a problem is the most important interview skill. These 11 patterns cover the majority of LeetCode medium and hard problems.

---

## Pattern 1: Sliding Window

**When to use:** "subarray", "substring", "window of size k", "longest/shortest contiguous sequence"

```python
# Fixed window
def fixed_window(arr, k):
    window_sum = sum(arr[:k])
    result = window_sum
    for i in range(k, len(arr)):
        window_sum += arr[i] - arr[i-k]
        result = max(result, window_sum)
    return result

# Variable window (shrink when condition violated)
def variable_window(arr, target):
    left = 0; total = 0; best = 0
    for right in range(len(arr)):
        total += arr[right]
        while total > target:
            total -= arr[left]; left += 1
        best = max(best, right - left + 1)
    return best
```

---

## Pattern 2: Two Pointers

**When to use:** Sorted array, find pairs/triplets with a target, remove duplicates, is palindrome

```python
def two_pointers_template(arr, target):
    left, right = 0, len(arr) - 1
    while left < right:
        current = arr[left] + arr[right]
        if current == target: return [left, right]
        elif current < target: left += 1
        else: right -= 1
    return []
```

---

## Pattern 3: Fast and Slow Pointers

**When to use:** Linked list cycle, find middle, detect palindrome in linked list

```python
def fast_slow_template(head):
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast: return True  # cycle detected
    return False
```

---

## Pattern 4: Merge Intervals

**When to use:** Overlapping intervals, meeting rooms, calendar problems

```python
def merge_intervals_template(intervals):
    intervals.sort(key=lambda x: x[0])
    result = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= result[-1][1]:
            result[-1][1] = max(result[-1][1], end)
        else:
            result.append([start, end])
    return result
```

---

## Pattern 5: Cyclic Sort

**When to use:** Array of numbers in range 1..n, find missing/duplicate numbers

```python
def cyclic_sort(nums):
    i = 0
    while i < len(nums):
        j = nums[i] - 1   # where nums[i] should be
        if nums[i] != nums[j]:
            nums[i], nums[j] = nums[j], nums[i]  # swap to correct position
        else:
            i += 1

def find_missing(nums):
    cyclic_sort(nums)
    for i, num in enumerate(nums):
        if num != i + 1: return i + 1
    return len(nums) + 1
```

---

## Pattern 6: Monotonic Stack

**When to use:** "next greater element", "next smaller", "largest rectangle", "daily temperatures"

```python
def next_greater_template(nums):
    result = [-1] * len(nums)
    stack = []  # stores indices
    for i in range(len(nums)):
        while stack and nums[stack[-1]] < nums[i]:
            result[stack.pop()] = nums[i]
        stack.append(i)
    return result
```

---

## Pattern 7: Top K Elements

**When to use:** "Kth largest/smallest", "top K frequent", "K closest"

```python
import heapq

def top_k_template(nums, k):
    # Min heap of size k — O(n log k)
    heap = []
    for num in nums:
        heapq.heappush(heap, num)
        if len(heap) > k:
            heapq.heappop(heap)
    return heap[0]  # kth largest
```

---

## Pattern 8: Tree DFS

**When to use:** Path problems, tree properties (height, diameter), validate structure

```python
def tree_dfs_template(root):
    if not root: return base_value
    
    left_result  = tree_dfs_template(root.left)
    right_result = tree_dfs_template(root.right)
    
    # Update global answer using left + root + right
    # Return value passed UP to parent
    return combine(root.val, left_result, right_result)
```

---

## Pattern 9: Tree BFS

**When to use:** Level-order traversal, zigzag traversal, right side view, min depth

```python
from collections import deque

def tree_bfs_template(root):
    if not root: return []
    result = []
    queue = deque([root])
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:  queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result
```

---

## Pattern 10: Backtracking

**When to use:** "Generate all", "find all paths", "permutations/combinations/subsets"

```python
def backtrack_template(result, path, choices, start):
    if is_complete(path):
        result.append(path[:])
        return
    for i in range(start, len(choices)):
        if not is_valid(choices[i], path): continue
        path.append(choices[i])           # choose
        backtrack_template(result, path, choices, i + 1)
        path.pop()                         # unchoose
```

---

## Pattern 11: Union Find

**When to use:** "Connected components", "group/merge elements", "redundant connection"

```python
class UF:
    def __init__(self, n):
        self.p = list(range(n))
    def find(self, x):
        if self.p[x] != x: self.p[x] = self.find(self.p[x])
        return self.p[x]
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py: return False
        self.p[px] = py
        return True
```

---

## Pattern Recognition Guide

| Keyword in problem | Consider |
|---|---|
| "subarray", "substring", "window" | Sliding Window |
| "sorted array", "pairs", "triplets" | Two Pointers |
| "linked list", "cycle", "middle" | Fast/Slow Pointers |
| "intervals", "meetings", "calendar" | Merge Intervals |
| "1 to n", "missing", "duplicate" | Cyclic Sort |
| "next greater/smaller" | Monotonic Stack |
| "top K", "Kth largest" | Heap / Top K |
| "all subsets", "all permutations" | Backtracking |
| "tree path", "tree property" | Tree DFS |
| "level order", "min depth" | Tree BFS |
| "connected", "group", "component" | Union Find or BFS/DFS |
| "sorted", "minimize/maximize answer" | Binary Search |
| "optimal substructure + overlapping" | Dynamic Programming |
| "max sum subarray" | Kadane's Algorithm |

---

# Part 18 — Interview Strategy

## Step 1: Understand the Problem (2-3 minutes)

- Restate the problem in your own words.
- Ask clarifying questions:
  - What is the input format? (array, string, linked list?)
  - Can the input be empty?
  - Are there duplicates?
  - What are the constraints? (n ≤ 10⁴ or n ≤ 10⁸?)
  - What should be returned — index, value, count?

**Constraints → Complexity:**

| Constraint | Target complexity |
|---|---|
| n ≤ 20 | O(2ⁿ) or O(n!) acceptable |
| n ≤ 1,000 | O(n²) acceptable |
| n ≤ 100,000 | O(n log n) required |
| n ≤ 10,000,000 | O(n) required |

---

## Step 2: Think Out Loud (2-3 minutes)

- Start with a brute force approach and state its complexity.
- Then optimize: "I can use [pattern] to bring this down to O(...)."
- Confirm the approach with the interviewer before coding.

**How to identify the right data structure:**

| Need | Use |
|---|---|
| O(1) lookup by key | Hash map (`dict`) |
| O(1) lookup by membership | Hash set (`set`) |
| O(log n) insert/delete/min | Heap (`heapq`) |
| LIFO access | Stack (list) |
| FIFO access | Queue (`deque`) |
| Sorted order + fast lookup | BST / `SortedList` |
| Prefix matching | Trie |
| Group elements | Union Find |

---

## Step 3: Write the Code (15-20 minutes)

- Start with function signature and handle edge cases first.
- Write helper functions if needed — keeps main function clean.
- Talk through each major step as you write it.
- Do not go silent — narrate your thinking.

```python
def solve(nums: list[int], target: int) -> list[int]:
    # Edge case
    if not nums: return []
    
    # Main logic
    seen = {}
    for i, num in enumerate(nums):
        complement = target - num
        if complement in seen:
            return [seen[complement], i]
        seen[num] = i
    
    return []
```

---

## Step 4: Test Your Solution (3-5 minutes)

Test in this order:
1. **Normal case** — the example from the problem.
2. **Edge cases:**
   - Empty input
   - Single element
   - All same elements
   - Sorted / reverse sorted
   - Negative numbers (if applicable)
3. **Large input** — trace the complexity mentally.

---

## Step 5: Optimize and Discuss Trade-offs

- Can space complexity be reduced?
- Is there a mathematical insight that eliminates a loop?
- When would the approach fail? (large n, negative weights, etc.)

---

## Common Interview Anti-patterns to Avoid

| Anti-pattern | What to do instead |
|---|---|
| Jumping to code immediately | Clarify and plan first |
| Going silent for >1 minute | Narrate your thinking continuously |
| Writing O(n²) without acknowledging it | Mention brute force first, then optimize |
| Not testing with edge cases | Always test before saying "done" |
| Giving up when stuck | Say "let me think about smaller subproblems" |

> **Summary:** Interview = Understand → Plan → Code → Test → Optimize. Always clarify constraints first. Match constraint size to required complexity. Narrate your thinking — interviewers evaluate reasoning, not just final code.


---

# Complexity Cheat Sheet

## Data Structure Operations

| Data Structure | Access | Search | Insert | Delete | Space |
|---|---|---|---|---|---|
| Array (list) | O(1) | O(n) | O(n) | O(n) | O(n) |
| Linked List | O(n) | O(n) | O(1) head | O(1) given ptr | O(n) |
| Hash Map (dict) | O(1) avg | O(1) avg | O(1) avg | O(1) avg | O(n) |
| Hash Set (set) | — | O(1) avg | O(1) avg | O(1) avg | O(n) |
| Stack (list) | O(n) | O(n) | O(1) | O(1) | O(n) |
| Queue (deque) | O(n) | O(n) | O(1) | O(1) | O(n) |
| Binary Heap | O(1) min | O(n) | O(log n) | O(log n) | O(n) |
| BST (balanced) | O(log n) | O(log n) | O(log n) | O(log n) | O(n) |
| Trie | — | O(m) | O(m) | O(m) | O(n*m) |

*m = key/word length*

---

## Sorting Algorithms

| Algorithm | Best | Average | Worst | Space | Stable |
|---|---|---|---|---|---|
| Bubble Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No |
| Counting Sort | O(n+k) | O(n+k) | O(n+k) | O(k) | Yes |
| Python `sort()` | O(n) | O(n log n) | O(n log n) | O(n) | Yes (Timsort) |

---

## Graph Algorithms

| Algorithm | Time | Space | Use case |
|---|---|---|---|
| BFS | O(V + E) | O(V) | Shortest path (unweighted) |
| DFS | O(V + E) | O(V) | Connectivity, cycles |
| Topological Sort | O(V + E) | O(V) | DAG ordering |
| Union Find | O(α(n)) ≈ O(1) | O(V) | Components, cycles |
| Dijkstra | O((V+E) log V) | O(V) | SSSP (non-negative weights) |
| Bellman-Ford | O(V × E) | O(V) | SSSP (negative weights) |

---

## DP Pattern Complexities

| Pattern | Time | Space | Optimized Space |
|---|---|---|---|
| 1D DP (Fibonacci/Robber) | O(n) | O(n) | O(1) |
| 2D DP (LCS, grid) | O(m×n) | O(m×n) | O(min(m,n)) |
| 0/1 Knapsack | O(n×W) | O(n×W) | O(W) |
| LIS | O(n²) | O(n) | O(n log n) with bisect |
| Coin Change | O(n×amount) | O(amount) | — |

---

## Python Built-ins Complexity

| Operation | Complexity |
|---|---|
| `list.append(x)` | O(1) amortized |
| `list.pop()` | O(1) |
| `list.pop(0)` | O(n) — use deque! |
| `list.insert(i, x)` | O(n) |
| `x in list` | O(n) |
| `x in set` | O(1) average |
| `dict[key]` | O(1) average |
| `sorted(iterable)` | O(n log n) |
| `list.sort()` | O(n log n) |
| `heapq.heappush` | O(log n) |
| `heapq.heappop` | O(log n) |
| `heapq.heapify` | O(n) |
| `''.join(list)` | O(n) |
| `Counter(iterable)` | O(n) |

---

# Final Revision Sheet

> Last 15 minutes before interview — review this page only.

---

## Algorithm Complexity Ladder

```
O(1) → O(log n) → O(n) → O(n log n) → O(n²) → O(2ⁿ) → O(n!)
hash    binary     loop    merge sort   nested   subsets  perms
lookup  search             heap sort    loops
```

---

## Pattern Recognition — Quick Map

| See this | Think this |
|---|---|
| Subarray / substring | Sliding Window |
| Sorted array, pairs | Two Pointers |
| Linked list cycle | Fast/Slow Pointer |
| Overlapping intervals | Sort + Merge |
| Range 1..n, missing/dup | Cyclic Sort |
| Next greater/smaller | Monotonic Stack |
| Top K, Kth largest | Min-heap of size K |
| All subsets/permutations | Backtracking |
| Tree path sum | DFS (return from children) |
| Level order, min depth | BFS |
| Connected components | Union Find / BFS |
| "Optimal" + overlapping subproblems | DP |
| Greedy: sort by end/value first | Greedy |
| Monotonic function, minimize max | Binary Search on Answer |

---

## 10 Most Important Algorithms to Know Cold

1. **BFS / DFS** — traversal for graphs and trees
2. **Binary Search** — all 3 templates
3. **Two Pointers** — sorted arrays, pairs
4. **Sliding Window** — subarrays/substrings
5. **Merge Sort / Quick Sort** — understand both
6. **Dijkstra** — weighted shortest path
7. **Union Find** — cycle detection, grouping
8. **Topological Sort** — dependency ordering
9. **Backtracking template** — generate all solutions
10. **DP: 1D, 2D, Knapsack, LCS** — all four

---

## 15 Must-Know LeetCode Problems

| # | Problem | Pattern |
|---|---|---|
| 1 | Two Sum | Hash Map |
| 121 | Best Time to Buy Stock | Kadane variant |
| 206 | Reverse Linked List | Linked List |
| 200 | Number of Islands | BFS/DFS |
| 70 | Climbing Stairs | 1D DP |
| 322 | Coin Change | DP |
| 53 | Maximum Subarray | Kadane |
| 207 | Course Schedule | Topological Sort |
| 417 | Pacific Atlantic Waterflow | Multi-source BFS |
| 33 | Search Rotated Sorted Array | Binary Search |
| 76 | Minimum Window Substring | Sliding Window |
| 46 | Permutations | Backtracking |
| 295 | Find Median from Data Stream | Two Heaps |
| 300 | Longest Increasing Subsequence | DP |
| 212 | Word Search II | Trie + DFS |

---

## Python Tricks to Remember

```python
# Min/Max with default
min(a, b, default_if_empty)

# Sort by second element
arr.sort(key=lambda x: x[1])

# Reverse sort
arr.sort(reverse=True)

# Counter most common
Counter(arr).most_common(k)

# Infinity
float('inf'), float('-inf')

# Integer division (floors toward -inf in Python)
-7 // 2  # = -4, NOT -3

# All subsets via bitmask
for mask in range(1 << n): ...

# List to dict frequency
from collections import Counter
freq = Counter(arr)

# Binary search
import bisect
bisect.bisect_left(arr, target)   # lower bound
bisect.bisect_right(arr, target)  # upper bound

# Memoization
from functools import lru_cache
@lru_cache(maxsize=None)
def dp(i, j): ...

# Deque
from collections import deque
q = deque(); q.append(x); q.appendleft(x); q.pop(); q.popleft()
```

---

## State of Mind Before the Interview

1. **First minute:** Clarify constraints and edge cases. Do NOT start coding.
2. **Brute force first:** Always mention the O(n²) solution before optimizing.
3. **Pattern match:** Which of the 11 patterns fits?
4. **Code cleanly:** Good variable names, edge case at the top.
5. **Test out loud:** Walk through your example step by step.
6. **Complexity last:** Always state time and space complexity when done.

---

*End of guide. Good luck in your interviews.*
