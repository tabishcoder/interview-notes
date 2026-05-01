# Arrays + Strings + HashMaps — Interview Guide (Complete DSA Prep)

A **pattern-based** handbook for **internship, junior MERN, and Pakistani/FAANG-style** screening — **only arrays, strings, and hashing**. No trees or graphs.

---

## Table of Contents

1. [Introduction: Why Arrays, Strings, HashMaps Matter](#1-introduction-why-arrays-strings-hashmaps-matter)
2. [Core Problem-Solving Patterns (VERY IMPORTANT)](#2-core-problem-solving-patterns-very-important)
3. [Arrays Interview Questions (Complete Set)](#3-arrays-interview-questions-complete-set)
4. [Strings Interview Questions (Complete Set)](#4-strings-interview-questions-complete-set)
5. [HashMap / Hashing Interview Questions (CORE SECTION)](#5-hashmap--hashing-interview-questions-core-section)
6. [Combined Patterns (VERY IMPORTANT)](#6-combined-patterns-very-important)
7. [Step-by-Step Problem Solving Framework](#7-step-by-step-problem-solving-framework)
8. [Dry Run Training Section](#8-dry-run-training-section)
9. [Follow-Up Questions (Interview Trick Section)](#9-follow-up-questions-interview-trick-section)
10. [Common Mistakes Students Make](#10-common-mistakes-students-make)
11. [Coding Practice Plan](#11-coding-practice-plan)
12. [Interview Simulation Section](#12-interview-simulation-section)
13. [Optimization Mindset](#13-optimization-mindset)
14. [Final Revision Sheet](#14-final-revision-sheet)

---

## 1. Introduction: Why Arrays, Strings, HashMaps Matter

### Why a large share of interview questions use these topics

- **Arrays** are the universal memory model for sequences; loops, indices, and bounds appear everywhere.
- **Strings** are character arrays with **pattern** questions that test **sliding window** and **frequency**.
- **Hash maps** (objects / `Map` in JS) turn many **O(n²)** “search all pairs” problems into **O(n)** by trading **space for time**.

### How companies test thinking

Interviewers care whether you can:

1. Start with a **correct** brute force.  
2. **See repeated work** and remove it with a structure.  
3. State **time/space** and **edge cases** aloud.

### Brute force vs optimized

| Phase | Goal |
|-------|------|
| Brute | Prove you understand the problem; set correctness baseline |
| Optimize | Cut inner scans with **hashing**, **two pointers**, **prefix sums**, **sorting** |

**Relationship:** Most optimal solutions are **“one pass + O(1) or O(k) extra state per step.”**

---

## 2. Core Problem-Solving Patterns (VERY IMPORTANT)

### Pattern 1: Two pointers

| Variant | When it works |
|---------|----------------|
| **Opposite ends** | Sorted array: pair sum, palindrome check in arr, container with water style |
| **Same direction (slow/fast)** | Remove dupes in place, cycle detection (array-as-graph is out of scope—concept only), merge from two arrays |

**Interview line:** “Can I process the array in **one pass** with two indices that **move monotonically**?”

---

### Pattern 2: Sliding window

| Type | Idea |
|------|------|
| **Fixed size k** | Maintain sum / max / count of bad chars in window |
| **Variable size** | Expand until invalid, **shrink** until valid again — e.g. longest unique substring |

---

### Pattern 3: Prefix sum

- `pre[i] = sum(arr[0..i-1])` → sum `[L,R) = pre[R] - pre[L]`.
- Powers **range queries** and pairs with **hash map** for “subarray sum = K”.

---

### Pattern 4: Frequency counting (HashMap)

- Count occurrences, detect duplicates, **anagram** (same frequency vector).
- Key: **normalize** key (`char`, sorted string, or tuple).

---

### Pattern 5: Sorting + greedy

- Sorting gives **order** → one pass with greedy choice (intervals, meeting rooms style—intervals are array problems).
- **Trade-off:** O(n log n) sort vs O(n) counting sort when range is small integers.

---

### Pattern 6: Subarray / substring

- **Continuous** segment: often **window**, **prefix sum**, or **two pointers** after sort.

---

## 3. Arrays Interview Questions (Complete Set)

**Notation:** `n` = length; use **JavaScript** below.

---

### EASY 1 — Find largest element

**Brute:** Scan once, track max — already optimal for general unsorted array.

```javascript
function maxEl(a) {
  let m = -Infinity;
  for (const x of a) if (x > m) m = x;
  return m;
}
```

- **Time:** O(n), **Space:** O(1)  
- **Dry run:** `[3,1,4]` → m: 3→3→4  
- **Follow-up:** If **sorted** ascending, answer is `a[n-1]` in O(1). Multiple max indices? Return **index** instead.

---

### EASY 2 — Reverse array

**Brute:** New array from end to start — O(n) time & space.

**Optimal (in place):** Two pointers swap from ends.

```javascript
function reverseInPlace(a) {
  let i = 0, j = a.length - 1;
  while (i < j) [a[i], a[j]] = [a[j], a[i]], i++, j--;
  return a;
}
```

- **Time:** O(n), **Space:** O(1) extra  
- **Dry run:** `[1,2,3]` → swap 1&3 → `[3,2,1]`  
- **Follow-up:** Reverse **part** `[l,r]` only.

---

### EASY 3 — Remove duplicates (sorted array, in-place)

**Brute:** Use extra `Set`, copy back — O(n) space.

**Optimal:** Slow pointer `k` = next write; fast `i` scans; only write when `a[i] !== a[k-1]`.

```javascript
function removeDupSorted(a) {
  if (!a.length) return 0;
  let k = 1;
  for (let i = 1; i < a.length; i++)
    if (a[i] !== a[k - 1]) a[k++] = a[i];
  return k; // new length
}
```

- **Time:** O(n), **Space:** O(1)  
- **Dry run:** `[1,1,2,2,3]` → k grows after unique writes  
- **Follow-up:** **Unsorted** — use `Set` O(n) space or sort first O(n log n).

---

### EASY 4 — Linear search

```javascript
function linearSearch(a, t) {
  for (let i = 0; i < a.length; i++) if (a[i] === t) return i;
  return -1;
}
```

**Follow-up:** **Binary search** if sorted — O(log n) (still array pattern).

---

### MEDIUM 5 — Two Sum (indices, unsorted)

**Brute:** All pairs O(n²).

**Optimal:** Hash map **value → index**; for each `x`, need `target - x`.

```javascript
function twoSum(a, target) {
  const m = new Map();
  for (let i = 0; i < a.length; i++) {
    const c = target - a[i];
    if (m.has(c)) return [m.get(c), i];
    m.set(a[i], i);
  }
  return null;
}
```

- **Time:** O(n), **Space:** O(n)  
- **Dry run:** `[2,7,11,15]`, t=9 → i=0 store 2→0; i=1 c=2 found → `[0,1]`  
- **Follow-up:** **Sorted** + space O(1) → two pointers from both ends.

---

### MEDIUM 6 — Maximum subarray (Kadane)

**Brute:** All subarrays O(n³) or O(n²).

**Optimal:** `best = max(best + x, x)` each step.

```javascript
function maxSubarray(a) {
  let cur = a[0], best = a[0];
  for (let i = 1; i < a.length; i++) {
    cur = Math.max(a[i], cur + a[i]);
    best = Math.max(best, cur);
  }
  return best;
}
```

- **Time:** O(n), **Space:** O(1)  
- **Dry run:** `[-2,1,-3,4,-1,2,1]` best tracks max ending sum  
- **Follow-up:** Return **indices**; all **negative** array → largest single element.

---

### MEDIUM 7 — Move zeros to end (stable)

**Brute:** New array O(n) space.

**Optimal:** `k` = next non-zero slot.

```javascript
function moveZeros(a) {
  let k = 0;
  for (const x of a) if (x !== 0) a[k++] = x;
  while (k < a.length) a[k++] = 0;
  return a;
}
```

- **Time:** O(n), **Space:** O(1)  
- **Follow-up:** **Two-pointer swap** (not stable) in fewer writes.

---

### MEDIUM 8 — Rotate array right by k

**Brute:** New array place `(i+k)%n` — O(n) space.

**Optimal:** Reverse triple: reverse all, reverse `[0,k-1]`, reverse `[k,n-1]` (after `k%=n`).

```javascript
function rotateRight(a, k) {
  const n = a.length;
  if (!n) return a;
  k %= n;
  const rev = (l, r) => {
    while (l < r) [a[l], a[r]] = [a[r], a[l]], l++, r--;
  };
  rev(0, n - 1); rev(0, k - 1); rev(k, n - 1);
  return a;
}
```

- **Time:** O(n), **Space:** O(1)  
- **Dry run:** `[1,2,3,4,5]`, k=2 → aim `[4,5,1,2,3]`  
- **Follow-up:** **Left** rotate = different slice reverse.

---

### MEDIUM 9 — Missing number (0..n in array length n)

**Brute:** Sort and scan O(n log n).

**Optimal:** Expected sum `n(n+1)/2` minus actual sum — O(n) O(1) space (watch integer overflow in theory).

```javascript
function missingNumber(a) {
  const n = a.length;
  let s = (n * (n + 1)) / 2;
  for (const x of a) s -= x;
  return s;
}
```

**Alt:** XOR all indices and values.

- **Follow-up:** **Two missing** — bitset or math (harder variant).

---

### HARD 10 — Product of array except self (no division)

**Brute:** Multiply all each index O(n²).

**Optimal:** `left[i]` product of all left; multiply by running **right** product.

```javascript
function productExceptSelf(a) {
  const n = a.length;
  const out = Array(n).fill(1);
  let L = 1;
  for (let i = 0; i < n; i++) {
    out[i] = L;
    L *= a[i];
  }
  let R = 1;
  for (let i = n - 1; i >= 0; i--) {
    out[i] *= R;
    R *= a[i];
  }
  return out;
}
```

- **Time:** O(n), **Space:** O(1) excluding output  
- **Dry run:** `[1,2,3,4]` → out `[24,12,8,6]`  
- **Follow-up:** **Zeros** — if two zeros, all products 0; if one zero, only that index non-zero.

---

### HARD 11 — Subarray sum equals K

**Brute:** All subarrays O(n²).

**Optimal:** Prefix sum + map **count of prefix sums**; if `pre - K` seen, add its count.

```javascript
function subarraySum(a, K) {
  const m = new Map([[0, 1]]);
  let pre = 0, ans = 0;
  for (const x of a) {
    pre += x;
    ans += m.get(pre - K) ?? 0;
    m.set(pre, (m.get(pre) ?? 0) + 1);
  }
  return ans;
}
```

- **Time:** O(n), **Space:** O(n)  
- **Dry run:** `[1,2,3]`, K=3 → subarrays `[1,2]`, `[3]` count 2  
- **Follow-up:** Longest/shortest length with sum K — track **indices** in map.

---

### HARD 12 — Longest consecutive sequence (numbers unsorted)

**Brute:** Sort O(n log n).

**Optimal:** `Set` of nums; start streak only if `num-1` **not** in set.

```javascript
function longestConsecutive(a) {
  const s = new Set(a);
  let best = 0;
  for (const x of s) {
    if (s.has(x - 1)) continue;
    let y = x, len = 0;
    while (s.has(y)) y++, len++;
    best = Math.max(best, len);
  }
  return best;
}
```

- **Time:** O(n) amortized (each element in few while iterations)  
- **Follow-up:** **Streaming** — harder; batch or use BST (out of hash-only scope).

---

## 4. Strings Interview Questions (Complete Set)

Treat string as array of characters for many problems (`s.split('')` or loop `s[i]`).

### EASY 1 — Reverse string

Same as array reverse; in JS strings are immutable — use array of chars or build with loop.

```javascript
const rev = s => [...s].reverse().join("");
```

---

### EASY 2 — Palindrome check (alphanumeric optional)

Normalize two pointers:

```javascript
function isPal(s) {
  let i = 0, j = s.length - 1;
  while (i < j) {
    if (s[i] !== s[j]) return false;
    i++; j--;
  }
  return true;
}
```

---

### EASY 3 — Count vowels

```javascript
const V = new Set("aeiouAEIOU");
const vowels = s => [...s].filter(c => V.has(c)).length;
```

---

### MEDIUM 4 — Valid anagram

**Frequency equal** — sort O(n log n) or **hash count** O(n).

```javascript
function isAnagram(a, b) {
  if (a.length !== b.length) return false;
  const m = new Map();
  for (const c of a) m.set(c, (m.get(c) ?? 0) + 1);
  for (const c of b) {
    if (!(m.has(c))) return false;
    m.set(c, m.get(c) - 1);
    if (m.get(c) === 0) m.delete(c);
  }
  return m.size === 0;
}
```

**Unicode:** For full Unicode code points use **array from** `for ... of` over string.

---

### MEDIUM 5 — Longest substring without repeating characters

**Sliding window + freq map/set**; shrink when duplicate.

```javascript
function lengthOfLongestSubstring(s) {
  const last = new Map();
  let l = 0, best = 0;
  for (let r = 0; r < s.length; r++) {
    const c = s[r];
    if (last.has(c) && last.get(c) >= l) l = last.get(c) + 1;
    last.set(c, r);
    best = Math.max(best, r - l + 1);
  }
  return best;
}
```

- **Time:** O(n), **Space:** O(min(n, alphabet))  
- **Follow-up:** Return **substring** itself, not only length.

---

### MEDIUM 6 — String compression (`aabbbcccc` → `a2b3c4` or count run)

```javascript
function compress(s) {
  let out = "";
  let i = 0;
  while (i < s.length) {
    let j = i;
    while (j < s.length && s[j] === s[i]) j++;
    out += s[i] + String(j - i);
    i = j;
  }
  return out;
}
```

---

### HARD 7 — Longest palindromic substring

**Center expand** O(n²) (optimal DP O(n²) similar; Manacher out of scope).

```javascript
function longestPalindrome(s) {
  let best = "";
  const expand = (l, r) => {
    while (l >= 0 && r < s.length && s[l] === s[r]) l--, r++;
    return s.slice(l + 1, r);
  };
  for (let i = 0; i < s.length; i++) {
    const odd = expand(i, i), even = expand(i, i + 1);
    if (odd.length > best.length) best = odd;
    if (even.length > best.length) best = even;
  }
  return best;
}
```

---

### HARD 8 — Group anagrams

**Key:** sorted string OR **frequency tuple** as key.

```javascript
function groupAnagrams(strs) {
  const m = new Map();
  for (const w of strs) {
    const k = [...w].sort().join("");
    if (!m.has(k)) m.set(k, []);
    m.get(k).push(w);
  }
  return [...m.values()];
}
```

---

### HARD 9 — Minimum window substring (intro)

**Goal:** Smallest window in `s` containing all chars of `t` (with required counts).

**Idea:** Sliding window + **need** / **have** counts for chars in `t`; expand until valid, shrink while valid, track best.

**Interview script:** “I keep a map of **required counts** for `t`, a **formed** counter for how many unique chars satisfied, move `right` to include chars, move `left` to tighten.” **Complexity:** O(|s|+|t|) with fixed alphabet.

**Full code omitted** — implement after mastering **variable window + hash map**; trace on `s=ADOBECODEBANC`, `t=ABC`.

---

## 5. HashMap / Hashing Interview Questions (CORE SECTION)

### Must-know list (mapped to patterns)

| Problem | Hashing role |
|---------|----------------|
| **Two Sum** | Store complement → index |
| **First non-repeating character** | Count then second pass order O(n) |
| **Frequency of elements** | `Map` value→count |
| **Subarray sum = K** | Prefix sum frequency |
| **Group anagrams** | Key from sorted word or 26-char vector |
| **Longest consecutive** | `Set` for O(1) membership |

### First non-repeating character

```javascript
function firstUniq(s) {
  const m = new Map();
  for (const c of s) m.set(c, (m.get(c) ?? 0) + 1);
  for (let i = 0; i < s.length; i++) if (m.get(s[i]) === 1) return i;
  return -1;
}
```

### Why HashMap reduces time

**Lookup** average **O(1)** vs scanning array **O(n)** — turns “find partner” inner loop into **one pass**.

### HashMap vs array as frequency table

| Use **array index** | Use **Map / object** |
|---------------------|----------------------|
| Chars `'a'-'z'` or small int range | Sparse keys, Unicode, strings as keys |
| O(1) index, no hashing overhead | Flexible keys |

### Collisions (basic)

Two keys map to same bucket → chain or open addressing. **Interview:** “Average O(1) assumes **good hash**; worst O(n) if all collide.”

---

## 6. Combined Patterns (VERY IMPORTANT)

| Mix | Example problems |
|-----|------------------|
| **Sliding window + HashMap** | Longest unique substring, min window substring |
| **Prefix sum + HashMap** | Subarray sum K, **contiguous array** balance |
| **Two pointers + sorting** | Two sum II sorted, 3Sum (pair → third with hash or two pointers) |

**3Sum sketch (pattern nudge):** Sort; fix `i`, two pointers `l,r` for target `-a[i]` — **hash** alternative exists but two-pointer common after sort.

---

## 7. Step-by-Step Problem Solving Framework

1. **Restate** I/O and constraints (empty? negatives? sorted?).  
2. **Brute force** complexity out loud.  
3. Ask: **What am I re-scanning each step?**  
4. Pick **pattern**: pointers / window / prefix+map / sort.  
5. Code **happy path**, then **edges**.  
6. **Dry run** tiny input.  
7. State **time/space**.

---

## 8. Dry Run Training Section

### Two Sum — `[3,2,4]`, target `6`

| i | x | need=6-x | map (before) | action |
|---|----|----------|----------------|--------|
| 0 | 3 | 3 | {} | set 3→0 |
| 1 | 2 | 4 | {3:0} | set 2→1 |
| 2 | 4 | 2 | {3:0,2:1} | 2 seen → return **[1,2]** |

---

### Longest substring without repeat — `"abcba"`

| r | char | last index map | l moves | best |
|---|------|----------------|---------|------|
| 0 | a | a:0 | 0 | 1 |
| 1 | b | a:0,b:1 | 0 | 2 |
| 2 | c | ...c:2 | 0 | 3 |
| 3 | b | b exists at 1 ≥ l → l=2 | 2 | max(3,2)=3 |
| 4 | a | a at 0 < l skip; or if at 0 need rule: a at 0 < l so l stays, update a:4 | 2 | 3 |

(Trace carefully in interview—your code’s `last` update rule handles this.)

---

### Subarray sum = K — `[1,2,3]`, K=`3`

| step | pre | add `m.get(pre-K)` | map after |
|------|-----|-------------------|-----------|
| start | 0 | — | {0:1} |
| +1 | 1 | m.get(-2)=0 | {0:1,1:1} |
| +2 | 3 | m.get(0)=1 → ans+1 | {0:1,1:1,3:1} |
| +3 | 6 | m.get(3)=1 → ans+1 | ... |

Total **2** subarrays: `[1,2]` and `[3]`.

---

## 9. Follow-Up Questions (Interview Trick Section)

| Twist | Typical move |
|-------|---------------|
| **Sorted array** | Binary search; two pointers for pairs |
| **Memory limited** | Sort in place; bit tricks; streaming approximation |
| **O(1) extra space** | Often **sort first** or **mutate input** permitted |
| **Streaming input** | Sliding window / deque; **cannot** store all → approximate or window stats |
| **Duplicates / all solutions** | Skip duplicates after sort (3Sum style) |
| **Immutable string** | Use array builder |

---

## 10. Common Mistakes Students Make

| Mistake | Fix |
|---------|-----|
| Jump to optimal without brute | Say brute **once**, then optimize |
| Wrong empty/null | Guard `n===0` |
| Off-by-one in window | Define window **[l,r)** vs inclusive |
| Mutating while iterating | Copy indices carefully |
| `Map` vs object confusion | Prefer `Map` when keys are **non-string** or frequent deletes |
| Not updating **left** pointer in sliding window | Stuck in infinite expand |

---

## 11. Coding Practice Plan

| Days | Focus |
|------|--------|
| **1–3** | Max, reverse, dedupe sorted, Kadane, move zeros, two sum |
| **4–6** | Fixed/variable window intro, subarray sum K, prefix habits |
| **7–10** | Anagram set, longest unique substr, group anagrams, rotate, product except self, longest consecutive |

**Daily:** 2 problems timed (30–40 min), **1** redo blind next day.

---

## 12. Interview Simulation Section

**Interviewer:** “Given an array, find **two** numbers that sum to target.”

**You:** “Brute force check all pairs — **O(n²)**. I can improve with a **hash map** storing seen values. While scanning, if `target - current` exists, I return both indices — **O(n)** time and space.”

**Interviewer:** “What if sorted?”

**You:** “**Two pointers** at both ends — O(n) time, **O(1)** extra space.”

**Interviewer:** “What if duplicates allowed and you need all unique pairs?”

**You:** “Sort + two pointers with **skip duplicates** when moving — still array + pointer pattern.”

---

## 13. Optimization Mindset

### O(n²) → O(n) playbook

1. Is there a **complement** or **target difference**? → **HashMap**  
2. Is array **sorted** or can I sort? → **Two pointers**  
3. Is it **range sum**? → **Prefix sum + map**  
4. Is it **contiguous best**? → **Kadane / window**

### Why hashing is powerful

Turns “search the past” into **O(1) average lookup**.

### Patterns repeat

**Leetcode clustering:** many tagged “Hash Table,” “Two Pointers,” “Sliding Window” share the **same skeleton**.

---

## 14. Final Revision Sheet

### Patterns → grab bag

| Pattern | Know one flagship problem |
|---------|---------------------------|
| Two pointers opposite | Two sum II |
| Two pointers same | Remove dup sorted |
| Sliding window | Longest unique substr |
| Prefix + map | Subarray sum K |
| Hash freq | Anagram, group anagrams |
| Kadane | Max subarray |

### Complexity quick table

| Problem | Target |
|---------|--------|
| Two sum (hash) | O(n) / O(n) |
| Kadane | O(n) / O(1) |
| Longest unique | O(n) / O(alphabet) |
| Subarray sum K | O(n) / O(n) |
| Longest consecutive | O(n) |
| Product except self | O(n) / O(1) extra |

### Cheat sheet lines

- “**What am I looking up from earlier steps?**” → HashMap  
- “**Can I sort without breaking meaning?**” → Two pointers  
- “**Contiguous constraint?**” → Window or prefix  

---

**End of guide.** Drill **Section 8** until traces are automatic; then add **medium window** and **min window** last.
