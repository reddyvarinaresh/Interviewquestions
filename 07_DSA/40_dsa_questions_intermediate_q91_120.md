# DSA Questions — Intermediate Part 2 (Q91–Q120)
> **Phase 2: Intermediate Techniques** | LinkedLists, Two Pointers, Sliding Window
> **Difficulty:** Easy-Medium to Medium | **Time per question:** 25–40 minutes
> **Companies:** Product + Captive | **Frequency:** High (Tier 1–2)

---

## SECTION 1: LinkedLists Continued (Q91–Q100)

### Q91. Intersection of two linked lists
```python
def get_intersection_node(headA: ListNode, headB: ListNode) -> ListNode:
    if not headA or not headB:
        return None
    a, b = headA, headB
    while a != b:
        a = a.next if a else headB
        b = b.next if b else headA
    return a
```
**Time:** O(n + m) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q92. Reverse linked list II
```python
def reverse_between(head: ListNode, m: int, n: int) -> ListNode:
    if m == n:
        return head
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy
    for _ in range(m - 1):
        prev = prev.next
    current = prev.next
    for _ in range(n - m):
        next_temp = current.next
        current.next = next_temp.next
        next_temp.next = prev.next
        prev.next = next_temp
    return dummy.next
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q93. Swap nodes in pairs
```python
def swap_pairs(head: ListNode) -> ListNode:
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy
    while prev.next and prev.next.next:
        first = prev.next
        second = prev.next.next
        prev.next = second
        first.next = second.next
        second.next = first
        prev = first
    return dummy.next
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q94. Add two numbers (LinkedList)
```python
def add_two_numbers(l1: ListNode, l2: ListNode) -> ListNode:
    dummy = ListNode(0)
    current = dummy
    carry = 0
    while l1 or l2 or carry:
        val1 = l1.val if l1 else 0
        val2 = l2.val if l2 else 0
        total = val1 + val2 + carry
        carry = total // 10
        current.next = ListNode(total % 10)
        current = current.next
        l1 = l1.next if l1 else None
        l2 = l2.next if l2 else None
    return dummy.next
```
**Time:** O(max(n, m)) | **Space:** O(max(n, m)) | **Frequency:** High | **Tier:** 1

---

### Q95. Sort list
```python
def sort_list(head: ListNode) -> ListNode:
    if not head or not head.next:
        return head
    slow = fast = head
    prev = None
    while fast and fast.next:
        prev = slow
        slow = slow.next
        fast = fast.next.next
    prev.next = None
    left = sort_list(head)
    right = sort_list(slow)
    return merge(left, right)

def merge(l1: ListNode, l2: ListNode) -> ListNode:
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
**Time:** O(n log n) | **Space:** O(log n) | **Frequency:** High | **Tier:** 1

---

### Q96. Delete duplicates from sorted list
```python
def delete_duplicates(head: ListNode) -> ListNode:
    current = head
    while current and current.next:
        if current.val == current.next.val:
            current.next = current.next.next
        else:
            current = current.next
    return head
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q97. Delete all duplicates from sorted list
```python
def delete_duplicates_all(head: ListNode) -> ListNode:
    dummy = ListNode(0)
    dummy.next = head
    prev = dummy
    current = head
    while current:
        if current.next and current.val == current.next.val:
            while current.next and current.val == current.next.val:
                current = current.next
            prev.next = current.next
        else:
            prev = current
        current = current.next
    return dummy.next
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q98. Rotate list
```python
def rotate_list(head: ListNode, k: int) -> ListNode:
    if not head or not head.next or k == 0:
        return head
    length = 1
    current = head
    while current.next:
        current = current.next
        length += 1
    k = k % length
    if k == 0:
        return head
    current.next = head
    current = head
    for _ in range(length - k):
        current = current.next
    head = current.next
    current.next = None
    return head
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q99. Odd even linked list
```python
def odd_even_list(head: ListNode) -> ListNode:
    if not head or not head.next:
        return head
    odd = head
    even = head.next
    even_head = even
    while even and even.next:
        odd.next = even.next
        odd = odd.next
        even.next = odd.next
        even = even.next
    odd.next = even_head
    return head
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q100. Middle of linked list
```python
def middle_node(head: ListNode) -> ListNode:
    slow = fast = head
    while fast and fast.next:
        slow = slow.next
        fast = fast.next.next
    return slow
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

## SECTION 2: Two Pointers & Sliding Window (Q101–Q120)

### Q101. Remove duplicates from sorted array II
```python
def remove_duplicates_ii(arr: list) -> int:
    if len(arr) <= 2:
        return len(arr)
    idx = 2
    for i in range(2, len(arr)):
        if arr[i] != arr[idx - 2]:
            arr[idx] = arr[i]
            idx += 1
    return idx
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q102. Permutation in string
```python
def is_permutation(s1: str, s2: str) -> bool:
    if len(s1) > len(s2):
        return False
    count1 = {}
    count2 = {}
    for i in range(len(s1)):
        count1[s1[i]] = count1.get(s1[i], 0) + 1
        count2[s2[i]] = count2.get(s2[i], 0) + 1
    if count1 == count2:
        return True
    for i in range(len(s1), len(s2)):
        count2[s2[i]] = count2.get(s2[i], 0) + 1
        count2[s2[i - len(s1)]] -= 1
        if count2[s2[i - len(s1)]] == 0:
            del count2[s2[i - len(s1)]]
        if count1 == count2:
            return True
    return False
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q103. Minimum size subarray sum
```python
def min_subarray_len(target: int, nums: list) -> int:
    left = 0
    current_sum = 0
    min_len = float('inf')
    for right in range(len(nums)):
        current_sum += nums[right]
        while current_sum >= target:
            min_len = min(min_len, right - left + 1)
            current_sum -= nums[left]
            left += 1
    return min_len if min_len != float('inf') else 0
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q104. Fruit into baskets
```python
def total_fruit(fruits: list) -> int:
    left = 0
    basket = {}
    max_fruits = 0
    for right in range(len(fruits)):
        basket[fruits[right]] = basket.get(fruits[right], 0) + 1
        while len(basket) > 2:
            basket[fruits[left]] -= 1
            if basket[fruits[left]] == 0:
                del basket[fruits[left]]
            left += 1
        max_fruits = max(max_fruits, right - left + 1)
    return max_fruits
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q105. Longest repeating character replacement
```python
def character_replacement(s: str, k: int) -> int:
    count = {}
    left = 0
    max_len = 0
    max_count = 0
    for right in range(len(s)):
        count[s[right]] = count.get(s[right], 0) + 1
        max_count = max(max_count, count[s[right]])
        while right - left + 1 - max_count > k:
            count[s[left]] -= 1
            left += 1
        max_len = max(max_len, right - left + 1)
    return max_len
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q106. Substring with concatenation of all words
```python
def find_substring(s: str, words: list) -> list:
    if not s or not words:
        return []
    word_len = len(words[0])
    word_count = len(words)
    total_len = word_len * word_count
    word_map = {}
    for word in words:
        word_map[word] = word_map.get(word, 0) + 1
    result = []
    for i in range(len(s) - total_len + 1):
        substring = s[i:i + total_len]
        seen = {}
        for j in range(0, total_len, word_len):
            word = substring[j:j + word_len]
            if word not in word_map:
                break
            seen[word] = seen.get(word, 0) + 1
            if seen[word] > word_map[word]:
                break
        else:
            if seen == word_map:
                result.append(i)
    return result
```
**Time:** O(n * m) | **Space:** O(m) | **Frequency:** Medium | **Tier:** 2

---

### Q107. Max consecutive ones
```python
def find_max_consecutive_ones(nums: list) -> int:
    max_count = 0
    current_count = 0
    for num in nums:
        if num == 1:
            current_count += 1
            max_count = max(max_count, current_count)
        else:
            current_count = 0
    return max_count
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Low | **Tier:** 3

---

### Q108. Max consecutive ones III
```python
def longest_ones(nums: list, k: int) -> int:
    left = 0
    zeros = 0
    max_len = 0
    for right in range(len(nums)):
        if nums[right] == 0:
            zeros += 1
        while zeros > k:
            if nums[left] == 0:
                zeros -= 1
            left += 1
        max_len = max(max_len, right - left + 1)
    return max_len
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q109. Number of subarrays with product less than k
```python
def num_subarray_product_less_than_k(nums: list, k: int) -> int:
    if k <= 1:
        return 0
    left = 0
    product = 1
    count = 0
    for right in range(len(nums)):
        product *= nums[right]
        while product >= k:
            product //= nums[left]
            left += 1
        count += right - left + 1
    return count
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q110. Minimum window substring (revisited)
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
**Time:** O(|S| + |T|) | **Space:** O(|S| + |T|) | **Frequency:** High | **Tier:** 1

---

### Q111. Longest substring with at most k distinct characters
```python
def longest_substring_k_distinct(s: str, k: int) -> int:
    if k == 0 or not s:
        return 0
    char_count = {}
    left = 0
    max_len = 0
    for right in range(len(s)):
        char_count[s[right]] = char_count.get(s[right], 0) + 1
        while len(char_count) > k:
            char_count[s[left]] -= 1
            if char_count[s[left]] == 0:
                del char_count[s[left]]
            left += 1
        max_len = max(max_len, right - left + 1)
    return max_len
```
**Time:** O(n) | **Space:** O(k) | **Frequency:** Medium | **Tier:** 2

---

### Q112. Longest repeating substring
```python
def longest_repeating_substring(s: str) -> str:
    if not s:
        return ""
    left = 0
    max_len = 0
    max_start = 0
    char_index = {}
    for right in range(len(s)):
        if s[right] in char_index and char_index[s[right]] >= left:
            left = char_index[s[right]] + 1
        char_index[s[right]] = right
        if right - left + 1 > max_len:
            max_len = right - left + 1
            max_start = left
    return s[max_start:max_start + max_len]
```
**Time:** O(n) | **Space:** O(min(n, 26)) | **Frequency:** Low | **Tier:** 3

---

### Q113. Isomorphic strings
```python
def is_isomorphic(s: str, t: str) -> bool:
    if len(s) != len(t):
        return False
    s_map = {}
    t_map = {}
    for i in range(len(s)):
        if s[i] in s_map:
            if s_map[s[i]] != t[i]:
                return False
        else:
            s_map[s[i]] = t[i]
        if t[i] in t_map:
            if t_map[t[i]] != s[i]:
                return False
        else:
            t_map[t[i]] = s[i]
    return True
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q114. Word pattern
```python
def word_pattern(pattern: str, s: str) -> bool:
    words = s.split()
    if len(pattern) != len(words):
        return False
    p_map = {}
    w_map = {}
    for p, w in zip(pattern, words):
        if p in p_map:
            if p_map[p] != w:
                return False
        else:
            p_map[p] = w
        if w in w_map:
            if w_map[w] != p:
                return False
        else:
            w_map[w] = p
    return True
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q115. Minimum length of string after deleting similar ends
```python
def minimum_length(s: str) -> int:
    left = 0
    right = len(s) - 1
    while left < right and s[left] == s[right]:
        char = s[left]
        while left <= right and s[left] == char:
            left += 1
        while left <= right and s[right] == char:
            right -= 1
    return right - left + 1
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Low | **Tier:** 3

---

### Q116. Backspace string compare
```python
def backspace_compare(s: str, t: str) -> bool:
    def process(string):
        stack = []
        for char in string:
            if char != '#':
                stack.append(char)
            elif stack:
                stack.pop()
        return "".join(stack)
    return process(s) == process(t)
```
**Time:** O(n + m) | **Space:** O(n + m) | **Frequency:** Medium | **Tier:** 2

---

### Q117. Squares of sorted array (revisited)
```python
def sorted_squares(arr: list) -> list:
    result = [0] * len(arr)
    left, right = 0, len(arr) - 1
    for i in range(len(arr) - 1, -1, -1):
        if abs(arr[left]) > abs(arr[right]):
            result[i] = arr[left] ** 2
            left += 1
        else:
            result[i] = arr[right] ** 2
            right -= 1
    return result
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q118. Reverse only letters
```python
def reverse_only_letters(s: str) -> str:
    s = list(s)
    left, right = 0, len(s) - 1
    while left < right:
        if not s[left].isalpha():
            left += 1
        elif not s[right].isalpha():
            right -= 1
        else:
            s[left], s[right] = s[right], s[left]
            left += 1
            right -= 1
    return "".join(s)
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Low | **Tier:** 3

---

### Q119. Boats to save people
```python
def num_rescue_boats(people: list, limit: int) -> int:
    people.sort()
    left, right = 0, len(people) - 1
    boats = 0
    while left <= right:
        if people[left] + people[right] <= limit:
            left += 1
        right -= 1
        boats += 1
    return boats
```
**Time:** O(n log n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q120. Move zeros to end (revisited)
```python
def move_zeros(arr: list) -> list:
    non_zero_idx = 0
    for i in range(len(arr)):
        if arr[i] != 0:
            arr[non_zero_idx], arr[i] = arr[i], arr[non_zero_idx]
            non_zero_idx += 1
    return arr
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

## SUMMARY: Q91–Q120

| # | Problem | Time | Space | Tier |
|---|---------|------|-------|------|
| 91 | Intersection of lists | O(n+m) | O(1) | 1 |
| 92 | Reverse list II | O(n) | O(1) | 2 |
| 93 | Swap pairs | O(n) | O(1) | 1 |
| 94 | Add two numbers | O(max(n,m)) | O(max(n,m)) | 1 |
| 95 | Sort list | O(n log n) | O(log n) | 1 |
| 96 | Delete duplicates | O(n) | O(1) | 1 |
| 97 | Delete all duplicates | O(n) | O(1) | 2 |
| 98 | Rotate list | O(n) | O(1) | 2 |
| 99 | Odd even list | O(n) | O(1) | 2 |
| 100 | Middle of list | O(n) | O(1) | 1 |
| 101 | Remove dup II | O(n) | O(1) | 1 |
| 102 | Permutation | O(n) | O(1) | 1 |
| 103 | Min subarray sum | O(n) | O(1) | 1 |
| 104 | Fruit baskets | O(n) | O(1) | 2 |
| 105 | Char replacement | O(n) | O(1) | 1 |
| 106 | Substring concat | O(n*m) | O(m) | 2 |
| 107 | Max consecutive 1s | O(n) | O(1) | 3 |
| 108 | Max consecutive 1s III | O(n) | O(1) | 2 |
| 109 | Product < k | O(n) | O(1) | 2 |
| 110 | Min window | O(\|S\|+\|T\|) | O(\|S\|+\|T\|) | 1 |
| 111 | Longest k distinct | O(n) | O(k) | 2 |
| 112 | Longest repeating | O(n) | O(n) | 3 |
| 113 | Isomorphic strings | O(n) | O(1) | 2 |
| 114 | Word pattern | O(n) | O(n) | 2 |
| 115 | Min length delete | O(n) | O(1) | 3 |
| 116 | Backspace compare | O(n+m) | O(n+m) | 2 |
| 117 | Sorted squares | O(n) | O(n) | 1 |
| 118 | Reverse letters | O(n) | O(n) | 3 |
| 119 | Boats to save | O(n log n) | O(1) | 2 |
| 120 | Move zeros | O(n) | O(1) | 1 |

---

## NEXT STEPS

After mastering Q91–120:
- You've completed **Phase 2: Intermediate Techniques**
- Move to **Advanced (Q121–180)** for Trees, Graphs, DP, Greedy
- Review Phase 1 & 2 concepts weekly
