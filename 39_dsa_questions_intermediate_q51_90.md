# DSA Questions — Intermediate Part 1 (Q51–Q90)
> **Phase 2: Intermediate Techniques** | Sorting, Searching, Stacks, Queues, LinkedLists
> **Difficulty:** Easy-Medium to Medium | **Time per question:** 25–40 minutes
> **Companies:** Product + Captive | **Frequency:** High (Tier 1–2)

---

## SECTION 1: Sorting & Searching (Q51–Q65)

### Q51. Bubble sort
**Problem:** Implement bubble sort algorithm.
```python
def bubble_sort(arr: list) -> list:
    n = len(arr)
    for i in range(n):
        swapped = False
        for j in range(0, n - i - 1):
            if arr[j] > arr[j + 1]:
                arr[j], arr[j + 1] = arr[j + 1], arr[j]
                swapped = True
        if not swapped:
            break
    return arr
```
**Time:** O(n²) avg, O(n) best | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q52. Merge sort
**Problem:** Implement merge sort algorithm.
```python
def merge_sort(arr: list) -> list:
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

def merge(left: list, right: list) -> list:
    result = []
    i = j = 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            result.append(left[i])
            i += 1
        else:
            result.append(right[j])
            j += 1
    result.extend(left[i:])
    result.extend(right[j:])
    return result
```
**Time:** O(n log n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q53. Quick sort
**Problem:** Implement quick sort algorithm.
```python
def quick_sort(arr: list) -> list:
    if len(arr) <= 1:
        return arr
    pivot = arr[len(arr) // 2]
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return quick_sort(left) + middle + quick_sort(right)
```
**Time:** O(n log n) avg, O(n²) worst | **Space:** O(log n) | **Frequency:** High | **Tier:** 1

---

### Q54. Binary search
**Problem:** Implement binary search on sorted array.
```python
def binary_search(arr: list, target: int) -> int:
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** Very High | **Tier:** 1

---

### Q55. Search in rotated sorted array
**Problem:** Find target in rotated sorted array.
```python
def search_rotated(arr: list, target: int) -> int:
    left, right = 0, len(arr) - 1
    while left <= right:
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        if arr[left] <= arr[mid]:
            if arr[left] <= target < arr[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:
            if arr[mid] < target <= arr[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q56. Find first and last position of element
**Problem:** Find first and last index of target in sorted array.
```python
def search_range(arr: list, target: int) -> list:
    def find_first(arr, target):
        left, right = 0, len(arr) - 1
        result = -1
        while left <= right:
            mid = (left + right) // 2
            if arr[mid] == target:
                result = mid
                right = mid - 1
            elif arr[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        return result
    
    def find_last(arr, target):
        left, right = 0, len(arr) - 1
        result = -1
        while left <= right:
            mid = (left + right) // 2
            if arr[mid] == target:
                result = mid
                left = mid + 1
            elif arr[mid] < target:
                left = mid + 1
            else:
                right = mid - 1
        return result
    
    first = find_first(arr, target)
    if first == -1:
        return [-1, -1]
    last = find_last(arr, target)
    return [first, last]
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q57. Find peak element in array
**Problem:** Find peak element (greater than neighbors) using binary search.
```python
def find_peak(arr: list) -> int:
    left, right = 0, len(arr) - 1
    while left < right:
        mid = (left + right) // 2
        if arr[mid] > arr[mid + 1]:
            right = mid
        else:
            left = mid + 1
    return arr[left]
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q58. Kth smallest element
**Problem:** Find kth smallest element in array.
```python
def find_kth_smallest(arr: list, k: int) -> int:
    arr.sort()
    return arr[k - 1]
```
**Time:** O(n log n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q59. Median of two sorted arrays
**Problem:** Find median of two sorted arrays.
```python
def find_median(arr1: list, arr2: list) -> float:
    merged = sorted(arr1 + arr2)
    n = len(merged)
    if n % 2 == 1:
        return float(merged[n // 2])
    else:
        return (merged[n // 2 - 1] + merged[n // 2]) / 2
```
**Time:** O((n + m) log(n + m)) | **Space:** O(n + m) | **Frequency:** Medium | **Tier:** 2

---

### Q60. Intersection of two sorted arrays
**Problem:** Find intersection of two sorted arrays.
```python
def intersection(arr1: list, arr2: list) -> list:
    i = j = 0
    result = []
    while i < len(arr1) and j < len(arr2):
        if arr1[i] == arr2[j]:
            if not result or result[-1] != arr1[i]:
                result.append(arr1[i])
            i += 1
            j += 1
        elif arr1[i] < arr2[j]:
            i += 1
        else:
            j += 1
    return result
```
**Time:** O(n + m) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q61. Union of two sorted arrays
**Problem:** Find union of two sorted arrays.
```python
def union(arr1: list, arr2: list) -> list:
    i = j = 0
    result = []
    while i < len(arr1) and j < len(arr2):
        if arr1[i] == arr2[j]:
            if not result or result[-1] != arr1[i]:
                result.append(arr1[i])
            i += 1
            j += 1
        elif arr1[i] < arr2[j]:
            if not result or result[-1] != arr1[i]:
                result.append(arr1[i])
            i += 1
        else:
            if not result or result[-1] != arr2[j]:
                result.append(arr2[j])
            j += 1
    while i < len(arr1):
        if not result or result[-1] != arr1[i]:
            result.append(arr1[i])
        i += 1
    while j < len(arr2):
        if not result or result[-1] != arr2[j]:
            result.append(arr2[j])
        j += 1
    return result
```
**Time:** O(n + m) | **Space:** O(n + m) | **Frequency:** Medium | **Tier:** 2

---

### Q62. Partition array by pivot
**Problem:** Partition array around pivot value.
```python
def partition(arr: list, pivot: int) -> list:
    left = [x for x in arr if x < pivot]
    middle = [x for x in arr if x == pivot]
    right = [x for x in arr if x > pivot]
    return left + middle + right
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q63. Sort colors (0, 1, 2)
**Problem:** Sort array with only 0, 1, 2 values.
```python
def sort_colors(arr: list) -> list:
    low = mid = 0
    high = len(arr) - 1
    while mid <= high:
        if arr[mid] == 0:
            arr[low], arr[mid] = arr[mid], arr[low]
            low += 1
            mid += 1
        elif arr[mid] == 1:
            mid += 1
        else:
            arr[mid], arr[high] = arr[high], arr[mid]
            high -= 1
    return arr
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q64. Largest element in each subarray
**Problem:** Find largest element in each subarray of size k.
```python
from collections import deque

def max_in_subarrays(arr: list, k: int) -> list:
    dq = deque()
    result = []
    for i in range(len(arr)):
        if dq and dq[0][1] < i - k + 1:
            dq.popleft()
        while dq and dq[-1][0] <= arr[i]:
            dq.pop()
        dq.append((arr[i], i))
        if i >= k - 1:
            result.append(dq[0][0])
    return result
```
**Time:** O(n) | **Space:** O(k) | **Frequency:** Medium | **Tier:** 2

---

### Q65. Minimum window substring
**Problem:** Find minimum window substring containing all characters.
```python
def min_window(s: str, t: str) -> str:
    if not s or not t:
        return ""
    dict_t = {}
    for char in t:
        dict_t[char] = dict_t.get(char, 0) + 1
    required = len(dict_t)
    l, r = 0, 0
    formed = 0
    window_counts = {}
    ans = float("inf"), None, None
    while r < len(s):
        char = s[r]
        window_counts[char] = window_counts.get(char, 0) + 1
        if char in dict_t and window_counts[char] == dict_t[char]:
            formed += 1
        while l <= r and formed == required:
            char = s[l]
            if r - l + 1 < ans[0]:
                ans = (r - l + 1, l, r)
            window_counts[char] -= 1
            if char in dict_t and window_counts[char] < dict_t[char]:
                formed -= 1
            l += 1
        r += 1
    return "" if ans[0] == float("inf") else s[ans[1]:ans[2] + 1]
```
**Time:** O(|S| + |T|) | **Space:** O(|S| + |T|) | **Frequency:** Medium | **Tier:** 2

---

## SECTION 2: Stacks & Queues (Q66–Q80)

### Q66. Implement stack using list
```python
class Stack:
    def __init__(self):
        self.items = []
    
    def push(self, item):
        self.items.append(item)
    
    def pop(self):
        return self.items.pop() if not self.is_empty() else None
    
    def peek(self):
        return self.items[-1] if not self.is_empty() else None
    
    def is_empty(self):
        return len(self.items) == 0
```
**Time:** O(1) for all ops | **Space:** O(n) | **Frequency:** Very High | **Tier:** 1

---

### Q67. Implement queue using list
```python
from collections import deque

class Queue:
    def __init__(self):
        self.items = deque()
    
    def enqueue(self, item):
        self.items.append(item)
    
    def dequeue(self):
        return self.items.popleft() if not self.is_empty() else None
    
    def peek(self):
        return self.items[0] if not self.is_empty() else None
    
    def is_empty(self):
        return len(self.items) == 0
```
**Time:** O(1) for all ops | **Space:** O(n) | **Frequency:** Very High | **Tier:** 1

---

### Q68. Balanced parentheses
```python
def is_balanced(s: str) -> bool:
    stack = []
    pairs = {'(': ')', '{': '}', '[': ']'}
    for char in s:
        if char in pairs:
            stack.append(char)
        else:
            if not stack or pairs[stack.pop()] != char:
                return False
    return len(stack) == 0
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Very High | **Tier:** 1

---

### Q69. Next greater element
```python
def next_greater(arr: list) -> list:
    stack = []
    result = [-1] * len(arr)
    for i in range(len(arr) - 1, -1, -1):
        while stack and stack[-1] <= arr[i]:
            stack.pop()
        if stack:
            result[i] = stack[-1]
        stack.append(arr[i])
    return result
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q70. Implement min stack
```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []
    
    def push(self, val: int):
        self.stack.append(val)
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)
    
    def pop(self):
        if self.stack[-1] == self.min_stack[-1]:
            self.min_stack.pop()
        self.stack.pop()
    
    def top(self):
        return self.stack[-1]
    
    def get_min(self):
        return self.min_stack[-1]
```
**Time:** O(1) for all ops | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q71. Evaluate postfix expression
```python
def eval_postfix(tokens: list) -> int:
    stack = []
    operators = {'+', '-', '*', '/'}
    for token in tokens:
        if token in operators:
            b = stack.pop()
            a = stack.pop()
            if token == '+':
                stack.append(a + b)
            elif token == '-':
                stack.append(a - b)
            elif token == '*':
                stack.append(a * b)
            elif token == '/':
                stack.append(int(a / b))
        else:
            stack.append(int(token))
    return stack[0]
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q72. Implement circular queue
```python
class CircularQueue:
    def __init__(self, k: int):
        self.queue = [0] * k
        self.head = 0
        self.tail = -1
        self.size = 0
        self.capacity = k
    
    def enqueue(self, val: int) -> bool:
        if self.size == self.capacity:
            return False
        self.tail = (self.tail + 1) % self.capacity
        self.queue[self.tail] = val
        self.size += 1
        return True
    
    def dequeue(self) -> bool:
        if self.size == 0:
            return False
        self.head = (self.head + 1) % self.capacity
        self.size -= 1
        return True
```
**Time:** O(1) for all ops | **Space:** O(k) | **Frequency:** Medium | **Tier:** 2

---

### Q73. LRU Cache
```python
from collections import OrderedDict

class LRUCache:
    def __init__(self, capacity: int):
        self.cache = OrderedDict()
        self.capacity = capacity
    
    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        self.cache.move_to_end(key)
        return self.cache[key]
    
    def put(self, key: int, value: int):
        if key in self.cache:
            self.cache.move_to_end(key)
        self.cache[key] = value
        if len(self.cache) > self.capacity:
            self.cache.popitem(last=False)
```
**Time:** O(1) for get/put | **Space:** O(capacity) | **Frequency:** High | **Tier:** 1

---

### Q74. Decode string
```python
def decode_string(s: str) -> str:
    stack = []
    current_num = 0
    current_str = ""
    for char in s:
        if char.isdigit():
            current_num = current_num * 10 + int(char)
        elif char == '[':
            stack.append((current_str, current_num))
            current_str = ""
            current_num = 0
        elif char == ']':
            prev_str, num = stack.pop()
            current_str = prev_str + num * current_str
        else:
            current_str += char
    return current_str
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q75. Daily temperatures
```python
def daily_temperatures(temps: list) -> list:
    stack = []
    result = [0] * len(temps)
    for i in range(len(temps)):
        while stack and temps[i] > temps[stack[-1]]:
            prev_idx = stack.pop()
            result[prev_idx] = i - prev_idx
        stack.append(i)
    return result
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q76. Remove K digits
```python
def remove_k_digits(num: str, k: int) -> str:
    stack = []
    for digit in num:
        while stack and k > 0 and stack[-1] > digit:
            stack.pop()
            k -= 1
        stack.append(digit)
    if k > 0:
        stack = stack[:-k]
    result = "".join(stack).lstrip('0')
    return result if result else "0"
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q77. Largest rectangle in histogram
```python
def largest_rectangle(heights: list) -> int:
    stack = []
    max_area = 0
    for i in range(len(heights)):
        while stack and heights[i] < heights[stack[-1]]:
            h_idx = stack.pop()
            h = heights[h_idx]
            w = i if not stack else i - stack[-1] - 1
            max_area = max(max_area, h * w)
        stack.append(i)
    while stack:
        h_idx = stack.pop()
        h = heights[h_idx]
        w = len(heights) if not stack else len(heights) - stack[-1] - 1
        max_area = max(max_area, h * w)
    return max_area
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q78. Implement deque
```python
from collections import deque

class Deque:
    def __init__(self):
        self.items = deque()
    
    def add_front(self, item):
        self.items.appendleft(item)
    
    def add_rear(self, item):
        self.items.append(item)
    
    def remove_front(self):
        return self.items.popleft() if not self.is_empty() else None
    
    def remove_rear(self):
        return self.items.pop() if not self.is_empty() else None
    
    def is_empty(self):
        return len(self.items) == 0
```
**Time:** O(1) for all ops | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q79. Implement priority queue
```python
import heapq

class PriorityQueue:
    def __init__(self):
        self.heap = []
    
    def push(self, val: int, priority: int):
        heapq.heappush(self.heap, (priority, val))
    
    def pop(self):
        return heapq.heappop(self.heap)[1] if self.heap else None
    
    def is_empty(self):
        return len(self.heap) == 0
```
**Time:** O(log n) for push/pop | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q80. Trapping rain water II
```python
import heapq

def trap_rain_water_2d(elevation_map: list) -> int:
    if not elevation_map or not elevation_map[0]:
        return 0
    m, n = len(elevation_map), len(elevation_map[0])
    visited = [[False] * n for _ in range(m)]
    heap = []
    for i in range(m):
        for j in range(n):
            if i == 0 or i == m - 1 or j == 0 or j == n - 1:
                heapq.heappush(heap, (elevation_map[i][j], i, j))
                visited[i][j] = True
    water = 0
    directions = [(0, 1), (0, -1), (1, 0), (-1, 0)]
    while heap:
        height, i, j = heapq.heappop(heap)
        for di, dj in directions:
            ni, nj = i + di, j + dj
            if 0 <= ni < m and 0 <= nj < n and not visited[ni][nj]:
                water += max(0, height - elevation_map[ni][nj])
                heapq.heappush(heap, (max(height, elevation_map[ni][nj]), ni, nj))
                visited[ni][nj] = True
    return water
```
**Time:** O(m * n * log(m * n)) | **Space:** O(m * n) | **Frequency:** Low | **Tier:** 3

---

## SECTION 3: LinkedLists (Q81–Q90)

### Q81. Reverse a linked list
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

def reverse_list(head: ListNode) -> ListNode:
    prev = None
    current = head
    while current:
        next_temp = current.next
        current.next = prev
        prev = current
        current = next_temp
    return prev
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Very High | **Tier:** 1

---

### Q82. Detect cycle in linked list
```python
def has_cycle(head: ListNode) -> bool:
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            return True
    return False
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Very High | **Tier:** 1

---

### Q83. Find cycle start node
```python
def find_cycle_start(head: ListNode) -> ListNode:
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
        if slow == fast:
            break
    if not fast or not fast.next:
        return None
    slow = head
    while slow != fast:
        slow = slow.next
        fast = fast.next
    return slow
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q84. Merge two sorted linked lists
```python
def merge_sorted_lists(l1: ListNode, l2: ListNode) -> ListNode:
    dummy = ListNode(0)
    current = dummy
    while l1 and l2:
        if l1.val <= l2.val:
            current.next = l1
            l1 = l1.next
        else:
            current.next = l2
            l2 = l2.next
        current = current.next
    current.next = l1 if l1 else l2
    return dummy.next
```
**Time:** O(n + m) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q85. Remove nth node from end
```python
def remove_nth_from_end(head: ListNode, n: int) -> ListNode:
    dummy = ListNode(0)
    dummy.next = head
    first = dummy
    second = dummy
    for _ in range(n + 1):
        if first is None:
            return head
        first = first.next
    while first:
        first = first.next
        second = second.next
    second.next = second.next.next
    return dummy.next
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q86. Partition list
```python
def partition(head: ListNode, x: int) -> ListNode:
    dummy1 = ListNode(0)
    dummy2 = ListNode(0)
    p1, p2 = dummy1, dummy2
    current = head
    while current:
        if current.val < x:
            p1.next = current
            p1 = p1.next
        else:
            p2.next = current
            p2 = p2.next
        current = current.next
    p2.next = None
    p1.next = dummy2.next
    return dummy1.next
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q87. Palindrome linked list
```python
def is_palindrome(head: ListNode) -> bool:
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    prev = None
    while slow:
        next_temp = slow.next
        slow.next = prev
        prev = slow
        slow = next_temp
    left, right = head, prev
    while right:
        if left.val != right.val:
            return False
        left = left.next
        right = right.next
    return True
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q88. Flatten multilevel linked list
```python
class Node:
    def __init__(self, val, next=None, child=None):
        self.val = val
        self.next = next
        self.child = child

def flatten(head: Node) -> Node:
    def dfs(node):
        if not node:
            return None
        if not node.child:
            if node.next:
                return dfs(node.next)
            return node
        child_tail = dfs(node.child)
        next_head = node.next
        node.next = node.child
        node.child = None
        child_tail.next = next_head
        return dfs(next_head)
    
    dfs(head)
    return head
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Medium | **Tier:** 2

---

### Q89. Reorder list
```python
def reorder_list(head: ListNode) -> None:
    if not head or not head.next:
        return
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    prev = None
    current = slow
    while current:
        next_temp = current.next
        current.next = prev
        prev = current
        current = next_temp
    first, second = head, prev
    while second.next:
        next1, next2 = first.next, second.next
        first.next = second
        second.next = next1
        first, second = next1, next2
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q90. Copy list with random pointer
```python
class Node:
    def __init__(self, x: int, next=None, random=None):
        self.val = x
        self.next = next
        self.random = random

def copy_random_list(head: Node) -> Node:
    if not head:
        return None
    mapping = {}
    current = head
    while current:
        mapping[current] = Node(current.val)
        current = current.next
    current = head
    while current:
        if current.next:
            mapping[current].next = mapping[current.next]
        if current.random:
            mapping[current].random = mapping[current.random]
        current = current.next
    return mapping[head]
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

## NEXT STEPS

After mastering Q51–90:
- Practice each problem 2–3 times
- Solve without looking at solutions
- Move to **Intermediate Part 2 (Q91–120)** when confident
