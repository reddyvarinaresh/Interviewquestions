# Data Structures & Algorithms — Complete Company Interview Prep
> Sources: LeetCode Discuss, Glassdoor, LinkedIn, Blind, GeeksforGeeks, Reddit r/cscareerquestions
> Companies: TCS, Wipro, Infosys, Cognizant, Accenture, Deloitte, EPAM, HCL, Capgemini, Persistent, Brillio, LTIMindtree, Optum, Carelon
> All solutions in Python | Ordered by frequency across company reports

---

## FREQUENCY MAP — WHAT COMPANIES ACTUALLY ASK

| Topic | TCS/Wipro/Infosys | Accenture/Deloitte/EPAM | Optum/Carelon | Frequency |
|-------|-------------------|------------------------|---------------|-----------|
| Arrays / Strings | ✅✅✅ | ✅✅✅ | ✅✅ | 🔴 Very High |
| Linked Lists | ✅✅✅ | ✅✅ | ✅ | 🔴 Very High |
| Stacks & Queues | ✅✅✅ | ✅✅ | ✅ | 🔴 Very High |
| HashMap / HashSet | ✅✅✅ | ✅✅✅ | ✅✅ | 🔴 Very High |
| Two Pointers | ✅✅ | ✅✅✅ | ✅✅ | 🔴 Very High |
| Sliding Window | ✅✅ | ✅✅✅ | ✅✅ | 🔴 High |
| Binary Search | ✅✅ | ✅✅✅ | ✅✅ | 🔴 High |
| Sorting | ✅✅✅ | ✅✅ | ✅ | 🔴 High |
| Recursion | ✅✅✅ | ✅✅ | ✅ | 🔴 High |
| Trees (BFS/DFS) | ✅✅ | ✅✅✅ | ✅✅ | 🟡 High |
| Dynamic Programming | ✅ | ✅✅✅ | ✅✅ | 🟡 Medium-High |
| Graphs | ✅ | ✅✅ | ✅ | 🟡 Medium |
| Heaps / Priority Queue | ✅ | ✅✅ | ✅ | 🟡 Medium |
| Tries | ✅ | ✅✅ | ✅ | 🟢 Lower |
| Bit Manipulation | ✅ | ✅ | ✅ | 🟢 Lower |

---

## SECTION 1: ARRAYS — Most Asked (Start Here)

### P1. Two Sum 🔴
**Problem:** Find indices of two numbers that add to target.
```python
def two_sum(nums: list[int], target: int) -> list[int]:
    seen = {}
    for i, n in enumerate(nums):
        complement = target - n
        if complement in seen:
            return [seen[complement], i]
        seen[n] = i
    return []

# Input: nums=[2,7,11,15], target=9  →  Output: [0,1]
# Time: O(n), Space: O(n)
```

---

### P2. Best Time to Buy and Sell Stock 🔴
**Problem:** Find max profit from one buy-sell transaction.
```python
def max_profit(prices: list[int]) -> int:
    min_price = float('inf')
    max_profit = 0
    for price in prices:
        min_price = min(min_price, price)
        max_profit = max(max_profit, price - min_price)
    return max_profit

# Input: [7,1,5,3,6,4]  →  Output: 5 (buy at 1, sell at 6)
# Time: O(n), Space: O(1)
```

---

### P3. Contains Duplicate 🔴
**Problem:** Return True if any value appears at least twice.
```python
def contains_duplicate(nums: list[int]) -> bool:
    return len(nums) != len(set(nums))

# Variant: find the duplicate
def find_duplicate(nums: list[int]) -> int:
    seen = set()
    for n in nums:
        if n in seen:
            return n
        seen.add(n)
    return -1

# Time: O(n), Space: O(n)
```

---

### P4. Maximum Subarray (Kadane's Algorithm) 🔴
**Problem:** Find contiguous subarray with largest sum.
```python
def max_subarray(nums: list[int]) -> int:
    max_sum = current = nums[0]
    for n in nums[1:]:
        current = max(n, current + n)
        max_sum = max(max_sum, current)
    return max_sum

# Input: [-2,1,-3,4,-1,2,1,-5,4]  →  Output: 6 ([4,-1,2,1])
# Time: O(n), Space: O(1)
```

---

### P5. Product of Array Except Self 🔴
**Problem:** Return array where each element is product of all others (no division).
```python
def product_except_self(nums: list[int]) -> list[int]:
    n = len(nums)
    result = [1] * n
    # Left pass: result[i] = product of all elements left of i
    prefix = 1
    for i in range(n):
        result[i] = prefix
        prefix *= nums[i]
    # Right pass: multiply product of all elements right of i
    suffix = 1
    for i in range(n - 1, -1, -1):
        result[i] *= suffix
        suffix *= nums[i]
    return result

# Input: [1,2,3,4]  →  Output: [24,12,8,6]
# Time: O(n), Space: O(1) excluding output
```

---

### P6. Maximum Product Subarray 🔴
```python
def max_product(nums: list[int]) -> int:
    max_prod = min_prod = result = nums[0]
    for n in nums[1:]:
        candidates = (n, max_prod * n, min_prod * n)
        max_prod = max(candidates)
        min_prod = min(candidates)
        result = max(result, max_prod)
    return result

# Input: [2,3,-2,4]  →  Output: 6
# Input: [-2,0,-1]   →  Output: 0
```

---

### P7. Find Minimum in Rotated Sorted Array 🔴
```python
def find_min(nums: list[int]) -> int:
    lo, hi = 0, len(nums) - 1
    while lo < hi:
        mid = (lo + hi) // 2
        if nums[mid] > nums[hi]:
            lo = mid + 1
        else:
            hi = mid
    return nums[lo]

# Input: [3,4,5,1,2]  →  Output: 1
# Time: O(log n)
```

---

### P8. Search in Rotated Sorted Array 🔴
```python
def search(nums: list[int], target: int) -> int:
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        if nums[mid] == target:
            return mid
        # Left half is sorted
        if nums[lo] <= nums[mid]:
            if nums[lo] <= target < nums[mid]:
                hi = mid - 1
            else:
                lo = mid + 1
        # Right half is sorted
        else:
            if nums[mid] < target <= nums[hi]:
                lo = mid + 1
            else:
                hi = mid - 1
    return -1

# Input: nums=[4,5,6,7,0,1,2], target=0  →  Output: 4
```

---

### P9. 3Sum 🔴
**Problem:** Find all unique triplets that sum to zero.
```python
def three_sum(nums: list[int]) -> list[list[int]]:
    nums.sort()
    result = []
    for i in range(len(nums) - 2):
        if i > 0 and nums[i] == nums[i - 1]:
            continue  # skip duplicates
        lo, hi = i + 1, len(nums) - 1
        while lo < hi:
            s = nums[i] + nums[lo] + nums[hi]
            if s == 0:
                result.append([nums[i], nums[lo], nums[hi]])
                while lo < hi and nums[lo] == nums[lo + 1]: lo += 1
                while lo < hi and nums[hi] == nums[hi - 1]: hi -= 1
                lo += 1; hi -= 1
            elif s < 0:
                lo += 1
            else:
                hi -= 1
    return result

# Input: [-1,0,1,2,-1,-4]  →  [[-1,-1,2],[-1,0,1]]
# Time: O(n²), Space: O(1)
```

---

### P10. Container With Most Water 🔴
```python
def max_area(height: list[int]) -> int:
    lo, hi = 0, len(height) - 1
    max_water = 0
    while lo < hi:
        water = min(height[lo], height[hi]) * (hi - lo)
        max_water = max(max_water, water)
        if height[lo] < height[hi]:
            lo += 1
        else:
            hi -= 1
    return max_water

# Input: [1,8,6,2,5,4,8,3,7]  →  Output: 49
# Time: O(n), Space: O(1)
```

---

### P11. Move Zeroes 🔴
```python
def move_zeroes(nums: list[int]) -> None:
    write = 0
    for n in nums:
        if n != 0:
            nums[write] = n
            write += 1
    while write < len(nums):
        nums[write] = 0
        write += 1

# Input: [0,1,0,3,12]  →  [1,3,12,0,0]  (in-place)
```

---

### P12. Merge Intervals 🔴
```python
def merge(intervals: list[list[int]]) -> list[list[int]]:
    intervals.sort(key=lambda x: x[0])
    merged = [intervals[0]]
    for start, end in intervals[1:]:
        if start <= merged[-1][1]:
            merged[-1][1] = max(merged[-1][1], end)
        else:
            merged.append([start, end])
    return merged

# Input: [[1,3],[2,6],[8,10],[15,18]]  →  [[1,6],[8,10],[15,18]]
```

---

### P13. Find All Duplicates in Array 🟡
```python
def find_duplicates(nums: list[int]) -> list[int]:
    result = []
    for n in nums:
        idx = abs(n) - 1
        if nums[idx] < 0:
            result.append(abs(n))
        else:
            nums[idx] = -nums[idx]
    return result

# Input: [4,3,2,7,8,2,3,1]  →  [2,3]
# O(n) time, O(1) extra space (uses sign as marker)
```

---

### P14. Rotate Array 🔴
```python
def rotate(nums: list[int], k: int) -> None:
    n = len(nums)
    k %= n
    nums[:] = nums[-k:] + nums[:-k]

# Alternative: three reverses (O(1) space)
def rotate_inplace(nums: list[int], k: int) -> None:
    k %= len(nums)
    nums.reverse()
    nums[:k] = nums[:k][::-1]
    nums[k:] = nums[k:][::-1]

# Input: [1,2,3,4,5,6,7], k=3  →  [5,6,7,1,2,3,4]
```

---

### P15. Subarray Sum Equals K 🔴
```python
from collections import defaultdict

def subarray_sum(nums: list[int], k: int) -> int:
    count = 0
    prefix = 0
    freq = defaultdict(int)
    freq[0] = 1
    for n in nums:
        prefix += n
        count += freq[prefix - k]
        freq[prefix] += 1
    return count

# Input: [1,1,1], k=2  →  Output: 2
# Time: O(n), Space: O(n)
```

---

## SECTION 2: STRINGS — Very Frequently Asked

### P16. Valid Anagram 🔴
```python
from collections import Counter

def is_anagram(s: str, t: str) -> bool:
    return Counter(s) == Counter(t)

# Without Counter:
def is_anagram_v2(s: str, t: str) -> bool:
    if len(s) != len(t): return False
    count = [0] * 26
    for a, b in zip(s, t):
        count[ord(a) - ord('a')] += 1
        count[ord(b) - ord('a')] -= 1
    return all(c == 0 for c in count)

# "anagram","nagaram"  →  True
```

---

### P17. Valid Palindrome 🔴
```python
def is_palindrome(s: str) -> bool:
    s = ''.join(c.lower() for c in s if c.isalnum())
    return s == s[::-1]

# Two-pointer version (O(1) space):
def is_palindrome_tp(s: str) -> bool:
    lo, hi = 0, len(s) - 1
    while lo < hi:
        while lo < hi and not s[lo].isalnum(): lo += 1
        while lo < hi and not s[hi].isalnum(): hi -= 1
        if s[lo].lower() != s[hi].lower(): return False
        lo += 1; hi -= 1
    return True

# "A man, a plan, a canal: Panama"  →  True
```

---

### P18. Longest Substring Without Repeating Characters 🔴
```python
def length_of_longest_substring(s: str) -> int:
    seen = {}
    start = max_len = 0
    for i, ch in enumerate(s):
        if ch in seen and seen[ch] >= start:
            start = seen[ch] + 1
        seen[ch] = i
        max_len = max(max_len, i - start + 1)
    return max_len

# "abcabcbb"  →  3 ("abc")
# "pwwkew"    →  3 ("wke")
# Time: O(n), Space: O(min(m,n)) where m = charset size
```

---

### P19. Longest Repeating Character Replacement 🟡
```python
def character_replacement(s: str, k: int) -> int:
    count = {}
    lo = max_count = result = 0
    for hi, ch in enumerate(s):
        count[ch] = count.get(ch, 0) + 1
        max_count = max(max_count, count[ch])
        # Window size - max freq > k means we need to shrink
        if (hi - lo + 1) - max_count > k:
            count[s[lo]] -= 1
            lo += 1
        result = max(result, hi - lo + 1)
    return result

# "AABABBA", k=1  →  4 ("AABA" or "ABBA")
```

---

### P20. Group Anagrams 🔴
```python
from collections import defaultdict

def group_anagrams(strs: list[str]) -> list[list[str]]:
    groups = defaultdict(list)
    for s in strs:
        key = tuple(sorted(s))
        groups[key].append(s)
    return list(groups.values())

# ["eat","tea","tan","ate","nat","bat"]
# → [["eat","tea","ate"],["tan","nat"],["bat"]]
# Time: O(n·k·log k) where k = max string length
```

---

### P21. Valid Parentheses 🔴
```python
def is_valid(s: str) -> bool:
    stack = []
    pairs = {')': '(', ']': '[', '}': '{'}
    for ch in s:
        if ch in '([{':
            stack.append(ch)
        elif ch in ')]}':
            if not stack or stack[-1] != pairs[ch]:
                return False
            stack.pop()
    return len(stack) == 0

# "()"  →  True, "()[]{}"  →  True, "(]"  →  False
```

---

### P22. Generate Parentheses 🟡
```python
def generate_parentheses(n: int) -> list[str]:
    result = []
    def backtrack(s, open_c, close_c):
        if len(s) == 2 * n:
            result.append(s)
            return
        if open_c < n:
            backtrack(s + '(', open_c + 1, close_c)
        if close_c < open_c:
            backtrack(s + ')', open_c, close_c + 1)
    backtrack('', 0, 0)
    return result

# n=3  →  ["((()))","(()())","(())()","()(())","()()()"]
```

---

### P23. Minimum Window Substring 🟡
```python
from collections import Counter

def min_window(s: str, t: str) -> str:
    need = Counter(t)
    missing = len(t)
    lo = start = end = 0
    for hi, ch in enumerate(s, 1):
        if need[ch] > 0:
            missing -= 1
        need[ch] -= 1
        if missing == 0:
            while need[s[lo]] < 0:
                need[s[lo]] += 1
                lo += 1
            if not end or hi - lo < end - start:
                start, end = lo, hi
            need[s[lo]] += 1
            missing += 1
            lo += 1
    return s[start:end]

# s="ADOBECODEBANC", t="ABC"  →  "BANC"
```

---

### P24. Encode and Decode Strings 🟡
```python
def encode(strs: list[str]) -> str:
    return ''.join(f"{len(s)}#{s}" for s in strs)

def decode(s: str) -> list[str]:
    result, i = [], 0
    while i < len(s):
        j = s.index('#', i)
        length = int(s[i:j])
        result.append(s[j + 1: j + 1 + length])
        i = j + 1 + length
    return result

# ["lint","code","love","you"]  →  encoded  →  decoded back
```

---

### P25. Longest Palindromic Substring 🟡
```python
def longest_palindrome(s: str) -> str:
    result = ""
    def expand(lo, hi):
        nonlocal result
        while lo >= 0 and hi < len(s) and s[lo] == s[hi]:
            lo -= 1; hi += 1
        if hi - lo - 1 > len(result):
            result = s[lo + 1:hi]
    for i in range(len(s)):
        expand(i, i)     # odd length
        expand(i, i + 1) # even length
    return result

# "babad"  →  "bab" or "aba"
# "cbbd"   →  "bb"
# Time: O(n²), Space: O(1)
```

---

### P26. Palindromic Substrings Count 🟡
```python
def count_substrings(s: str) -> int:
    count = 0
    def expand(lo, hi):
        nonlocal count
        while lo >= 0 and hi < len(s) and s[lo] == s[hi]:
            count += 1
            lo -= 1; hi += 1
    for i in range(len(s)):
        expand(i, i)
        expand(i, i + 1)
    return count

# "abc"  →  3, "aaa"  →  6
```

---

### P27. String to Integer (atoi) 🟡
```python
def my_atoi(s: str) -> int:
    s = s.lstrip()
    if not s: return 0
    sign = 1
    i = 0
    if s[0] in '+-':
        sign = -1 if s[0] == '-' else 1
        i = 1
    result = 0
    while i < len(s) and s[i].isdigit():
        result = result * 10 + int(s[i])
        i += 1
    result *= sign
    INT_MIN, INT_MAX = -2**31, 2**31 - 1
    return max(INT_MIN, min(INT_MAX, result))
```

---

### P28. Longest Common Prefix 🔴
```python
def longest_common_prefix(strs: list[str]) -> str:
    if not strs: return ""
    prefix = strs[0]
    for s in strs[1:]:
        while not s.startswith(prefix):
            prefix = prefix[:-1]
        if not prefix: return ""
    return prefix

# ["flower","flow","flight"]  →  "fl"
```

---

## SECTION 3: LINKED LISTS — Asked in Every Company

### P29. Reverse Linked List 🔴
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def reverse_list(head: ListNode) -> ListNode:
    prev, curr = None, head
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt
    return prev

# Recursive version:
def reverse_recursive(head: ListNode) -> ListNode:
    if not head or not head.next:
        return head
    new_head = reverse_recursive(head.next)
    head.next.next = head
    head.next = None
    return new_head
```

---

### P30. Detect Cycle in Linked List 🔴
```python
def has_cycle(head: ListNode) -> bool:
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False

# Find the node where cycle begins:
def detect_cycle(head: ListNode) -> ListNode:
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            slow = head
            while slow is not fast:
                slow = slow.next
                fast = fast.next
            return slow
    return None
```

---

### P31. Merge Two Sorted Lists 🔴
```python
def merge_two_lists(l1: ListNode, l2: ListNode) -> ListNode:
    dummy = curr = ListNode(0)
    while l1 and l2:
        if l1.val <= l2.val:
            curr.next = l1
            l1 = l1.next
        else:
            curr.next = l2
            l2 = l2.next
        curr = curr.next
    curr.next = l1 or l2
    return dummy.next
```

---

### P32. Merge K Sorted Lists 🟡
```python
import heapq

def merge_k_lists(lists: list[ListNode]) -> ListNode:
    heap = []
    for i, node in enumerate(lists):
        if node:
            heapq.heappush(heap, (node.val, i, node))
    dummy = curr = ListNode(0)
    while heap:
        val, i, node = heapq.heappop(heap)
        curr.next = node
        curr = curr.next
        if node.next:
            heapq.heappush(heap, (node.next.val, i, node.next))
    return dummy.next
```

---

### P33. Remove Nth Node From End 🔴
```python
def remove_nth_from_end(head: ListNode, n: int) -> ListNode:
    dummy = ListNode(0, head)
    fast = slow = dummy
    for _ in range(n + 1):
        fast = fast.next
    while fast:
        slow = slow.next
        fast = fast.next
    slow.next = slow.next.next
    return dummy.next

# One-pass with two pointers n apart
```

---

### P34. Reorder List 🟡
```python
def reorder_list(head: ListNode) -> None:
    # Find middle
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    # Reverse second half
    prev, curr = None, slow.next
    slow.next = None
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt
    # Merge two halves
    l1, l2 = head, prev
    while l2:
        tmp1, tmp2 = l1.next, l2.next
        l1.next = l2
        l2.next = tmp1
        l1, l2 = tmp1, tmp2

# L0→L1→L2→L3→L4  →  L0→L4→L1→L3→L2
```

---

### P35. Find Middle of Linked List 🔴
```python
def find_middle(head: ListNode) -> ListNode:
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow

# [1,2,3,4,5]  →  node(3)
# [1,2,3,4]    →  node(3) — second middle
```

---

### P36. Add Two Numbers (as linked lists) 🔴
```python
def add_two_numbers(l1: ListNode, l2: ListNode) -> ListNode:
    dummy = curr = ListNode(0)
    carry = 0
    while l1 or l2 or carry:
        val = carry
        if l1: val += l1.val; l1 = l1.next
        if l2: val += l2.val; l2 = l2.next
        carry, val = divmod(val, 10)
        curr.next = ListNode(val)
        curr = curr.next
    return dummy.next

# (2->4->3) + (5->6->4)  =  342 + 465 = 807  →  (7->0->8)
```

---

### P37. Copy List with Random Pointer 🟡
```python
class Node:
    def __init__(self, x, next=None, random=None):
        self.val = x; self.next = next; self.random = random

def copy_random_list(head: Node) -> Node:
    if not head: return None
    old_to_new = {}
    curr = head
    while curr:
        old_to_new[curr] = Node(curr.val)
        curr = curr.next
    curr = head
    while curr:
        if curr.next:
            old_to_new[curr].next = old_to_new[curr.next]
        if curr.random:
            old_to_new[curr].random = old_to_new[curr.random]
        curr = curr.next
    return old_to_new[head]
```

---

## SECTION 4: STACKS & QUEUES

### P38. Min Stack 🔴
```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []

    def push(self, val: int) -> None:
        self.stack.append(val)
        min_val = min(val, self.min_stack[-1] if self.min_stack else val)
        self.min_stack.append(min_val)

    def pop(self) -> None:
        self.stack.pop()
        self.min_stack.pop()

    def top(self) -> int:
        return self.stack[-1]

    def get_min(self) -> int:
        return self.min_stack[-1]
```

---

### P39. Evaluate Reverse Polish Notation 🔴
```python
def eval_rpn(tokens: list[str]) -> int:
    stack = []
    ops = {
        '+': lambda a, b: a + b,
        '-': lambda a, b: a - b,
        '*': lambda a, b: a * b,
        '/': lambda a, b: int(a / b),  # truncate toward zero
    }
    for t in tokens:
        if t in ops:
            b, a = stack.pop(), stack.pop()
            stack.append(ops[t](a, b))
        else:
            stack.append(int(t))
    return stack[0]

# ["2","1","+","3","*"]  →  9  ((2+1)*3)
# ["4","13","5","/","+"] →  6  (4+(13/5))
```

---

### P40. Daily Temperatures 🔴
**Problem:** For each day, how many days until a warmer temperature?
```python
def daily_temperatures(temps: list[int]) -> list[int]:
    result = [0] * len(temps)
    stack = []  # monotonic decreasing stack of indices
    for i, t in enumerate(temps):
        while stack and temps[stack[-1]] < t:
            idx = stack.pop()
            result[idx] = i - idx
        stack.append(i)
    return result

# [73,74,75,71,69,72,76,73]  →  [1,1,4,2,1,1,0,0]
# Time: O(n), Space: O(n)
```

---

### P41. Largest Rectangle in Histogram 🟡
```python
def largest_rectangle(heights: list[int]) -> int:
    stack = []  # monotonic increasing stack
    max_area = 0
    for i, h in enumerate(heights + [0]):  # sentinel 0
        while stack and heights[stack[-1]] >= h:
            height = heights[stack.pop()]
            width = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, height * width)
        stack.append(i)
    return max_area

# [2,1,5,6,2,3]  →  10 (5*2 bars)
```

---

### P42. Implement Queue Using Two Stacks 🔴
```python
class MyQueue:
    def __init__(self):
        self.in_stack = []
        self.out_stack = []

    def push(self, x: int) -> None:
        self.in_stack.append(x)

    def pop(self) -> int:
        self._move()
        return self.out_stack.pop()

    def peek(self) -> int:
        self._move()
        return self.out_stack[-1]

    def empty(self) -> bool:
        return not self.in_stack and not self.out_stack

    def _move(self):
        if not self.out_stack:
            while self.in_stack:
                self.out_stack.append(self.in_stack.pop())
```

---

### P43. Sliding Window Maximum 🟡
```python
from collections import deque

def sliding_window_max(nums: list[int], k: int) -> list[int]:
    dq = deque()  # stores indices; front is always max
    result = []
    for i, n in enumerate(nums):
        # Remove elements outside window
        while dq and dq[0] < i - k + 1:
            dq.popleft()
        # Remove smaller elements from back (they'll never be max)
        while dq and nums[dq[-1]] < n:
            dq.pop()
        dq.append(i)
        if i >= k - 1:
            result.append(nums[dq[0]])
    return result

# [1,3,-1,-3,5,3,6,7], k=3  →  [3,3,5,5,6,7]
```

---

## SECTION 5: TREES — High Frequency

### P44. Maximum Depth of Binary Tree 🔴
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val; self.left = left; self.right = right

def max_depth(root: TreeNode) -> int:
    if not root: return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))

# Iterative BFS:
from collections import deque
def max_depth_bfs(root: TreeNode) -> int:
    if not root: return 0
    depth = 0
    queue = deque([root])
    while queue:
        depth += 1
        for _ in range(len(queue)):
            node = queue.popleft()
            if node.left: queue.append(node.left)
            if node.right: queue.append(node.right)
    return depth
```

---

### P45. Same Tree 🔴
```python
def is_same_tree(p: TreeNode, q: TreeNode) -> bool:
    if not p and not q: return True
    if not p or not q: return False
    return p.val == q.val and is_same_tree(p.left, q.left) and is_same_tree(p.right, q.right)
```

---

### P46. Invert Binary Tree 🔴
```python
def invert_tree(root: TreeNode) -> TreeNode:
    if not root: return None
    root.left, root.right = invert_tree(root.right), invert_tree(root.left)
    return root
```

---

### P47. Binary Tree Level Order Traversal 🔴
```python
from collections import deque

def level_order(root: TreeNode) -> list[list[int]]:
    if not root: return []
    result = []
    queue = deque([root])
    while queue:
        level = []
        for _ in range(len(queue)):
            node = queue.popleft()
            level.append(node.val)
            if node.left: queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(level)
    return result

# Returns [[3],[9,20],[15,7]] for standard example
```

---

### P48. Validate Binary Search Tree 🔴
```python
def is_valid_bst(root: TreeNode, lo=float('-inf'), hi=float('inf')) -> bool:
    if not root: return True
    if root.val <= lo or root.val >= hi:
        return False
    return (is_valid_bst(root.left, lo, root.val) and
            is_valid_bst(root.right, root.val, hi))
```

---

### P49. Lowest Common Ancestor of BST 🔴
```python
def lca_bst(root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
    while root:
        if p.val < root.val and q.val < root.val:
            root = root.left
        elif p.val > root.val and q.val > root.val:
            root = root.right
        else:
            return root
    return None

# Binary Tree (not BST) version:
def lca_bt(root, p, q):
    if not root or root is p or root is q: return root
    left = lca_bt(root.left, p, q)
    right = lca_bt(root.right, p, q)
    return root if left and right else left or right
```

---

### P50. Binary Tree Right Side View 🟡
```python
from collections import deque

def right_side_view(root: TreeNode) -> list[int]:
    if not root: return []
    result = []
    queue = deque([root])
    while queue:
        for i in range(len(queue)):
            node = queue.popleft()
            if i == len(queue):  # last node in level — but we already popleft
                pass
            if node.left: queue.append(node.left)
            if node.right: queue.append(node.right)
        result.append(node.val)  # node is last in level
    return result

# [1,2,3,null,5,null,4]  →  [1,3,4]
```

---

### P51. Diameter of Binary Tree 🔴
```python
def diameter_of_binary_tree(root: TreeNode) -> int:
    diameter = [0]
    def depth(node):
        if not node: return 0
        left = depth(node.left)
        right = depth(node.right)
        diameter[0] = max(diameter[0], left + right)
        return 1 + max(left, right)
    depth(root)
    return diameter[0]
```

---

### P52. Balanced Binary Tree 🔴
```python
def is_balanced(root: TreeNode) -> bool:
    def check(node):
        if not node: return 0
        left = check(node.left)
        if left == -1: return -1
        right = check(node.right)
        if right == -1: return -1
        if abs(left - right) > 1: return -1
        return 1 + max(left, right)
    return check(root) != -1
```

---

### P53. Binary Tree Maximum Path Sum 🟡
```python
def max_path_sum(root: TreeNode) -> int:
    max_sum = [float('-inf')]
    def max_gain(node):
        if not node: return 0
        left = max(max_gain(node.left), 0)
        right = max(max_gain(node.right), 0)
        max_sum[0] = max(max_sum[0], node.val + left + right)
        return node.val + max(left, right)
    max_gain(root)
    return max_sum[0]

# [-10,9,20,null,null,15,7]  →  42
```

---

### P54. Serialize and Deserialize Binary Tree 🟡
```python
from collections import deque

def serialize(root: TreeNode) -> str:
    if not root: return ""
    result = []
    queue = deque([root])
    while queue:
        node = queue.popleft()
        if node:
            result.append(str(node.val))
            queue.append(node.left)
            queue.append(node.right)
        else:
            result.append("null")
    return ','.join(result)

def deserialize(data: str) -> TreeNode:
    if not data: return None
    vals = deque(data.split(','))
    root = TreeNode(int(vals.popleft()))
    queue = deque([root])
    while queue:
        node = queue.popleft()
        left_val = vals.popleft()
        right_val = vals.popleft()
        if left_val != "null":
            node.left = TreeNode(int(left_val))
            queue.append(node.left)
        if right_val != "null":
            node.right = TreeNode(int(right_val))
            queue.append(node.right)
    return root
```

---

### P55. Kth Smallest in BST 🟡
```python
def kth_smallest(root: TreeNode, k: int) -> int:
    # Inorder traversal of BST gives sorted order
    stack = []
    curr = root
    count = 0
    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        count += 1
        if count == k:
            return curr.val
        curr = curr.right
```

---

### P56. Path Sum (root to leaf) 🔴
```python
def has_path_sum(root: TreeNode, target: int) -> bool:
    if not root: return False
    if not root.left and not root.right:
        return root.val == target
    return (has_path_sum(root.left, target - root.val) or
            has_path_sum(root.right, target - root.val))

# All paths:
def path_sum_all(root, target):
    result = []
    def dfs(node, remaining, path):
        if not node: return
        path.append(node.val)
        if not node.left and not node.right and remaining == node.val:
            result.append(path[:])
        dfs(node.left, remaining - node.val, path)
        dfs(node.right, remaining - node.val, path)
        path.pop()
    dfs(root, target, [])
    return result
```

---

## SECTION 6: GRAPHS — Medium-High Frequency

### P57. Number of Islands 🔴
```python
def num_islands(grid: list[list[str]]) -> int:
    if not grid: return 0
    rows, cols = len(grid), len(grid[0])
    count = 0

    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
            return
        grid[r][c] = '0'  # mark visited
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            dfs(r + dr, c + dc)

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)
    return count

# Time: O(m*n), Space: O(m*n) recursion stack
```

---

### P58. Clone Graph 🟡
```python
class GraphNode:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors or []

def clone_graph(node: GraphNode) -> GraphNode:
    if not node: return None
    cloned = {}
    def dfs(n):
        if n in cloned: return cloned[n]
        clone = GraphNode(n.val)
        cloned[n] = clone
        for neighbor in n.neighbors:
            clone.neighbors.append(dfs(neighbor))
        return clone
    return dfs(node)
```

---

### P59. Pacific Atlantic Water Flow 🟡
```python
def pacific_atlantic(heights: list[list[int]]) -> list[list[int]]:
    rows, cols = len(heights), len(heights[0])
    pacific = set()
    atlantic = set()

    def bfs(starts, visited):
        queue = deque(starts)
        visited.update(starts)
        while queue:
            r, c = queue.popleft()
            for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
                nr, nc = r + dr, c + dc
                if (0 <= nr < rows and 0 <= nc < cols and
                        (nr, nc) not in visited and
                        heights[nr][nc] >= heights[r][c]):
                    visited.add((nr, nc))
                    queue.append((nr, nc))

    pacific_starts = [(r, 0) for r in range(rows)] + [(0, c) for c in range(cols)]
    atlantic_starts = [(r, cols-1) for r in range(rows)] + [(rows-1, c) for c in range(cols)]
    bfs(pacific_starts, pacific)
    bfs(atlantic_starts, atlantic)
    return [[r, c] for r, c in pacific & atlantic]
```

---

### P60. Course Schedule (Cycle Detection) 🔴
```python
def can_finish(num_courses: int, prerequisites: list[list[int]]) -> bool:
    graph = [[] for _ in range(num_courses)]
    for course, prereq in prerequisites:
        graph[prereq].append(course)

    # 0=unvisited, 1=visiting(in cycle), 2=done
    state = [0] * num_courses

    def dfs(node):
        if state[node] == 1: return False  # cycle
        if state[node] == 2: return True
        state[node] = 1
        for neighbor in graph[node]:
            if not dfs(neighbor): return False
        state[node] = 2
        return True

    return all(dfs(i) for i in range(num_courses))
```

---

### P61. Course Schedule II (Topological Sort) 🟡
```python
from collections import deque

def find_order(num_courses: int, prerequisites: list[list[int]]) -> list[int]:
    graph = [[] for _ in range(num_courses)]
    in_degree = [0] * num_courses
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1

    queue = deque(i for i in range(num_courses) if in_degree[i] == 0)
    order = []
    while queue:
        course = queue.popleft()
        order.append(course)
        for next_c in graph[course]:
            in_degree[next_c] -= 1
            if in_degree[next_c] == 0:
                queue.append(next_c)
    return order if len(order) == num_courses else []
```

---

### P62. Number of Connected Components (Union-Find) 🟡
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [1] * n
        self.components = n

    def find(self, x):
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]  # path compression
            x = self.parent[x]
        return x

    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py: return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        self.rank[px] += max(self.rank[px], self.rank[py])
        self.components -= 1
        return True

def count_components(n: int, edges: list[list[int]]) -> int:
    uf = UnionFind(n)
    for a, b in edges:
        uf.union(a, b)
    return uf.components
```

---

### P63. Shortest Path — BFS (Unweighted Graph) 🔴
```python
from collections import deque

def shortest_path(graph: dict, src: int, dst: int) -> int:
    queue = deque([(src, 0)])
    visited = {src}
    while queue:
        node, dist = queue.popleft()
        if node == dst: return dist
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
    return -1
```

---

### P64. Dijkstra's Shortest Path (Weighted Graph) 🟡
```python
import heapq

def dijkstra(graph: dict, src: int) -> dict:
    dist = {src: 0}
    heap = [(0, src)]
    while heap:
        d, node = heapq.heappop(heap)
        if d > dist.get(node, float('inf')): continue
        for neighbor, weight in graph.get(node, []):
            new_dist = d + weight
            if new_dist < dist.get(neighbor, float('inf')):
                dist[neighbor] = new_dist
                heapq.heappush(heap, (new_dist, neighbor))
    return dist

# graph = {0: [(1,4),(2,1)], 2: [(1,2),(3,5)], 1: [(3,1)], 3: []}
# dijkstra(graph, 0) → {0:0, 1:3, 2:1, 3:4}
```

---

### P65. Surrounded Regions 🟡
```python
def solve(board: list[list[str]]) -> None:
    rows, cols = len(board), len(board[0])
    def dfs(r, c):
        if r < 0 or r >= rows or c < 0 or c >= cols or board[r][c] != 'O':
            return
        board[r][c] = 'S'  # safe
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            dfs(r+dr, c+dc)
    # Mark safe Os (connected to border)
    for r in range(rows):
        for c in [0, cols-1]: dfs(r, c)
    for c in range(cols):
        for r in [0, rows-1]: dfs(r, c)
    # Flip
    for r in range(rows):
        for c in range(cols):
            board[r][c] = 'X' if board[r][c] == 'O' else ('O' if board[r][c] == 'S' else board[r][c])
```

---

## SECTION 7: DYNAMIC PROGRAMMING — Medium-High Frequency

### P66. Climbing Stairs 🔴
```python
def climb_stairs(n: int) -> int:
    if n <= 2: return n
    a, b = 1, 2
    for _ in range(3, n + 1):
        a, b = b, a + b
    return b

# n=5  →  8 (it's Fibonacci!)
# Time: O(n), Space: O(1)
```

---

### P67. House Robber 🔴
```python
def rob(nums: list[int]) -> int:
    prev2 = prev1 = 0
    for n in nums:
        prev2, prev1 = prev1, max(prev1, prev2 + n)
    return prev1

# [2,7,9,3,1]  →  12 (rob 2+9+1)
# Time: O(n), Space: O(1)
```

---

### P68. House Robber II (Circular) 🟡
```python
def rob_circular(nums: list[int]) -> int:
    def rob_linear(arr):
        prev2 = prev1 = 0
        for n in arr:
            prev2, prev1 = prev1, max(prev1, prev2 + n)
        return prev1
    if len(nums) == 1: return nums[0]
    return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))
```

---

### P69. Longest Increasing Subsequence 🔴
```python
def lis(nums: list[int]) -> int:
    dp = [1] * len(nums)
    for i in range(1, len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)

# O(n²) — for O(n log n) use patience sorting with bisect:
import bisect
def lis_fast(nums: list[int]) -> int:
    tails = []
    for n in nums:
        pos = bisect.bisect_left(tails, n)
        if pos == len(tails): tails.append(n)
        else: tails[pos] = n
    return len(tails)

# [10,9,2,5,3,7,101,18]  →  4 ([2,3,7,101])
```

---

### P70. Coin Change 🔴
```python
def coin_change(coins: list[int], amount: int) -> int:
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for coin in coins:
        for a in range(coin, amount + 1):
            dp[a] = min(dp[a], dp[a - coin] + 1)
    return dp[amount] if dp[amount] != float('inf') else -1

# coins=[1,5,11], amount=15  →  3 (11+1+1+1? No: 5+5+5=3 coins)
# coins=[1,2,5], amount=11   →  3 (5+5+1)
```

---

### P71. Coin Change II (Number of Ways) 🟡
```python
def change(amount: int, coins: list[int]) -> int:
    dp = [0] * (amount + 1)
    dp[0] = 1
    for coin in coins:
        for a in range(coin, amount + 1):
            dp[a] += dp[a - coin]
    return dp[amount]

# amount=5, coins=[1,2,5]  →  4
```

---

### P72. Longest Common Subsequence 🔴
```python
def lcs(text1: str, text2: str) -> int:
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    return dp[m][n]

# "abcde","ace"  →  3
```

---

### P73. 0/1 Knapsack 🔴
```python
def knapsack(weights: list[int], values: list[int], capacity: int) -> int:
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            dp[i][w] = dp[i-1][w]  # don't take item i
            if weights[i-1] <= w:
                dp[i][w] = max(dp[i][w], dp[i-1][w - weights[i-1]] + values[i-1])
    return dp[n][capacity]

# weights=[1,3,4,5], values=[1,4,5,7], capacity=7  →  9
```

---

### P74. Word Break 🔴
```python
def word_break(s: str, word_dict: list[str]) -> bool:
    words = set(word_dict)
    dp = [False] * (len(s) + 1)
    dp[0] = True
    for i in range(1, len(s) + 1):
        for j in range(i):
            if dp[j] and s[j:i] in words:
                dp[i] = True
                break
    return dp[len(s)]

# s="leetcode", wordDict=["leet","code"]  →  True
```

---

### P75. Unique Paths 🔴
```python
def unique_paths(m: int, n: int) -> int:
    dp = [[1] * n for _ in range(m)]
    for r in range(1, m):
        for c in range(1, n):
            dp[r][c] = dp[r-1][c] + dp[r][c-1]
    return dp[m-1][n-1]

# m=3, n=7  →  28
# Space-optimized to O(n):
def unique_paths_opt(m: int, n: int) -> int:
    row = [1] * n
    for _ in range(m - 1):
        for c in range(1, n):
            row[c] += row[c-1]
    return row[n-1]
```

---

### P76. Jump Game 🔴
```python
def can_jump(nums: list[int]) -> bool:
    max_reach = 0
    for i, n in enumerate(nums):
        if i > max_reach: return False
        max_reach = max(max_reach, i + n)
    return True

# [2,3,1,1,4]  →  True
# [3,2,1,0,4]  →  False
```

---

### P77. Partition Equal Subset Sum 🟡
```python
def can_partition(nums: list[int]) -> bool:
    total = sum(nums)
    if total % 2: return False
    target = total // 2
    dp = {0}
    for n in nums:
        dp |= {s + n for s in dp if s + n <= target}
    return target in dp

# [1,5,11,5]  →  True ([1,5,5] and [11])
```

---

### P78. Edit Distance (Levenshtein) 🟡
```python
def min_distance(word1: str, word2: str) -> int:
    m, n = len(word1), len(word2)
    dp = list(range(n + 1))
    for i in range(1, m + 1):
        prev = dp[0]
        dp[0] = i
        for j in range(1, n + 1):
            temp = dp[j]
            if word1[i-1] == word2[j-1]:
                dp[j] = prev
            else:
                dp[j] = 1 + min(prev, dp[j], dp[j-1])
            prev = temp
    return dp[n]

# "horse","ros"  →  3
```

---

## SECTION 8: BINARY SEARCH — High Frequency

### P79. Binary Search (Standard) 🔴
```python
def binary_search(nums: list[int], target: int) -> int:
    lo, hi = 0, len(nums) - 1
    while lo <= hi:
        mid = lo + (hi - lo) // 2
        if nums[mid] == target: return mid
        elif nums[mid] < target: lo = mid + 1
        else: hi = mid - 1
    return -1
```

---

### P80. Find First and Last Position 🔴
```python
import bisect

def search_range(nums: list[int], target: int) -> list[int]:
    left = bisect.bisect_left(nums, target)
    if left == len(nums) or nums[left] != target:
        return [-1, -1]
    right = bisect.bisect_right(nums, target) - 1
    return [left, right]

# [5,7,7,8,8,10], target=8  →  [3,4]
```

---

### P81. Search a 2D Matrix 🔴
```python
def search_matrix(matrix: list[list[int]], target: int) -> bool:
    rows, cols = len(matrix), len(matrix[0])
    lo, hi = 0, rows * cols - 1
    while lo <= hi:
        mid = (lo + hi) // 2
        val = matrix[mid // cols][mid % cols]
        if val == target: return True
        elif val < target: lo = mid + 1
        else: hi = mid - 1
    return False
```

---

### P82. Koko Eating Bananas 🟡
```python
import math

def min_eating_speed(piles: list[int], h: int) -> int:
    lo, hi = 1, max(piles)
    while lo < hi:
        mid = (lo + hi) // 2
        hours = sum(math.ceil(p / mid) for p in piles)
        if hours <= h:
            hi = mid
        else:
            lo = mid + 1
    return lo

# piles=[3,6,7,11], h=8  →  4
```

---

### P83. Median of Two Sorted Arrays 🟢
```python
def find_median_sorted_arrays(nums1: list[int], nums2: list[int]) -> float:
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1
    m, n = len(nums1), len(nums2)
    lo, hi = 0, m
    while lo <= hi:
        px = (lo + hi) // 2
        py = (m + n + 1) // 2 - px
        max_left_x  = nums1[px-1] if px > 0 else float('-inf')
        min_right_x = nums1[px]   if px < m else float('inf')
        max_left_y  = nums2[py-1] if py > 0 else float('-inf')
        min_right_y = nums2[py]   if py < n else float('inf')
        if max_left_x <= min_right_y and max_left_y <= min_right_x:
            if (m + n) % 2 == 1:
                return float(max(max_left_x, max_left_y))
            return (max(max_left_x, max_left_y) + min(min_right_x, min_right_y)) / 2
        elif max_left_x > min_right_y:
            hi = px - 1
        else:
            lo = px + 1

# O(log(min(m,n)))
```

---

## SECTION 9: HEAPS & PRIORITY QUEUES

### P84. Find Kth Largest Element 🔴
```python
import heapq

def find_kth_largest(nums: list[int], k: int) -> int:
    # Min-heap of size k
    heap = nums[:k]
    heapq.heapify(heap)
    for n in nums[k:]:
        if n > heap[0]:
            heapq.heapreplace(heap, n)
    return heap[0]

# [3,2,1,5,6,4], k=2  →  5
# Time: O(n log k), Space: O(k)
```

---

### P85. Top K Frequent Elements 🔴
```python
from collections import Counter
import heapq

def top_k_frequent(nums: list[int], k: int) -> list[int]:
    count = Counter(nums)
    return heapq.nlargest(k, count.keys(), key=count.get)

# Bucket sort O(n):
def top_k_bucket(nums: list[int], k: int) -> list[int]:
    count = Counter(nums)
    buckets = [[] for _ in range(len(nums) + 1)]
    for num, freq in count.items():
        buckets[freq].append(num)
    result = []
    for freq in range(len(buckets) - 1, -1, -1):
        result.extend(buckets[freq])
        if len(result) >= k: break
    return result[:k]

# [1,1,1,2,2,3], k=2  →  [1,2]
```

---

### P86. K Closest Points to Origin 🔴
```python
import heapq

def k_closest(points: list[list[int]], k: int) -> list[list[int]]:
    return heapq.nsmallest(k, points, key=lambda p: p[0]**2 + p[1]**2)

# [[1,3],[-2,2]], k=1  →  [[-2,2]]
```

---

### P87. Task Scheduler 🟡
```python
from collections import Counter
import heapq

def least_interval(tasks: list[str], n: int) -> int:
    freq = Counter(tasks)
    heap = [-f for f in freq.values()]
    heapq.heapify(heap)
    time = 0
    while heap:
        cycle = []
        for _ in range(n + 1):
            if heap:
                cycle.append(heapq.heappop(heap))
        for f in cycle:
            if f + 1 < 0:
                heapq.heappush(heap, f + 1)
        time += n + 1 if heap else len(cycle)
    return time

# ["A","A","A","B","B","B"], n=2  →  8
```

---

### P88. Median Finder (Two Heaps) 🟡
```python
import heapq

class MedianFinder:
    def __init__(self):
        self.small = []  # max-heap (negate values)
        self.large = []  # min-heap

    def add_num(self, num: int) -> None:
        heapq.heappush(self.small, -num)
        # Balance: smallest of small must be <= largest of large
        if self.small and self.large and -self.small[0] > self.large[0]:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        # Size balance
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))

    def find_median(self) -> float:
        if len(self.small) > len(self.large):
            return float(-self.small[0])
        return (-self.small[0] + self.large[0]) / 2
```

---

## SECTION 10: RECURSION & BACKTRACKING

### P89. Subsets 🔴
```python
def subsets(nums: list[int]) -> list[list[int]]:
    result = [[]]
    for n in nums:
        result += [s + [n] for s in result]
    return result

# Backtracking version:
def subsets_bt(nums):
    result = []
    def backtrack(start, current):
        result.append(current[:])
        for i in range(start, len(nums)):
            current.append(nums[i])
            backtrack(i + 1, current)
            current.pop()
    backtrack(0, [])
    return result

# [1,2,3]  →  [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
```

---

### P90. Permutations 🔴
```python
def permutations(nums: list[int]) -> list[list[int]]:
    result = []
    def backtrack(current, remaining):
        if not remaining:
            result.append(current[:])
            return
        for i, n in enumerate(remaining):
            current.append(n)
            backtrack(current, remaining[:i] + remaining[i+1:])
            current.pop()
    backtrack([], nums)
    return result

# [1,2,3]  →  6 permutations
```

---

### P91. Combination Sum 🔴
```python
def combination_sum(candidates: list[int], target: int) -> list[list[int]]:
    result = []
    candidates.sort()
    def backtrack(start, current, remaining):
        if remaining == 0:
            result.append(current[:])
            return
        for i in range(start, len(candidates)):
            if candidates[i] > remaining: break
            current.append(candidates[i])
            backtrack(i, current, remaining - candidates[i])  # i not i+1 (reuse allowed)
            current.pop()
    backtrack(0, [], target)
    return result

# candidates=[2,3,6,7], target=7  →  [[2,2,3],[7]]
```

---

### P92. Letter Combinations of Phone Number 🔴
```python
def letter_combinations(digits: str) -> list[str]:
    if not digits: return []
    phone = {'2':'abc','3':'def','4':'ghi','5':'jkl',
             '6':'mno','7':'pqrs','8':'tuv','9':'wxyz'}
    result = []
    def backtrack(i, current):
        if i == len(digits):
            result.append(current)
            return
        for ch in phone[digits[i]]:
            backtrack(i + 1, current + ch)
    backtrack(0, '')
    return result

# "23"  →  ["ad","ae","af","bd","be","bf","cd","ce","cf"]
```

---

### P93. N-Queens 🟡
```python
def solve_n_queens(n: int) -> list[list[str]]:
    result = []
    cols = set(); diag1 = set(); diag2 = set()
    board = [['.']*n for _ in range(n)]

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

### P94. Sudoku Solver 🟡
```python
def solve_sudoku(board: list[list[str]]) -> None:
    def is_valid(r, c, val):
        box_r, box_c = 3*(r//3), 3*(c//3)
        for i in range(9):
            if board[r][i] == val: return False
            if board[i][c] == val: return False
            if board[box_r+i//3][box_c+i%3] == val: return False
        return True

    def solve():
        for r in range(9):
            for c in range(9):
                if board[r][c] == '.':
                    for val in '123456789':
                        if is_valid(r, c, val):
                            board[r][c] = val
                            if solve(): return True
                            board[r][c] = '.'
                    return False
        return True
    solve()
```

---

## SECTION 11: TWO POINTERS & SLIDING WINDOW

### P95. Valid Palindrome II (One Deletion Allowed) 🔴
```python
def valid_palindrome(s: str) -> bool:
    def is_pal(lo, hi):
        while lo < hi:
            if s[lo] != s[hi]: return False
            lo += 1; hi -= 1
        return True
    lo, hi = 0, len(s) - 1
    while lo < hi:
        if s[lo] != s[hi]:
            return is_pal(lo+1, hi) or is_pal(lo, hi-1)
        lo += 1; hi -= 1
    return True
```

---

### P96. Trapping Rain Water 🔴
```python
def trap(height: list[int]) -> int:
    lo, hi = 0, len(height) - 1
    max_left = max_right = 0
    water = 0
    while lo < hi:
        if height[lo] < height[hi]:
            max_left = max(max_left, height[lo])
            water += max_left - height[lo]
            lo += 1
        else:
            max_right = max(max_right, height[hi])
            water += max_right - height[hi]
            hi -= 1
    return water

# [0,1,0,2,1,0,1,3,2,1,2,1]  →  6
# Time: O(n), Space: O(1)
```

---

### P97. Longest Mountain in Array 🟡
```python
def longest_mountain(arr: list[int]) -> int:
    n = len(arr)
    if n < 3: return 0
    result = 0
    i = 1
    while i < n - 1:
        if arr[i-1] < arr[i] > arr[i+1]:
            lo = hi = i
            while lo > 0 and arr[lo-1] < arr[lo]: lo -= 1
            while hi < n-1 and arr[hi] > arr[hi+1]: hi += 1
            result = max(result, hi - lo + 1)
            i = hi
        else:
            i += 1
    return result
```

---

### P98. Four Sum 🟡
```python
def four_sum(nums: list[int], target: int) -> list[list[int]]:
    nums.sort()
    n = len(nums)
    result = []
    for i in range(n - 3):
        if i > 0 and nums[i] == nums[i-1]: continue
        for j in range(i+1, n-2):
            if j > i+1 and nums[j] == nums[j-1]: continue
            lo, hi = j+1, n-1
            while lo < hi:
                s = nums[i]+nums[j]+nums[lo]+nums[hi]
                if s == target:
                    result.append([nums[i],nums[j],nums[lo],nums[hi]])
                    while lo < hi and nums[lo] == nums[lo+1]: lo += 1
                    while lo < hi and nums[hi] == nums[hi-1]: hi -= 1
                    lo += 1; hi -= 1
                elif s < target: lo += 1
                else: hi -= 1
    return result
```

---

## SECTION 12: TRIE — Asked at Senior Level

### P99. Implement Trie 🟡
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            node = node.children.setdefault(ch, TrieNode())
        node.is_end = True

    def search(self, word: str) -> bool:
        node = self.root
        for ch in word:
            if ch not in node.children: return False
            node = node.children[ch]
        return node.is_end

    def starts_with(self, prefix: str) -> bool:
        node = self.root
        for ch in prefix:
            if ch not in node.children: return False
            node = node.children[ch]
        return True
```

---

### P100. Word Search II (Trie + DFS) 🟡
```python
def find_words(board: list[list[str]], words: list[str]) -> list[str]:
    trie = Trie()
    for word in words: trie.insert(word)
    rows, cols = len(board), len(board[0])
    result = set()

    def dfs(node, r, c, path):
        ch = board[r][c]
        child = node.children.get(ch)
        if not child: return
        path += ch
        if child.is_end: result.add(path)
        board[r][c] = '#'
        for dr, dc in [(0,1),(0,-1),(1,0),(-1,0)]:
            nr, nc = r+dr, c+dc
            if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] != '#':
                dfs(child, nr, nc, path)
        board[r][c] = ch

    for r in range(rows):
        for c in range(cols):
            dfs(trie.root, r, c, '')
    return list(result)
```

---

## SECTION 13: BIT MANIPULATION

### P101. Single Number 🔴
```python
from functools import reduce
from operator import xor

def single_number(nums: list[int]) -> int:
    return reduce(xor, nums)

# [4,1,2,1,2]  →  4 (XOR cancels pairs)
```

---

### P102. Number of 1 Bits 🔴
```python
def hamming_weight(n: int) -> int:
    return bin(n).count('1')

# Brian Kernighan's:
def hamming_weight_bk(n: int) -> int:
    count = 0
    while n:
        n &= n - 1  # clears lowest set bit
        count += 1
    return count
```

---

### P103. Reverse Bits 🟡
```python
def reverse_bits(n: int) -> int:
    result = 0
    for _ in range(32):
        result = (result << 1) | (n & 1)
        n >>= 1
    return result
```

---

### P104. Missing Number 🔴
```python
def missing_number(nums: list[int]) -> int:
    return len(nums) * (len(nums) + 1) // 2 - sum(nums)

# XOR version:
def missing_xor(nums: list[int]) -> int:
    result = len(nums)
    for i, n in enumerate(nums):
        result ^= i ^ n
    return result

# [3,0,1]  →  2
```

---

### P105. Power of Two 🔴
```python
def is_power_of_two(n: int) -> bool:
    return n > 0 and (n & (n - 1)) == 0

# 16 → True, 18 → False
```

---

## SECTION 14: MATH & NUMBER THEORY

### P106. Excel Sheet Column Number 🔴
```python
def title_to_number(column_title: str) -> int:
    result = 0
    for ch in column_title:
        result = result * 26 + (ord(ch) - ord('A') + 1)
    return result

# "AB"  →  28
```

---

### P107. Happy Number 🔴
```python
def is_happy(n: int) -> bool:
    def digit_square_sum(x):
        return sum(int(d)**2 for d in str(x))
    slow = fast = n
    while True:
        slow = digit_square_sum(slow)
        fast = digit_square_sum(digit_square_sum(fast))
        if slow == fast: break
    return slow == 1

# 19 → True (1²+9²=82 → 68 → 100 → 1)
```

---

### P108. Count Primes 🟡
```python
def count_primes(n: int) -> int:
    if n < 2: return 0
    sieve = [True] * n
    sieve[0] = sieve[1] = False
    for i in range(2, int(n**0.5) + 1):
        if sieve[i]:
            for j in range(i*i, n, i):
                sieve[j] = False
    return sum(sieve)
```

---

## SECTION 15: SORTING ALGORITHMS (Asked to Code From Scratch)

### P109. Quick Sort 🔴
```python
def quick_sort(arr: list[int]) -> list[int]:
    if len(arr) <= 1: return arr
    pivot = arr[len(arr) // 2]
    left  = [x for x in arr if x < pivot]
    mid   = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quick_sort(left) + mid + quick_sort(right)

# In-place version:
def quick_sort_inplace(arr, lo=0, hi=None):
    if hi is None: hi = len(arr) - 1
    if lo < hi:
        p = partition(arr, lo, hi)
        quick_sort_inplace(arr, lo, p - 1)
        quick_sort_inplace(arr, p + 1, hi)

def partition(arr, lo, hi):
    pivot = arr[hi]
    i = lo - 1
    for j in range(lo, hi):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i+1], arr[hi] = arr[hi], arr[i+1]
    return i + 1
```

---

### P110. Merge Sort 🔴
```python
def merge_sort(arr: list[int]) -> list[int]:
    if len(arr) <= 1: return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left, right):
    result, i, j = [], 0, 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i]); i += 1
        else:
            result.append(right[j]); j += 1
    return result + left[i:] + right[j:]

# Time: O(n log n), Space: O(n) — stable sort
```

---

### P111. Heap Sort 🟡
```python
def heap_sort(arr: list[int]) -> list[int]:
    import heapq
    heapq.heapify(arr)
    return [heapq.heappop(arr) for _ in range(len(arr))]

# Manual heap sort:
def heap_sort_manual(arr):
    n = len(arr)
    for i in range(n//2 - 1, -1, -1):
        heapify(arr, n, i)
    for i in range(n-1, 0, -1):
        arr[0], arr[i] = arr[i], arr[0]
        heapify(arr, i, 0)

def heapify(arr, n, i):
    largest = i
    l, r = 2*i+1, 2*i+2
    if l < n and arr[l] > arr[largest]: largest = l
    if r < n and arr[r] > arr[largest]: largest = r
    if largest != i:
        arr[i], arr[largest] = arr[largest], arr[i]
        heapify(arr, n, largest)
```

---

## SECTION 16: DESIGN PROBLEMS (Data Structure Design)

### P112. Design LRU Cache 🔴
```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = OrderedDict()

    def get(self, key: int) -> int:
        if key not in self.cache: return -1
        self.cache.move_to_end(key)
        return self.cache[key]

    def put(self, key: int, value: int) -> None:
        self.cache[key] = value
        self.cache.move_to_end(key)
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)

# Time: O(1) for both get and put
```

---

### P113. Design HashMap 🔴
```python
class MyHashMap:
    def __init__(self):
        self.size = 1000
        self.buckets = [[] for _ in range(self.size)]

    def _hash(self, key): return key % self.size

    def put(self, key: int, value: int) -> None:
        bucket = self.buckets[self._hash(key)]
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return
        bucket.append((key, value))

    def get(self, key: int) -> int:
        for k, v in self.buckets[self._hash(key)]:
            if k == key: return v
        return -1

    def remove(self, key: int) -> None:
        bucket = self.buckets[self._hash(key)]
        self.buckets[self._hash(key)] = [(k,v) for k,v in bucket if k != key]
```

---

### P114. Design Twitter 🟡
```python
from collections import defaultdict
import heapq

class Twitter:
    def __init__(self):
        self.tweets = defaultdict(list)   # user_id → [(timestamp, tweet_id)]
        self.following = defaultdict(set)
        self.timestamp = 0

    def post_tweet(self, user_id: int, tweet_id: int) -> None:
        self.tweets[user_id].append((self.timestamp, tweet_id))
        self.timestamp -= 1  # decrement for min-heap max trick

    def get_news_feed(self, user_id: int) -> list[int]:
        heap = []
        users = self.following[user_id] | {user_id}
        for uid in users:
            tweets = self.tweets[uid]
            if tweets:
                idx = len(tweets) - 1
                ts, tid = tweets[idx]
                heapq.heappush(heap, (ts, tid, uid, idx - 1))
        result = []
        while heap and len(result) < 10:
            ts, tid, uid, idx = heapq.heappop(heap)
            result.append(tid)
            if idx >= 0:
                ts2, tid2 = self.tweets[uid][idx]
                heapq.heappush(heap, (ts2, tid2, uid, idx - 1))
        return result

    def follow(self, follower: int, followee: int) -> None:
        self.following[follower].add(followee)

    def unfollow(self, follower: int, followee: int) -> None:
        self.following[follower].discard(followee)
```

---

## PART 2: INTERVIEW STRATEGY — HOW TO APPROACH DSA PROBLEMS

### The 5-Step Framework
```
1. UNDERSTAND (2 min):
   - Read problem twice
   - State constraints: n, value range, duplicates?
   - Ask: sorted? In-place? Return index or value?
   - Work through 2 examples by hand

2. BRUTE FORCE (1 min):
   - State the naive O(n²) or O(2ⁿ) solution first
   - "I can brute force with nested loops — O(n²) — but let me optimize"

3. PATTERN RECOGNITION (2 min):
   Ask yourself:
   - Sorted + find pair → Two Pointers
   - Subarray/substring → Sliding Window
   - Prefix sum → HashMap of prefix sums
   - Tree traversal → DFS/BFS
   - Optimal substructure → DP
   - Repeated subproblems → Memoization
   - Choices: include/exclude → Backtracking
   - Sorted + O(log n) → Binary Search

4. CODE (15 min):
   - Talk while coding
   - Use meaningful variable names
   - Handle edge cases: empty, single element, all same

5. TEST (3 min):
   - Trace through given example
   - Test edge case: [], [1], [1,1]
   - State time and space complexity
```

---

## PART 3: COMPLEXITY CHEAT SHEET

| Operation | Array | Linked List | HashMap | BST | Heap |
|-----------|-------|-------------|---------|-----|------|
| Access | O(1) | O(n) | O(1) avg | O(log n) | O(n) |
| Search | O(n) | O(n) | O(1) avg | O(log n) | O(n) |
| Insert | O(n) | O(1) | O(1) avg | O(log n) | O(log n) |
| Delete | O(n) | O(1) | O(1) avg | O(log n) | O(log n) |

| Algorithm | Time | Space | Stable? |
|-----------|------|-------|---------|
| Bubble Sort | O(n²) | O(1) | Yes |
| Selection Sort | O(n²) | O(1) | No |
| Insertion Sort | O(n²) | O(1) | Yes |
| Merge Sort | O(n log n) | O(n) | Yes |
| Quick Sort | O(n log n) avg | O(log n) | No |
| Heap Sort | O(n log n) | O(1) | No |
| Counting Sort | O(n+k) | O(k) | Yes |
| Radix Sort | O(nk) | O(n+k) | Yes |

---

## PART 4: TOP 30 MUST-SOLVE PROBLEMS (High Frequency Final List)

| # | Problem | Topic | Difficulty |
|---|---------|-------|-----------|
| 1 | Two Sum | Array + HashMap | Easy 🔴 |
| 2 | Best Time to Buy/Sell Stock | Array | Easy 🔴 |
| 3 | Maximum Subarray (Kadane's) | Array + DP | Easy 🔴 |
| 4 | Valid Parentheses | Stack | Easy 🔴 |
| 5 | Reverse Linked List | Linked List | Easy 🔴 |
| 6 | Detect Cycle in List | Linked List | Easy 🔴 |
| 7 | Merge Two Sorted Lists | Linked List | Easy 🔴 |
| 8 | Climbing Stairs | DP | Easy 🔴 |
| 9 | House Robber | DP | Easy 🔴 |
| 10 | Binary Search | Binary Search | Easy 🔴 |
| 11 | Missing Number | Array + Math | Easy 🔴 |
| 12 | Contains Duplicate | Array + Set | Easy 🔴 |
| 13 | Valid Palindrome | String | Easy 🔴 |
| 14 | Longest Substring No Repeat | String + Window | Medium 🔴 |
| 15 | 3Sum | Array + Two Pointer | Medium 🔴 |
| 16 | Group Anagrams | String + HashMap | Medium 🔴 |
| 17 | Product Except Self | Array | Medium 🔴 |
| 18 | Merge Intervals | Array + Sorting | Medium 🔴 |
| 19 | Number of Islands | Graph + DFS | Medium 🔴 |
| 20 | Course Schedule | Graph + Topo Sort | Medium 🔴 |
| 21 | Level Order Traversal | Tree + BFS | Medium 🔴 |
| 22 | Validate BST | Tree | Medium 🔴 |
| 23 | LCA of Binary Tree | Tree | Medium 🔴 |
| 24 | Kth Largest Element | Heap | Medium 🔴 |
| 25 | Top K Frequent | Heap + HashMap | Medium 🔴 |
| 26 | Daily Temperatures | Monotonic Stack | Medium 🔴 |
| 27 | Trapping Rain Water | Two Pointer | Hard 🟡 |
| 28 | Coin Change | DP | Medium 🔴 |
| 29 | LCS | DP | Medium 🔴 |
| 30 | Word Break | DP + Trie | Medium 🔴 |
