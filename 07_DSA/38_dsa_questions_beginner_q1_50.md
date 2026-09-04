# DSA Questions — Beginner (Q1–50)
> **Phase 1: Foundations** | Arrays, Strings, Hashing, Basic Math
> **Difficulty:** Easy to Easy-Medium | **Time per question:** 20–35 minutes
> **Companies:** All (Product + Captive) | **Frequency:** Very High (Tier 1)

---

## SECTION 1: Arrays (Q1–Q15)

### Q1. Reverse an array
**Problem:** Given an array, reverse it in-place.
```python
def reverse_array(arr: list) -> list:
    left, right = 0, len(arr) - 1
    while left < right:
        arr[left], arr[right] = arr[right], arr[left]
        left += 1
        right -= 1
    return arr

# [1, 2, 3, 4, 5] → [5, 4, 3, 2, 1]
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Very High | **Tier:** 1

---

### Q2. Find the second largest element
**Problem:** Find the second largest element in an unsorted array.
```python
def second_largest(arr: list) -> int:
    if len(arr) < 2:
        return -1
    first = second = float('-inf')
    for num in arr:
        if num > first:
            second = first
            first = num
        elif num > second and num != first:
            second = num
    return second if second != float('-inf') else -1

# [10, 5, 8, 12, 3] → 10
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q3. Rotate array by k positions
**Problem:** Rotate array to the right by k steps.
```python
def rotate_array(arr: list, k: int) -> list:
    k = k % len(arr)
    arr.reverse()
    arr[:k] = reversed(arr[:k])
    arr[k:] = reversed(arr[k:])
    return arr

# [1, 2, 3, 4, 5], k=2 → [4, 5, 1, 2, 3]
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q4. Find duplicates in array
**Problem:** Find all duplicate elements in an array.
```python
def find_duplicates(arr: list) -> list:
    seen = set()
    duplicates = set()
    for num in arr:
        if num in seen:
            duplicates.add(num)
        seen.add(num)
    return list(duplicates)

# [1, 2, 2, 3, 3, 3] → [2, 3]
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q5. Move all zeros to the end
**Problem:** Move all zeros to the end while maintaining order of non-zero elements.
```python
def move_zeros(arr: list) -> list:
    non_zero_idx = 0
    for i in range(len(arr)):
        if arr[i] != 0:
            arr[non_zero_idx], arr[i] = arr[i], arr[non_zero_idx]
            non_zero_idx += 1
    return arr

# [1, 0, 2, 0, 3, 0, 4] → [1, 2, 3, 4, 0, 0, 0]
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q6. Find missing number (1 to N)
**Problem:** Given array of n-1 numbers from 1 to n, find the missing number.
```python
def find_missing(arr: list) -> int:
    n = len(arr) + 1
    expected_sum = n * (n + 1) // 2
    actual_sum = sum(arr)
    return expected_sum - actual_sum

# [1, 2, 4, 5] → 3
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q7. Find the maximum product of two elements
**Problem:** Find the maximum product of any two elements in the array.
```python
def max_product(arr: list) -> int:
    if len(arr) < 2:
        return -1
    arr.sort()
    return max(arr[0] * arr[1], arr[-1] * arr[-2])

# [1, 5, 0, 3, 4, 5] → 25 (5 * 5)
```
**Time:** O(n log n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q8. Merge two sorted arrays
**Problem:** Merge two sorted arrays into one sorted array.
```python
def merge_sorted(arr1: list, arr2: list) -> list:
    result = []
    i = j = 0
    while i < len(arr1) and j < len(arr2):
        if arr1[i] <= arr2[j]:
            result.append(arr1[i])
            i += 1
        else:
            result.append(arr2[j])
            j += 1
    result.extend(arr1[i:])
    result.extend(arr2[j:])
    return result

# [1, 3, 5], [2, 4, 6] → [1, 2, 3, 4, 5, 6]
```
**Time:** O(n + m) | **Space:** O(n + m) | **Frequency:** High | **Tier:** 1

---

### Q9. Find the peak element
**Problem:** Find an element that is greater than its neighbors.
```python
def find_peak(arr: list) -> int:
    for i in range(len(arr)):
        if (i == 0 or arr[i] > arr[i-1]) and (i == len(arr)-1 or arr[i] > arr[i+1]):
            return arr[i]
    return -1

# [1, 3, 20, 4, 1, 0] → 20
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q10. Count pairs with given sum
**Problem:** Count pairs in array that sum to a target value.
```python
def count_pairs(arr: list, target: int) -> int:
    seen = set()
    count = 0
    for num in arr:
        complement = target - num
        if complement in seen:
            count += 1
        seen.add(num)
    return count

# [1, 5, 7, -1, 5], target=6 → 2 (1+5, 7-1)
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q11. Find the majority element (appears > n/2 times)
**Problem:** Find element appearing more than n/2 times.
```python
def majority_element(arr: list) -> int:
    count = 0
    candidate = None
    for num in arr:
        if count == 0:
            candidate = num
        count += (1 if num == candidate else -1)
    return candidate

# [3, 2, 3] → 3
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q12. Remove duplicates from sorted array
**Problem:** Remove duplicates in-place from sorted array.
```python
def remove_duplicates(arr: list) -> int:
    if not arr:
        return 0
    idx = 0
    for i in range(1, len(arr)):
        if arr[i] != arr[idx]:
            idx += 1
            arr[idx] = arr[i]
    return idx + 1

# [1, 1, 2, 2, 3] → returns 3, arr becomes [1, 2, 3, ...]
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q13. Intersection of two arrays
**Problem:** Find common elements in two arrays.
```python
def intersection(arr1: list, arr2: list) -> list:
    set1 = set(arr1)
    result = []
    for num in arr2:
        if num in set1 and num not in result:
            result.append(num)
    return result

# [1, 2, 2, 1], [2, 2] → [2]
```
**Time:** O(n + m) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q14. Subarray with maximum sum (Kadane's Algorithm)
**Problem:** Find contiguous subarray with largest sum.
```python
def max_subarray_sum(arr: list) -> int:
    max_sum = current_sum = arr[0]
    for num in arr[1:]:
        current_sum = max(num, current_sum + num)
        max_sum = max(max_sum, current_sum)
    return max_sum

# [-2, 1, -3, 4, -1, 2, 1, -5, 4] → 6 (subarray [4, -1, 2, 1])
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Very High | **Tier:** 1

---

### Q15. Separate even and odd numbers
**Problem:** Rearrange array so all even numbers come before odd numbers.
```python
def separate_even_odd(arr: list) -> list:
    left = 0
    for i in range(len(arr)):
        if arr[i] % 2 == 0:
            arr[left], arr[i] = arr[i], arr[left]
            left += 1
    return arr

# [1, 2, 3, 4, 5, 6] → [2, 4, 6, 1, 3, 5]
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

## SECTION 2: Strings (Q16–Q30)

### Q16. Reverse a string
**Problem:** Reverse a string.
```python
def reverse_string(s: str) -> str:
    return s[::-1]

# "hello" → "olleh"
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Very High | **Tier:** 1

---

### Q17. Check if string is palindrome
**Problem:** Check if string reads same forwards and backwards.
```python
def is_palindrome(s: str) -> bool:
    s = s.lower().replace(" ", "")
    return s == s[::-1]

# "racecar" → True, "hello" → False
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Very High | **Tier:** 1

---

### Q18. Check if two strings are anagrams
**Problem:** Check if two strings are anagrams (same characters, different order).
```python
from collections import Counter

def are_anagrams(s1: str, s2: str) -> bool:
    return Counter(s1.lower()) == Counter(s2.lower())

# "listen", "silent" → True
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q19. Find first non-repeating character
**Problem:** Find the first character that appears only once.
```python
from collections import Counter

def first_non_repeating(s: str) -> str:
    count = Counter(s)
    for ch in s:
        if count[ch] == 1:
            return ch
    return ""

# "aabbcde" → "c"
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q20. Count vowels and consonants
**Problem:** Count vowels and consonants in a string.
```python
def count_vowels_consonants(s: str) -> dict:
    vowels = set("aeiouAEIOU")
    v = sum(1 for c in s if c in vowels)
    c = sum(1 for c in s if c.isalpha() and c not in vowels)
    return {"vowels": v, "consonants": c}

# "Hello World" → {"vowels": 3, "consonants": 7}
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q21. Remove duplicate characters
**Problem:** Remove duplicate characters from string.
```python
def remove_duplicates(s: str) -> str:
    seen = set()
    return "".join(c for c in s if not (c in seen or seen.add(c)))

# "programming" → "progamin"
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q22. Reverse words in sentence
**Problem:** Reverse the order of words in a sentence.
```python
def reverse_words(s: str) -> str:
    return " ".join(s.split()[::-1])

# "Hello World Python" → "Python World Hello"
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q23. Check if string is rotation of another
**Problem:** Check if s2 is rotation of s1.
```python
def is_rotation(s1: str, s2: str) -> bool:
    if len(s1) != len(s2):
        return False
    return s2 in s1 + s1

# "waterbottle", "erbottlewat" → True
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q24. Longest substring without repeating characters
**Problem:** Find length of longest substring without repeating characters.
```python
def longest_substring(s: str) -> int:
    char_index = {}
    max_len = 0
    start = 0
    for i, char in enumerate(s):
        if char in char_index and char_index[char] >= start:
            start = char_index[char] + 1
        char_index[char] = i
        max_len = max(max_len, i - start + 1)
    return max_len

# "abcabcbb" → 3 ("abc")
```
**Time:** O(n) | **Space:** O(min(n, 26)) | **Frequency:** High | **Tier:** 1

---

### Q25. Character frequency counter
**Problem:** Count frequency of each character in string.
```python
from collections import Counter

def char_frequency(s: str) -> dict:
    return dict(Counter(s))

# "hello" → {'h': 1, 'e': 1, 'l': 2, 'o': 1}
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q26. Check if string has all unique characters
**Problem:** Check if all characters in string are unique.
```python
def has_unique_chars(s: str) -> bool:
    return len(s) == len(set(s))

# "abcdef" → True, "hello" → False
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q27. Capitalize first character of each word
**Problem:** Capitalize first character of each word.
```python
def capitalize_words(s: str) -> str:
    return " ".join(word.capitalize() for word in s.split())

# "hello world" → "Hello World"
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q28. Count occurrences of substring
**Problem:** Count how many times substring appears in string.
```python
def count_substring(s: str, sub: str) -> int:
    count = 0
    start = 0
    while True:
        start = s.find(sub, start)
        if start == -1:
            break
        count += 1
        start += 1
    return count

# "ababa", "aba" → 2
```
**Time:** O(n * m) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q29. Remove spaces from string
**Problem:** Remove all spaces from string.
```python
def remove_spaces(s: str) -> str:
    return s.replace(" ", "")

# "hello world" → "helloworld"
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Low | **Tier:** 3

---

### Q30. Check if string is valid number
**Problem:** Check if string represents a valid number.
```python
def is_valid_number(s: str) -> bool:
    try:
        float(s)
        return True
    except ValueError:
        return False

# "123" → True, "12.34" → True, "abc" → False
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

## SECTION 3: Hashing & Two Pointers (Q31–Q40)

### Q31. Two Sum
**Problem:** Find two numbers that add up to target.
```python
def two_sum(arr: list, target: int) -> list:
    seen = set()
    for num in arr:
        complement = target - num
        if complement in seen:
            return [complement, num]
        seen.add(num)
    return []

# [2, 7, 11, 15], target=9 → [2, 7]
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Very High | **Tier:** 1

---

### Q32. Three Sum
**Problem:** Find three numbers that add up to target.
```python
def three_sum(arr: list, target: int) -> list:
    arr.sort()
    result = []
    for i in range(len(arr) - 2):
        left, right = i + 1, len(arr) - 1
        while left < right:
            total = arr[i] + arr[left] + arr[right]
            if total == target:
                result.append([arr[i], arr[left], arr[right]])
                left += 1
                right -= 1
            elif total < target:
                left += 1
            else:
                right -= 1
    return result

# [1, 0, -1, 0, -2, 2], target=0 → [[-2, 0, 2], [-2, 1, 1], [-1, 0, 1]]
```
**Time:** O(n²) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q33. Container with most water
**Problem:** Find two lines that form container with maximum area.
```python
def max_area(heights: list) -> int:
    left, right = 0, len(heights) - 1
    max_area = 0
    while left < right:
        width = right - left
        height = min(heights[left], heights[right])
        area = width * height
        max_area = max(max_area, area)
        if heights[left] < heights[right]:
            left += 1
        else:
            right -= 1
    return max_area

# [1, 8, 6, 2, 5, 4, 8, 3, 7] → 49
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q34. Valid parentheses
**Problem:** Check if parentheses are balanced.
```python
def is_valid_parentheses(s: str) -> bool:
    stack = []
    pairs = {'(': ')', '{': '}', '[': ']'}
    for char in s:
        if char in pairs:
            stack.append(char)
        else:
            if not stack or pairs[stack.pop()] != char:
                return False
    return len(stack) == 0

# "()" → True, "([)]" → False
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Very High | **Tier:** 1

---

### Q35. Trapping rain water
**Problem:** Calculate trapped rainwater between bars.
```python
def trap_water(heights: list) -> int:
    if not heights:
        return 0
    left_max = [0] * len(heights)
    right_max = [0] * len(heights)
    left_max[0] = heights[0]
    right_max[-1] = heights[-1]
    for i in range(1, len(heights)):
        left_max[i] = max(left_max[i-1], heights[i])
    for i in range(len(heights)-2, -1, -1):
        right_max[i] = max(right_max[i+1], heights[i])
    water = 0
    for i in range(len(heights)):
        water += min(left_max[i], right_max[i]) - heights[i]
    return water

# [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1] → 6
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q36. Merge sorted array in-place
**Problem:** Merge two sorted arrays in-place.
```python
def merge_sorted_inplace(nums1: list, m: int, nums2: list, n: int) -> None:
    p1, p2, p = m - 1, n - 1, m + n - 1
    while p1 >= 0 and p2 >= 0:
        if nums1[p1] > nums2[p2]:
            nums1[p] = nums1[p1]
            p1 -= 1
        else:
            nums1[p] = nums2[p2]
            p2 -= 1
        p -= 1
    while p2 >= 0:
        nums1[p] = nums2[p2]
        p2 -= 1
        p -= 1

# nums1 = [1, 2, 3, 0, 0, 0], m = 3, nums2 = [2, 5, 6], n = 3
# Result: [1, 2, 2, 3, 5, 6]
```
**Time:** O(m + n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q37. Squares of sorted array
**Problem:** Return array of squares of sorted array.
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

# [-4, -1, 0, 3, 10] → [0, 1, 9, 16, 100]
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q38. Valid palindrome (alphanumeric only)
**Problem:** Check if string is palindrome (ignore non-alphanumeric).
```python
def is_valid_palindrome(s: str) -> bool:
    s = ''.join(c.lower() for c in s if c.isalnum())
    return s == s[::-1]

# "A man, a plan, a canal: Panama" → True
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q39. Remove nth node from end of list (LinkedList)
**Problem:** Remove nth node from end of linked list.
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

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
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q40. Sliding window maximum
**Problem:** Find maximum in each sliding window of size k.
```python
from collections import deque

def sliding_window_max(arr: list, k: int) -> list:
    if not arr or k == 0:
        return []
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

# [1, 3, -1, -3, 5, 3, 6, 7], k=3 → [3, 3, 5, 5, 6, 7]
```
**Time:** O(n) | **Space:** O(k) | **Frequency:** Medium | **Tier:** 2

---

## SECTION 4: Basic Math (Q41–Q50)

### Q41. Fibonacci number
**Problem:** Find nth Fibonacci number.
```python
def fibonacci(n: int) -> int:
    if n <= 1:
        return n
    a, b = 0, 1
    for _ in range(2, n + 1):
        a, b = b, a + b
    return b

# fibonacci(6) → 8
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q42. Check if number is prime
**Problem:** Check if number is prime.
```python
def is_prime(n: int) -> bool:
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    for i in range(3, int(n**0.5) + 1, 2):
        if n % i == 0:
            return False
    return True

# is_prime(17) → True
```
**Time:** O(√n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q43. Factorial
**Problem:** Calculate factorial of n.
```python
def factorial(n: int) -> int:
    if n <= 1:
        return 1
    result = 1
    for i in range(2, n + 1):
        result *= i
    return result

# factorial(5) → 120
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q44. Power of number
**Problem:** Calculate x^n efficiently.
```python
def power(x: float, n: int) -> float:
    if n == 0:
        return 1.0
    if n < 0:
        x = 1 / x
        n = -n
    result = 1.0
    while n > 0:
        if n % 2 == 1:
            result *= x
        x *= x
        n //= 2
    return result

# power(2, 10) → 1024
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q45. GCD (Greatest Common Divisor)
**Problem:** Find GCD of two numbers.
```python
def gcd(a: int, b: int) -> int:
    while b:
        a, b = b, a % b
    return a

# gcd(48, 18) → 6
```
**Time:** O(log(min(a, b))) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q46. LCM (Least Common Multiple)
**Problem:** Find LCM of two numbers.
```python
def lcm(a: int, b: int) -> int:
    def gcd(x, y):
        while y:
            x, y = y, x % y
        return x
    return (a * b) // gcd(a, b)

# lcm(12, 18) → 36
```
**Time:** O(log(min(a, b))) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q47. Count digits in number
**Problem:** Count total digits in a number.
```python
def count_digits(n: int) -> int:
    if n == 0:
        return 1
    count = 0
    n = abs(n)
    while n > 0:
        count += 1
        n //= 10
    return count

# count_digits(12345) → 5
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** Low | **Tier:** 3

---

### Q48. Sum of digits
**Problem:** Find sum of all digits in a number.
```python
def sum_of_digits(n: int) -> int:
    n = abs(n)
    total = 0
    while n > 0:
        total += n % 10
        n //= 10
    return total

# sum_of_digits(12345) → 15
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q49. Reverse a number
**Problem:** Reverse digits of a number.
```python
def reverse_number(n: int) -> int:
    sign = -1 if n < 0 else 1
    n = abs(n)
    result = 0
    while n > 0:
        result = result * 10 + n % 10
        n //= 10
    return sign * result

# reverse_number(12345) → 54321
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q50. Check if number is palindrome
**Problem:** Check if number is palindrome.
```python
def is_palindrome_number(n: int) -> bool:
    if n < 0:
        return False
    original = n
    reversed_num = 0
    while n > 0:
        reversed_num = reversed_num * 10 + n % 10
        n //= 10
    return original == reversed_num

# is_palindrome_number(121) → True
```
**Time:** O(log n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

## SUMMARY TABLE

| # | Problem | Topic | Time | Space | Tier |
|---|---------|-------|------|-------|------|
| 1 | Reverse array | Array | O(n) | O(1) | 1 |
| 2 | Second largest | Array | O(n) | O(1) | 1 |
| 3 | Rotate array | Array | O(n) | O(1) | 1 |
| 4 | Find duplicates | Array | O(n) | O(n) | 1 |
| 5 | Move zeros | Array | O(n) | O(1) | 1 |
| 6 | Missing number | Array | O(n) | O(1) | 1 |
| 7 | Max product | Array | O(n log n) | O(1) | 2 |
| 8 | Merge sorted | Array | O(n+m) | O(n+m) | 1 |
| 9 | Peak element | Array | O(n) | O(1) | 2 |
| 10 | Count pairs | Array | O(n) | O(n) | 1 |
| 11 | Majority element | Array | O(n) | O(1) | 1 |
| 12 | Remove duplicates | Array | O(n) | O(1) | 1 |
| 13 | Intersection | Array | O(n+m) | O(n) | 1 |
| 14 | Max subarray sum | Array | O(n) | O(1) | 1 |
| 15 | Even/odd separation | Array | O(n) | O(1) | 2 |
| 16 | Reverse string | String | O(n) | O(n) | 1 |
| 17 | Palindrome check | String | O(n) | O(n) | 1 |
| 18 | Anagrams | String | O(n) | O(1) | 1 |
| 19 | First non-repeating | String | O(n) | O(1) | 1 |
| 20 | Vowels/consonants | String | O(n) | O(1) | 2 |
| 21 | Remove duplicates | String | O(n) | O(n) | 1 |
| 22 | Reverse words | String | O(n) | O(n) | 1 |
| 23 | String rotation | String | O(n) | O(n) | 2 |
| 24 | Longest substring | String | O(n) | O(n) | 1 |
| 25 | Char frequency | String | O(n) | O(1) | 1 |
| 26 | Unique chars | String | O(n) | O(1) | 1 |
| 27 | Capitalize words | String | O(n) | O(n) | 2 |
| 28 | Count substring | String | O(n*m) | O(1) | 2 |
| 29 | Remove spaces | String | O(n) | O(n) | 3 |
| 30 | Valid number | String | O(n) | O(1) | 2 |
| 31 | Two Sum | Hashing | O(n) | O(n) | 1 |
| 32 | Three Sum | Hashing | O(n²) | O(1) | 1 |
| 33 | Container water | Two Ptr | O(n) | O(1) | 1 |
| 34 | Valid parentheses | Stack | O(n) | O(n) | 1 |
| 35 | Trapping water | Two Ptr | O(n) | O(n) | 2 |
| 36 | Merge in-place | Two Ptr | O(n+m) | O(1) | 1 |
| 37 | Sorted squares | Two Ptr | O(n) | O(n) | 2 |
| 38 | Valid palindrome | Two Ptr | O(n) | O(n) | 1 |
| 39 | Remove nth node | LinkedList | O(n) | O(1) | 2 |
| 40 | Sliding window max | Deque | O(n) | O(k) | 2 |
| 41 | Fibonacci | Math | O(n) | O(1) | 1 |
| 42 | Is prime | Math | O(√n) | O(1) | 1 |
| 43 | Factorial | Math | O(n) | O(1) | 2 |
| 44 | Power | Math | O(log n) | O(1) | 1 |
| 45 | GCD | Math | O(log n) | O(1) | 2 |
| 46 | LCM | Math | O(log n) | O(1) | 2 |
| 47 | Count digits | Math | O(log n) | O(1) | 3 |
| 48 | Sum of digits | Math | O(log n) | O(1) | 2 |
| 49 | Reverse number | Math | O(log n) | O(1) | 2 |
| 50 | Palindrome number | Math | O(log n) | O(1) | 2 |

---

## NEXT STEPS

After mastering Q1–50:
- Practice each problem 2–3 times
- Solve without looking at solutions
- Optimize for both time and space
- Move to **Intermediate (Q51–120)** when confident
