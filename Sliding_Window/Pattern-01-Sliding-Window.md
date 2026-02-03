# Pattern 01 — Sliding Window (Python)

The Sliding Window pattern is a core algorithmic technique used to efficiently process **contiguous segments** of data such as subarrays in arrays or substrings in strings.

This pattern exists to solve a specific inefficiency: **repeated computation over overlapping ranges**.  
Many problems appear simple at first, but brute-force solutions quickly become too slow when input sizes grow.

Sliding Window provides a structured way to move through data **once**, while maintaining enough information to compute answers incrementally.

---

## 1) The Fundamental Problem Sliding Window Solves

A large class of problems ask you to:
- examine every contiguous subarray or substring
- compute some property (sum, length, count, frequency)
- return the best (maximum, minimum, longest, shortest)

The naive approach is to:
- fix a starting index
- expand an ending index
- recompute the property for every range independently

This approach is **logically correct** but **computationally wasteful**.

---

## 2) Why Brute Force Is Inefficient

Consider two consecutive windows of the same size:

- Window 1 covers indices `[i … j]`
- Window 2 covers indices `[i+1 … j+1]`

Most of the elements are the same.  
However, brute force recomputes everything from scratch.

This leads to:
- unnecessary repeated work
- nested loops
- time complexity often degrading to `O(n²)` or worse

Sliding Window exists to eliminate this redundancy.

---

## 3) The Sliding Window Mental Model

A sliding window is **not** a data structure.  
It is a **way of thinking** about a contiguous region in the input.

Every sliding window solution is built from three parts:

1. **Left pointer** – marks where the window starts  
2. **Right pointer** – marks where the window ends  
3. **Window state** – information about the current window

The state is the most important part.

Examples of window state:
- running sum
- count of zeros
- frequency of characters
- number of distinct elements
- number of matched characters

The window moves forward, and the state is updated incrementally.

---

## 4) Incremental Update Principle

The key principle behind Sliding Window is:

> When the window moves forward, only two things change:
> - one element enters the window
> - one element leaves the window

Instead of recomputing the entire window:
- subtract the element leaving
- add the element entering
- update the state accordingly

This principle is what converts quadratic solutions into linear ones.

---

## 5) Fixed-Size Sliding Window

In fixed-size sliding window problems, the window length is constant.

Typical signals:
- “subarray of size k”
- “average of k elements”
- “maximum sum of k consecutive elements”

Characteristics:
- window size never changes
- window slides one step at a time
- answer is updated only when window size equals k

Complexity:
- Time: `O(n)`
- Space: `O(1)`

---

## 6) Variable-Size Sliding Window

In variable-size sliding window problems, the window size adapts dynamically.

Typical signals:
- “smallest subarray with sum ≥ S”
- “longest substring with at most K distinct characters”
- “replace at most K elements”

Characteristics:
- right pointer expands to explore
- left pointer shrinks to restore validity
- window size is a result, not a decision

Complexity:
- Time: `O(n)`
- Space: `O(1)` or `O(k)`

---

## 7) Expansion vs Shrinking Logic

Every sliding window algorithm revolves around two questions:

1. When should the window expand?
2. When should the window shrink?

The answer depends on whether the problem asks for:
- longest valid window
- smallest valid window

Mistakes here are the most common source of bugs.

---

## 8) Sliding Window with Memory (Frequency Maps)

For string problems, numeric state is not enough.

You often need to track:
- how many times a character appears
- how many distinct characters exist
- whether a pattern has been fully matched

This is done using:
- hash maps
- fixed-size arrays (for known alphabets)

The window state becomes a **data structure**, not just a number.

---

## 9) Why Sliding Window Is O(n)

Sliding Window works in linear time because:

- the right pointer moves forward at most `n` times
- the left pointer moves forward at most `n` times
- no pointer ever moves backward

Each element is processed a constant number of times.

---

## 10) When Sliding Window Does NOT Apply

Sliding Window often fails when:
- negative numbers break monotonic behavior
- shrinking the window does not move closer to validity

In such cases, other patterns like Prefix Sum are required.

---

## 11) Common Conceptual Mistakes

- Treating the window as static data
- Updating answers before window validity
- Forgetting to clean up window state
- Assuming sliding window applies universally

---

## 12) Minimal Python Templates

These are **NOT problem solutions** — they are **templates**.

---

## Pattern A — Fixed-Size Window (Aggregation)

### Use when:
- subarray size is exactly `k`
- sum / average / max / min

### Invariant:
Window size is always `k`.

```python
def fixed_size_window(nums, k):
    left = 0
    window_sum = 0
    result = None

    for right in range(len(nums)):
        window_sum += nums[right]

        if right >= k - 1:
            # update result using window_sum
            # result = ...
            window_sum -= nums[left]
            left += 1

    return result
```

Time: `O(n)`  
Space: `O(1)`

---

## Pattern B — Variable Window (Smallest Valid Window)

### Use when:
- find smallest subarray / substring
- condition like `sum >= target`

### Invariant:
Shrink window **while valid**.

```python
def smallest_window(nums, target):
    left = 0
    window_sum = 0
    best = float("inf")

    for right in range(len(nums)):
        window_sum += nums[right]

        while window_sum >= target:
            best = min(best, right - left + 1)
            window_sum -= nums[left]
            left += 1

    return 0 if best == float("inf") else best
```

---

## Pattern C — Variable Window (Longest Valid Window)

### Use when:
- longest substring / subarray
- constraints like "at most K"

### Invariant:
Shrink only when window becomes invalid.

```python
def longest_window(nums):
    left = 0
    best = 0

    for right in range(len(nums)):
        # update window state

        while False:  # invalid condition
            # remove nums[left]
            left += 1

        best = max(best, right - left + 1)

    return best
```

---

## Pattern D — Frequency-Based Window (Distinct Elements)

### Use when:
- strings
- at most / exactly K distinct characters

```python
from collections import defaultdict

def freq_window(s, k):
    left = 0
    freq = defaultdict(int)
    best = 0

    for right, ch in enumerate(s):
        freq[ch] += 1

        while len(freq) > k:
            freq[s[left]] -= 1
            if freq[s[left]] == 0:
                del freq[s[left]]
            left += 1

        best = max(best, right - left + 1)

    return best
```

---

## Pattern E — Replacement Window

### Use when:
- allowed to replace at most `k`
- longest repeating substring / ones

### Key Formula:
```
window_size - max_frequency <= k
```

```python
from collections import defaultdict

def replacement_window(s, k):
    left = 0
    freq = defaultdict(int)
    max_freq = 0
    best = 0

    for right, ch in enumerate(s):
        freq[ch] += 1
        max_freq = max(max_freq, freq[ch])

        if (right - left + 1) - max_freq > k:
            freq[s[left]] -= 1
            left += 1

        best = max(best, right - left + 1)

    return best
```

---

## Pattern F — Fixed Window + Pattern Matching (Anagram / Permutation)

```python
from collections import Counter

def permutation_window(s, p):
    need = Counter(p)
    matched = 0
    left = 0

    for right, ch in enumerate(s):
        if ch in need:
            need[ch] -= 1
            if need[ch] == 0:
                matched += 1

        if right >= len(p) - 1:
            if matched == len(need):
                return True

            left_char = s[left]
            left += 1
            if left_char in need:
                if need[left_char] == 0:
                    matched -= 1
                need[left_char] += 1

    return False
```

---

## Pattern G — Minimum Window Containing Pattern

```python
from collections import Counter

def min_window(s, t):
    need = Counter(t)
    missing = len(t)
    left = 0
    start = 0
    min_len = float("inf")

    for right, ch in enumerate(s):
        if need[ch] > 0:
            missing -= 1
        need[ch] -= 1

        while missing == 0:
            if right - left + 1 < min_len:
                min_len = right - left + 1
                start = left

            need[s[left]] += 1
            if need[s[left]] > 0:
                missing += 1
            left += 1

    return "" if min_len == float("inf") else s[start:start+min_len]
```
| Question asks         | Use this pattern               |
| --------------------- | ------------------------------ |
| Size exactly K        | Fixed-size window              |
| Smallest valid window | Variable + shrink while valid  |
| Longest valid window  | Variable + shrink when invalid |
| Distinct characters   | Frequency window               |
| Replace ≤ K           | Replacement window             |
| Anagram / permutation | Fixed-length + frequency       |
| Must contain pattern  | Min-window pattern             |
---

## 13) Practice Problems — Sliding Window

### Easy (5)
1. https://leetcode.com/problems/maximum-average-subarray-i/
2. https://leetcode.com/problems/max-consecutive-ones/
3. https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/
4. https://leetcode.com/problems/count-number-of-nice-subarrays/
5. https://www.geeksforgeeks.org/find-subarray-with-given-sum/

### Basic / Lower-Medium (5)
6. https://www.geeksforgeeks.org/find-maximum-sum-subarray-of-size-k/
7. https://www.geeksforgeeks.org/smallest-subarray-with-sum-greater-than-x/
8. https://leetcode.com/problems/longest-substring-without-repeating-characters/
9. https://leetcode.com/problems/longest-substring-with-at-most-two-distinct-characters/
10. https://leetcode.com/problems/binary-subarrays-with-sum/

### Medium (5)
11. https://leetcode.com/problems/fruit-into-baskets/
12. https://leetcode.com/problems/minimum-size-subarray-sum/
13. https://leetcode.com/problems/longest-repeating-character-replacement/
14. https://leetcode.com/problems/permutation-in-string/
15. https://leetcode.com/problems/find-all-anagrams-in-a-string/

### Hard (5)
16. https://leetcode.com/problems/minimum-window-substring/
17. https://leetcode.com/problems/substring-with-concatenation-of-all-words/
18. https://leetcode.com/problems/max-consecutive-ones-iii/
19. https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/
20. https://leetcode.com/problems/count-subarrays-with-fixed-bounds/
