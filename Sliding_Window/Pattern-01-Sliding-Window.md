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
## Pattern 1 — Fixed-Size Sliding Window

### When to Use
Use this pattern when the window size is explicitly fixed by the problem.

Typical signals:
- subarray or substring of size k
- k consecutive elements
- |i - j| <= k
- average / sum of k elements

### Invariant
The window size never changes.

right - left + 1 == k

### Mental Model
- Slide the window one step at a time
- One element enters the window
- One element leaves the window
- Update window state incrementally

### Window State Examples
- running sum
- set (existence check)
- frequency map
- count

### Template
```python
def fixed_size_window(nums, k):
    left = 0
    window_state = initial_state
    result = initial_value

    for right in range(len(nums)):
        add nums[right] to window_state

        if right >= k - 1:
            update result using window_state
            remove nums[left] from window_state
            left += 1

    return result
```
---

## Pattern 2 — Variable-Size Sliding Window

### When to Use
Use this pattern when the window size is not fixed and must be discovered.

Typical signals:
- longest
- smallest
- at most k
- at least target
- replace at most k

### Invariant
Window must remain valid according to a condition.

### Mental Model
- Expand using right pointer
- Shrink using left pointer to restore validity
- Window size is a result, not a decision

### Template
```python
def variable_size_window(nums):
    left = 0
    window_state = initial_state
    best = initial_value

    for right in range(len(nums)):
        add nums[right] to window_state

        while window_invalid(window_state):
            remove nums[left] from window_state
            left += 1

        update best using right - left + 1

    return best
```
---

## 13) Practice Problems — Sliding Window

### Easy (5)
1. https://leetcode.com/problems/maximum-average-subarray-i/
2. https://leetcode.com/problems/contains-duplicate-ii/
3. https://leetcode.com/problems/number-of-sub-arrays-of-size-k-and-average-greater-than-or-equal-to-threshold/
4. https://leetcode.com/problems/defuse-the-bomb/
5. https://leetcode.com/problems/diet-plan-performance/

### Basic / Lower-Medium (5)
6. https://leetcode.com/problems/minimum-size-subarray-sum/
7. https://leetcode.com/problems/max-consecutive-ones-iii/
8. https://leetcode.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/
9. https://leetcode.com/problems/longest-subarray-of-1s-after-deleting-one-element/
10. https://leetcode.com/problems/get-equal-substrings-within-budget/

### Medium (5)
11. https://leetcode.com/problems/longest-substring-without-repeating-characters/
12. https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/
13. https://leetcode.com/problems/fruit-into-baskets/
14. https://leetcode.com/problems/subarrays-with-k-different-integers/
15. https://leetcode.com/problems/find-all-anagrams-in-a-string/

### Hard (5)
16. https://leetcode.com/problems/minimum-window-substring/
17. https://leetcode.com/problems/substring-with-concatenation-of-all-words/
18. https://leetcode.com/problems/longest-repeating-character-replacement/
19. https://leetcode.com/problems/sliding-window-maximum/
20. https://leetcode.com/problems/minimum-number-of-k-consecutive-bit-flips/

