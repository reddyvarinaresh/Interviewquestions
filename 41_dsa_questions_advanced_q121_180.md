# DSA Questions — Advanced (Q121–Q180)
> **Phase 3: Advanced Patterns** | Trees, Graphs, Dynamic Programming, Greedy
> **Difficulty:** Medium to Hard | **Time per question:** 35–50 minutes
> **Companies:** Product + Captive | **Frequency:** Medium-High (Tier 1–2)

---

## SECTION 1: Trees (Q121–Q145)

### Q121. Binary tree inorder traversal
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

def inorder_traversal(root: TreeNode) -> list:
    result = []
    def dfs(node):
        if not node:
            return
        dfs(node.left)
        result.append(node.val)
        dfs(node.right)
    dfs(root)
    return result
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Very High | **Tier:** 1

---

### Q122. Binary tree preorder traversal
```python
def preorder_traversal(root: TreeNode) -> list:
    result = []
    def dfs(node):
        if not node:
            return
        result.append(node.val)
        dfs(node.left)
        dfs(node.right)
    dfs(root)
    return result
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Very High | **Tier:** 1

---

### Q123. Binary tree postorder traversal
```python
def postorder_traversal(root: TreeNode) -> list:
    result = []
    def dfs(node):
        if not node:
            return
        dfs(node.left)
        dfs(node.right)
        result.append(node.val)
    dfs(root)
    return result
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Very High | **Tier:** 1

---

### Q124. Level order traversal (BFS)
```python
from collections import deque

def level_order(root: TreeNode) -> list:
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        result.append(level)
    return result
```
**Time:** O(n) | **Space:** O(w) | **Frequency:** Very High | **Tier:** 1

---

### Q125. Maximum depth of binary tree
```python
def max_depth(root: TreeNode) -> int:
    if not root:
        return 0
    return 1 + max(max_depth(root.left), max_depth(root.right))
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Very High | **Tier:** 1

---

### Q126. Balanced binary tree
```python
def is_balanced(root: TreeNode) -> bool:
    def check(node):
        if not node:
            return 0, True
        left_height, left_balanced = check(node.left)
        if not left_balanced:
            return 0, False
        right_height, right_balanced = check(node.right)
        if not right_balanced:
            return 0, False
        if abs(left_height - right_height) > 1:
            return 0, False
        return 1 + max(left_height, right_height), True
    return check(root)[1]
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q127. Lowest common ancestor (LCA)
```python
def lowest_common_ancestor(root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode:
    if not root or root == p or root == q:
        return root
    left = lowest_common_ancestor(root.left, p, q)
    right = lowest_common_ancestor(root.right, p, q)
    if left and right:
        return root
    return left if left else right
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q128. Path sum
```python
def has_path_sum(root: TreeNode, target: int) -> bool:
    if not root:
        return False
    if not root.left and not root.right:
        return root.val == target
    return has_path_sum(root.left, target - root.val) or has_path_sum(root.right, target - root.val)
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q129. Path sum II
```python
def path_sum_ii(root: TreeNode, target: int) -> list:
    result = []
    def dfs(node, remaining, path):
        if not node:
            return
        if not node.left and not node.right and node.val == remaining:
            result.append(path + [node.val])
            return
        dfs(node.left, remaining - node.val, path + [node.val])
        dfs(node.right, remaining - node.val, path + [node.val])
    dfs(root, target, [])
    return result
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Medium | **Tier:** 2

---

### Q130. Binary search tree validation
```python
def is_valid_bst(root: TreeNode) -> bool:
    def validate(node, low, high):
        if not node:
            return True
        if node.val <= low or node.val >= high:
            return False
        return validate(node.left, low, node.val) and validate(node.right, node.val, high)
    return validate(root, float('-inf'), float('inf'))
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Very High | **Tier:** 1

---

### Q131. Kth smallest in BST
```python
def kth_smallest(root: TreeNode, k: int) -> int:
    count = [0]
    result = [None]
    def inorder(node):
        if not node:
            return
        inorder(node.left)
        count[0] += 1
        if count[0] == k:
            result[0] = node.val
            return
        inorder(node.right)
    inorder(root)
    return result[0]
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q132. Serialize and deserialize binary tree
```python
def serialize(root: TreeNode) -> str:
    result = []
    def dfs(node):
        if not node:
            result.append('None')
            return
        result.append(str(node.val))
        dfs(node.left)
        dfs(node.right)
    dfs(root)
    return ','.join(result)

def deserialize(data: str) -> TreeNode:
    nodes = data.split(',')
    idx = [0]
    def dfs():
        if nodes[idx[0]] == 'None':
            idx[0] += 1
            return None
        node = TreeNode(int(nodes[idx[0]]))
        idx[0] += 1
        node.left = dfs()
        node.right = dfs()
        return node
    return dfs()
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Medium | **Tier:** 2

---

### Q133. Construct binary tree from preorder and inorder
```python
def build_tree(preorder: list, inorder: list) -> TreeNode:
    if not preorder or not inorder:
        return None
    root = TreeNode(preorder[0])
    idx = inorder.index(preorder[0])
    root.left = build_tree(preorder[1:idx+1], inorder[:idx])
    root.right = build_tree(preorder[idx+1:], inorder[idx+1:])
    return root
```
**Time:** O(n²) | **Space:** O(h) | **Frequency:** Medium | **Tier:** 2

---

### Q134. Diameter of binary tree
```python
def diameter_of_binary_tree(root: TreeNode) -> int:
    max_diameter = [0]
    def dfs(node):
        if not node:
            return 0
        left = dfs(node.left)
        right = dfs(node.right)
        max_diameter[0] = max(max_diameter[0], left + right)
        return 1 + max(left, right)
    dfs(root)
    return max_diameter[0]
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q135. Flatten binary tree to linked list
```python
def flatten(root: TreeNode) -> None:
    def dfs(node):
        if not node:
            return None
        if not node.left and not node.right:
            return node
        left_tail = dfs(node.left)
        right_tail = dfs(node.right)
        if node.left:
            left_tail.right = node.right
            node.right = node.left
            node.left = None
        return right_tail if right_tail else left_tail
    dfs(root)
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** Medium | **Tier:** 2

---

### Q136. Vertical order traversal
```python
def vertical_order(root: TreeNode) -> list:
    from collections import defaultdict, deque
    if not root:
        return []
    columns = defaultdict(list)
    queue = deque([(root, 0)])
    while queue:
        node, col = queue.popleft()
        columns[col].append(node.val)
        if node.left:
            queue.append((node.left, col - 1))
        if node.right:
            queue.append((node.right, col + 1))
    return [columns[col] for col in sorted(columns.keys())]
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

### Q137. All paths from root to leaf
```python
def all_paths(root: TreeNode) -> list:
    result = []
    def dfs(node, path):
        if not node:
            return
        path.append(node.val)
        if not node.left and not node.right:
            result.append(path[:])
        else:
            dfs(node.left, path)
            dfs(node.right, path)
        path.pop()
    dfs(root, [])
    return result
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q138. Symmetric tree
```python
def is_symmetric(root: TreeNode) -> bool:
    def is_mirror(left, right):
        if not left and not right:
            return True
        if not left or not right:
            return False
        return left.val == right.val and is_mirror(left.left, right.right) and is_mirror(left.right, right.left)
    return is_mirror(root, root)
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q139. Same tree
```python
def is_same_tree(p: TreeNode, q: TreeNode) -> bool:
    if not p and not q:
        return True
    if not p or not q:
        return False
    return p.val == q.val and is_same_tree(p.left, q.left) and is_same_tree(p.right, q.right)
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q140. Subtree of another tree
```python
def is_subtree(root: TreeNode, subroot: TreeNode) -> bool:
    if not root:
        return False
    if is_same_tree(root, subroot):
        return True
    return is_subtree(root.left, subroot) or is_subtree(root.right, subroot)

def is_same_tree(p: TreeNode, q: TreeNode) -> bool:
    if not p and not q:
        return True
    if not p or not q:
        return False
    return p.val == q.val and is_same_tree(p.left, q.left) and is_same_tree(p.right, q.right)
```
**Time:** O(n * m) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q141. Invert binary tree
```python
def invert_tree(root: TreeNode) -> TreeNode:
    if not root:
        return None
    root.left, root.right = root.right, root.left
    invert_tree(root.left)
    invert_tree(root.right)
    return root
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q142. Right side view of binary tree
```python
def right_side_view(root: TreeNode) -> list:
    from collections import deque
    if not root:
        return []
    result = []
    queue = deque([root])
    while queue:
        level_size = len(queue)
        for i in range(level_size):
            node = queue.popleft()
            if i == level_size - 1:
                result.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
    return result
```
**Time:** O(n) | **Space:** O(w) | **Frequency:** High | **Tier:** 1

---

### Q143. Zigzag level order traversal
```python
def zigzag_level_order(root: TreeNode) -> list:
    from collections import deque
    if not root:
        return []
    result = []
    queue = deque([root])
    left_to_right = True
    while queue:
        level_size = len(queue)
        level = []
        for _ in range(level_size):
            node = queue.popleft()
            level.append(node.val)
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        if not left_to_right:
            level.reverse()
        result.append(level)
        left_to_right = not left_to_right
    return result
```
**Time:** O(n) | **Space:** O(w) | **Frequency:** Medium | **Tier:** 2

---

### Q144. Maximum path sum
```python
def max_path_sum(root: TreeNode) -> int:
    max_sum = [float('-inf')]
    def dfs(node):
        if not node:
            return 0
        left = max(0, dfs(node.left))
        right = max(0, dfs(node.right))
        max_sum[0] = max(max_sum[0], left + right + node.val)
        return node.val + max(left, right)
    dfs(root)
    return max_sum[0]
```
**Time:** O(n) | **Space:** O(h) | **Frequency:** High | **Tier:** 1

---

### Q145. Populating next right pointers
```python
class Node:
    def __init__(self, val: int = 0, left=None, right=None, next=None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next

def connect(root: Node) -> Node:
    if not root:
        return None
    queue = [root]
    while queue:
        next_queue = []
        for i in range(len(queue)):
            if i < len(queue) - 1:
                queue[i].next = queue[i + 1]
            if queue[i].left:
                next_queue.append(queue[i].left)
            if queue[i].right:
                next_queue.append(queue[i].right)
        queue = next_queue
    return root
```
**Time:** O(n) | **Space:** O(w) | **Frequency:** Medium | **Tier:** 2

---

## SECTION 2: Graphs (Q146–Q160)

### Q146. Graph DFS
```python
def dfs(graph: dict, start: str, visited: set = None) -> list:
    if visited is None:
        visited = set()
    visited.add(start)
    result = [start]
    for neighbor in graph.get(start, []):
        if neighbor not in visited:
            result.extend(dfs(graph, neighbor, visited))
    return result
```
**Time:** O(V + E) | **Space:** O(V) | **Frequency:** Very High | **Tier:** 1

---

### Q147. Graph BFS
```python
from collections import deque

def bfs(graph: dict, start: str) -> list:
    visited = set([start])
    queue = deque([start])
    result = []
    while queue:
        node = queue.popleft()
        result.append(node)
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
    return result
```
**Time:** O(V + E) | **Space:** O(V) | **Frequency:** Very High | **Tier:** 1

---

### Q148. Number of islands
```python
def num_islands(grid: list) -> int:
    if not grid:
        return 0
    count = 0
    def dfs(i, j):
        if i < 0 or i >= len(grid) or j < 0 or j >= len(grid[0]) or grid[i][j] == '0':
            return
        grid[i][j] = '0'
        dfs(i + 1, j)
        dfs(i - 1, j)
        dfs(i, j + 1)
        dfs(i, j - 1)
    for i in range(len(grid)):
        for j in range(len(grid[0])):
            if grid[i][j] == '1':
                dfs(i, j)
                count += 1
    return count
```
**Time:** O(m * n) | **Space:** O(m * n) | **Frequency:** Very High | **Tier:** 1

---

### Q149. Surrounded regions
```python
def solve(board: list) -> None:
    if not board:
        return
    def dfs(i, j):
        if i < 0 or i >= len(board) or j < 0 or j >= len(board[0]) or board[i][j] != 'O':
            return
        board[i][j] = 'V'
        dfs(i + 1, j)
        dfs(i - 1, j)
        dfs(i, j + 1)
        dfs(i, j - 1)
    for i in range(len(board)):
        for j in range(len(board[0])):
            if (i == 0 or i == len(board) - 1 or j == 0 or j == len(board[0]) - 1) and board[i][j] == 'O':
                dfs(i, j)
    for i in range(len(board)):
        for j in range(len(board[0])):
            if board[i][j] == 'O':
                board[i][j] = 'X'
            elif board[i][j] == 'V':
                board[i][j] = 'O'
```
**Time:** O(m * n) | **Space:** O(m * n) | **Frequency:** High | **Tier:** 1

---

### Q150. Topological sort
```python
def topological_sort(graph: dict, nodes: list) -> list:
    in_degree = {node: 0 for node in nodes}
    for node in graph:
        for neighbor in graph.get(node, []):
            in_degree[neighbor] += 1
    queue = [node for node in nodes if in_degree[node] == 0]
    result = []
    while queue:
        node = queue.pop(0)
        result.append(node)
        for neighbor in graph.get(node, []):
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    return result if len(result) == len(nodes) else []
```
**Time:** O(V + E) | **Space:** O(V) | **Frequency:** High | **Tier:** 1

---

### Q151. Course schedule
```python
def can_finish(num_courses: int, prerequisites: list) -> bool:
    graph = {i: [] for i in range(num_courses)}
    in_degree = [0] * num_courses
    for course, prereq in prerequisites:
        graph[prereq].append(course)
        in_degree[course] += 1
    queue = [i for i in range(num_courses) if in_degree[i] == 0]
    count = 0
    while queue:
        course = queue.pop(0)
        count += 1
        for next_course in graph[course]:
            in_degree[next_course] -= 1
            if in_degree[next_course] == 0:
                queue.append(next_course)
    return count == num_courses
```
**Time:** O(V + E) | **Space:** O(V + E) | **Frequency:** High | **Tier:** 1

---

### Q152. Clone graph
```python
class GraphNode:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []

def clone_graph(node: GraphNode) -> GraphNode:
    if not node:
        return None
    visited = {}
    def dfs(node):
        if node in visited:
            return visited[node]
        copy = GraphNode(node.val)
        visited[node] = copy
        for neighbor in node.neighbors:
            copy.neighbors.append(dfs(neighbor))
        return copy
    return dfs(node)
```
**Time:** O(V + E) | **Space:** O(V) | **Frequency:** Medium | **Tier:** 2

---

### Q153. Shortest path in unweighted graph
```python
from collections import deque

def shortest_path(graph: dict, start: str, end: str) -> int:
    visited = set([start])
    queue = deque([(start, 0)])
    while queue:
        node, dist = queue.popleft()
        if node == end:
            return dist
        for neighbor in graph.get(node, []):
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
    return -1
```
**Time:** O(V + E) | **Space:** O(V) | **Frequency:** High | **Tier:** 1

---

### Q154. Dijkstra's algorithm
```python
import heapq

def dijkstra(graph: dict, start: str) -> dict:
    distances = {node: float('inf') for node in graph}
    distances[start] = 0
    pq = [(0, start)]
    while pq:
        current_dist, current_node = heapq.heappop(pq)
        if current_dist > distances[current_node]:
            continue
        for neighbor, weight in graph.get(current_node, []):
            distance = current_dist + weight
            if distance < distances[neighbor]:
                distances[neighbor] = distance
                heapq.heappush(pq, (distance, neighbor))
    return distances
```
**Time:** O((V + E) log V) | **Space:** O(V) | **Frequency:** Medium | **Tier:** 2

---

### Q155. Detect cycle in undirected graph
```python
def has_cycle(graph: dict, num_nodes: int) -> bool:
    visited = [False] * num_nodes
    def dfs(node, parent):
        visited[node] = True
        for neighbor in graph.get(node, []):
            if not visited[neighbor]:
                if dfs(neighbor, node):
                    return True
            elif neighbor != parent:
                return True
        return False
    for i in range(num_nodes):
        if not visited[i]:
            if dfs(i, -1):
                return True
    return False
```
**Time:** O(V + E) | **Space:** O(V) | **Frequency:** High | **Tier:** 1

---

### Q156. Detect cycle in directed graph
```python
def has_cycle_directed(graph: dict, num_nodes: int) -> bool:
    WHITE, GRAY, BLACK = 0, 1, 2
    color = [WHITE] * num_nodes
    def dfs(node):
        color[node] = GRAY
        for neighbor in graph.get(node, []):
            if color[neighbor] == GRAY:
                return True
            if color[neighbor] == WHITE and dfs(neighbor):
                return True
        color[node] = BLACK
        return False
    for i in range(num_nodes):
        if color[i] == WHITE:
            if dfs(i):
                return True
    return False
```
**Time:** O(V + E) | **Space:** O(V) | **Frequency:** High | **Tier:** 1

---

### Q157. Alien dictionary
```python
def alien_order(words: list) -> str:
    graph = {char: [] for word in words for char in word}
    in_degree = {char: 0 for word in words for char in word}
    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i + 1]
        for j in range(min(len(w1), len(w2))):
            if w1[j] != w2[j]:
                graph[w1[j]].append(w2[j])
                in_degree[w2[j]] += 1
                break
    queue = [char for char in graph if in_degree[char] == 0]
    result = []
    while queue:
        char = queue.pop(0)
        result.append(char)
        for neighbor in graph[char]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    return "".join(result) if len(result) == len(graph) else ""
```
**Time:** O(N * L + U + E) | **Space:** O(U + E) | **Frequency:** Medium | **Tier:** 2

---

### Q158. Word ladder
```python
from collections import deque

def ladder_length(begin: str, end: str, word_list: list) -> int:
    word_set = set(word_list)
    if end not in word_set:
        return 0
    queue = deque([(begin, 1)])
    visited = set([begin])
    while queue:
        word, length = queue.popleft()
        if word == end:
            return length
        for i in range(len(word)):
            for c in 'abcdefghijklmnopqrstuvwxyz':
                new_word = word[:i] + c + word[i+1:]
                if new_word in word_set and new_word not in visited:
                    visited.add(new_word)
                    queue.append((new_word, length + 1))
    return 0
```
**Time:** O(N * L * 26) | **Space:** O(N * L) | **Frequency:** Medium | **Tier:** 2

---

### Q159. Minimum spanning tree (Kruskal's)
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        if self.rank[px] < self.rank[py]:
            px, py = py, px
        self.parent[py] = px
        if self.rank[px] == self.rank[py]:
            self.rank[px] += 1
        return True

def kruskal(n: int, edges: list) -> int:
    edges.sort(key=lambda x: x[2])
    uf = UnionFind(n)
    mst_weight = 0
    for u, v, w in edges:
        if uf.union(u, v):
            mst_weight += w
    return mst_weight
```
**Time:** O(E log E) | **Space:** O(V) | **Frequency:** Medium | **Tier:** 2

---

### Q160. Bipartite graph
```python
def is_bipartite(graph: list) -> bool:
    color = [-1] * len(graph)
    def bfs(start):
        from collections import deque
        queue = deque([start])
        color[start] = 0
        while queue:
            node = queue.popleft()
            for neighbor in graph[node]:
                if color[neighbor] == -1:
                    color[neighbor] = 1 - color[node]
                    queue.append(neighbor)
                elif color[neighbor] == color[node]:
                    return False
        return True
    for i in range(len(graph)):
        if color[i] == -1:
            if not bfs(i):
                return False
    return True
```
**Time:** O(V + E) | **Space:** O(V) | **Frequency:** Medium | **Tier:** 2

---

## SECTION 3: Dynamic Programming (Q161–Q175)

### Q161. Climbing stairs
```python
def climb_stairs(n: int) -> int:
    if n <= 2:
        return n
    a, b = 1, 2
    for _ in range(3, n + 1):
        a, b = b, a + b
    return b
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Very High | **Tier:** 1

---

### Q162. House robber
```python
def rob(nums: list) -> int:
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    prev2, prev1 = 0, 0
    for num in nums:
        current = max(prev1, prev2 + num)
        prev2, prev1 = prev1, current
    return prev1
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Very High | **Tier:** 1

---

### Q163. House robber II
```python
def rob_ii(nums: list) -> int:
    if not nums:
        return 0
    if len(nums) == 1:
        return nums[0]
    def rob_linear(arr):
        prev2, prev1 = 0, 0
        for num in arr:
            current = max(prev1, prev2 + num)
            prev2, prev1 = prev1, current
        return prev1
    return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q164. Coin change
```python
def coin_change(coins: list, amount: int) -> int:
    dp = [float('inf')] * (amount + 1)
    dp[0] = 0
    for coin in coins:
        for i in range(coin, amount + 1):
            dp[i] = min(dp[i], dp[i - coin] + 1)
    return dp[amount] if dp[amount] != float('inf') else -1
```
**Time:** O(n * amount) | **Space:** O(amount) | **Frequency:** Very High | **Tier:** 1

---

### Q165. Coin change II
```python
def change(amount: int, coins: list) -> int:
    dp = [0] * (amount + 1)
    dp[0] = 1
    for coin in coins:
        for i in range(coin, amount + 1):
            dp[i] += dp[i - coin]
    return dp[amount]
```
**Time:** O(n * amount) | **Space:** O(amount) | **Frequency:** High | **Tier:** 1

---

### Q166. 0/1 Knapsack
```python
def knapsack(weights: list, values: list, capacity: int) -> int:
    n = len(weights)
    dp = [[0] * (capacity + 1) for _ in range(n + 1)]
    for i in range(1, n + 1):
        for w in range(capacity + 1):
            if weights[i - 1] <= w:
                dp[i][w] = max(values[i - 1] + dp[i - 1][w - weights[i - 1]], dp[i - 1][w])
            else:
                dp[i][w] = dp[i - 1][w]
    return dp[n][capacity]
```
**Time:** O(n * capacity) | **Space:** O(n * capacity) | **Frequency:** High | **Tier:** 1

---

### Q167. Longest common subsequence
```python
def longest_common_subsequence(text1: str, text2: str) -> int:
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i - 1] == text2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1] + 1
            else:
                dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])
    return dp[m][n]
```
**Time:** O(m * n) | **Space:** O(m * n) | **Frequency:** High | **Tier:** 1

---

### Q168. Longest increasing subsequence
```python
def longest_increasing_subsequence(nums: list) -> int:
    if not nums:
        return 0
    dp = [1] * len(nums)
    for i in range(1, len(nums)):
        for j in range(i):
            if nums[j] < nums[i]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)
```
**Time:** O(n²) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q169. Edit distance
```python
def edit_distance(word1: str, word2: str) -> int:
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i - 1] == word2[j - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            else:
                dp[i][j] = 1 + min(dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1])
    return dp[m][n]
```
**Time:** O(m * n) | **Space:** O(m * n) | **Frequency:** High | **Tier:** 1

---

### Q170. Word break
```python
def word_break(s: str, word_dict: list) -> bool:
    word_set = set(word_dict)
    dp = [False] * (len(s) + 1)
    dp[0] = True
    for i in range(1, len(s) + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break
    return dp[len(s)]
```
**Time:** O(n²) | **Space:** O(n) | **Frequency:** High | **Tier:** 1

---

### Q171. Unique paths
```python
def unique_paths(m: int, n: int) -> int:
    dp = [[1] * n for _ in range(m)]
    for i in range(1, m):
        for j in range(1, n):
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1]
    return dp[m - 1][n - 1]
```
**Time:** O(m * n) | **Space:** O(m * n) | **Frequency:** High | **Tier:** 1

---

### Q172. Unique paths II (with obstacles)
```python
def unique_paths_ii(grid: list) -> int:
    m, n = len(grid), len(grid[0])
    dp = [[0] * n for _ in range(m)]
    dp[0][0] = 1 if grid[0][0] == 0 else 0
    for i in range(m):
        for j in range(n):
            if grid[i][j] == 1:
                dp[i][j] = 0
            elif i == 0 and j == 0:
                continue
            else:
                dp[i][j] = (dp[i - 1][j] if i > 0 else 0) + (dp[i][j - 1] if j > 0 else 0)
    return dp[m - 1][n - 1]
```
**Time:** O(m * n) | **Space:** O(m * n) | **Frequency:** High | **Tier:** 1

---

### Q173. Maximum subarray (Kadane's)
```python
def max_subarray(nums: list) -> int:
    max_sum = current_sum = nums[0]
    for num in nums[1:]:
        current_sum = max(num, current_sum + num)
        max_sum = max(max_sum, current_sum)
    return max_sum
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Very High | **Tier:** 1

---

### Q174. Minimum path sum
```python
def min_path_sum(grid: list) -> int:
    m, n = len(grid), len(grid[0])
    dp = [[0] * n for _ in range(m)]
    dp[0][0] = grid[0][0]
    for i in range(m):
        for j in range(n):
            if i == 0 and j == 0:
                continue
            elif i == 0:
                dp[i][j] = dp[i][j - 1] + grid[i][j]
            elif j == 0:
                dp[i][j] = dp[i - 1][j] + grid[i][j]
            else:
                dp[i][j] = min(dp[i - 1][j], dp[i][j - 1]) + grid[i][j]
    return dp[m - 1][n - 1]
```
**Time:** O(m * n) | **Space:** O(m * n) | **Frequency:** High | **Tier:** 1

---

### Q175. Palindromic substrings
```python
def count_substrings(s: str) -> int:
    count = 0
    def expand_around_center(left, right):
        nonlocal count
        while left >= 0 and right < len(s) and s[left] == s[right]:
            count += 1
            left -= 1
            right += 1
    for i in range(len(s)):
        expand_around_center(i, i)
        expand_around_center(i, i + 1)
    return count
```
**Time:** O(n²) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

## SECTION 4: Greedy (Q176–Q180)

### Q176. Activity selection
```python
def activity_selection(activities: list) -> int:
    activities.sort(key=lambda x: x[1])
    count = 1
    last_end = activities[0][1]
    for i in range(1, len(activities)):
        if activities[i][0] >= last_end:
            count += 1
            last_end = activities[i][1]
    return count
```
**Time:** O(n log n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q177. Jump game
```python
def can_jump(nums: list) -> bool:
    max_reach = 0
    for i in range(len(nums)):
        if i > max_reach:
            return False
        max_reach = max(max_reach, i + nums[i])
    return True
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q178. Jump game II
```python
def jump(nums: list) -> int:
    jumps = 0
    current_end = 0
    farthest = 0
    for i in range(len(nums) - 1):
        farthest = max(farthest, i + nums[i])
        if i == current_end:
            jumps += 1
            current_end = farthest
    return jumps
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** High | **Tier:** 1

---

### Q179. Gas station
```python
def can_complete_circuit(gas: list, cost: list) -> int:
    total_gas = 0
    current_gas = 0
    start = 0
    for i in range(len(gas)):
        total_gas += gas[i] - cost[i]
        current_gas += gas[i] - cost[i]
        if current_gas < 0:
            start = i + 1
            current_gas = 0
    return start if total_gas >= 0 else -1
```
**Time:** O(n) | **Space:** O(1) | **Frequency:** Medium | **Tier:** 2

---

### Q180. Candy distribution
```python
def distribute_candies(ratings: list) -> int:
    n = len(ratings)
    candies = [1] * n
    for i in range(1, n):
        if ratings[i] > ratings[i - 1]:
            candies[i] = candies[i - 1] + 1
    for i in range(n - 2, -1, -1):
        if ratings[i] > ratings[i + 1]:
            candies[i] = max(candies[i], candies[i + 1] + 1)
    return sum(candies)
```
**Time:** O(n) | **Space:** O(n) | **Frequency:** Medium | **Tier:** 2

---

## SUMMARY: Advanced Questions (Q121–Q180)

**Total:** 60 advanced questions covering Trees, Graphs, DP, and Greedy
**Estimated study time:** 20–25 hours
**After completing all 180 questions, you'll be ready for most DSA interviews!**

---

## NEXT STEPS

1. Master Q1–50 (Foundations)
2. Master Q51–120 (Intermediate)
3. Master Q121–180 (Advanced)
4. Review all 180 questions weekly
5. Practice on LeetCode/GeeksforGeeks
6. Mock interviews with friends
