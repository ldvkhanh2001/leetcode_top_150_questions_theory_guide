# LeetCode Top 150 Interview Questions - Complete Algorithm Theory Guide

## Table of Contents
1. [Arrays & Strings](#1-arrays--strings)
2. [Two Pointers](#2-two-pointers)
3. [Sliding Window](#3-sliding-window)
4. [Matrix](#4-matrix)
5. [Hash Map / Hash Set](#5-hash-map--hash-set)
6. [Intervals](#6-intervals)
7. [Stack](#7-stack)
8. [Linked List](#8-linked-list)
9. [Binary Tree (General / DFS)](#9-binary-tree-general--dfs)
10. [Binary Tree BFS](#10-binary-tree-bfs)
11. [Binary Search Tree](#11-binary-search-tree)
12. [Graph (General / DFS & Union-Find)](#12-graph-general--dfs--union-find)
13. [Graph BFS](#13-graph-bfs)
14. [Trie](#14-trie)
15. [Backtracking](#15-backtracking)
16. [Divide & Conquer](#16-divide--conquer)
17. [Kadane's Algorithm](#17-kadanes-algorithm)
18. [Binary Search](#18-binary-search)
19. [Heap / Priority Queue](#19-heap--priority-queue)
20. [Bit Manipulation](#20-bit-manipulation)
21. [Math](#21-math)
22. [1D Dynamic Programming](#22-1d-dynamic-programming)
23. [Multidimensional DP](#23-multidimensional-dp)

---

## 1. Arrays & Strings

### Core Theory

**Arrays** are contiguous memory blocks storing elements of the same type. Key operations include access (O(1)), insertion/deletion (O(n) for arbitrary positions), and searching (O(n) unsorted, O(log n) sorted).

**In-place operations** modify the array without using extra space. Common patterns:
- **Two pointers**: One for reading, one for writing
- **Swapping**: Exchange elements to avoid extra space
- **Overwriting**: Use processed positions to store results

**Key Algorithms:**

1. **Boyer-Moore Majority Vote Algorithm**:
   ```python
   def majorityElement(nums):
       candidate = count = 0
       for num in nums:
           if count == 0:
               candidate = num
           count += 1 if num == candidate else -1
       return candidate
   ```
   Time: O(n), Space: O(1)

2. **Array Rotation (Reverse Method)**:
   ```python
   def rotate(nums, k):
       k %= len(nums)
       reverse(nums, 0, len(nums) - 1)  # Reverse entire array
       reverse(nums, 0, k - 1)          # Reverse first k
       reverse(nums, k, len(nums) - 1)  # Reverse remaining
   ```

3. **Prefix/Suffix Products**:
   ```python
   def productExceptSelf(nums):
       result = [1] * len(nums)
       # Build prefix products
       for i in range(1, len(nums)):
           result[i] = result[i-1] * nums[i-1]
       # Multiply with suffix products
       suffix = 1
       for i in range(len(nums) - 1, -1, -1):
           result[i] *= suffix
           suffix *= nums[i]
       return result
   ```

**String Algorithms:**
- **KMP Algorithm** for pattern matching (strStr)
- **Two pointers** for palindrome checking
- **Tokenization** and parsing for text processing

### Time Complexity Patterns
- Array access: O(1)
- Linear scan: O(n)
- In-place modification: O(1) space, O(n) time
- Sorting: O(n log n)

### Questions from Top 150 (1–24)
- ✅ Merge Sorted Array
- ✅ Remove Element  
- ✅ Remove Duplicates from Sorted Array (I, II)
- ✅ Majority Element
- ✅ Rotate Array
- ✅ Best Time to Buy and Sell Stock (I, II)
- ✅ Jump Game (I, II)
- ✅ H-Index
- ✅ Insert Delete GetRandom O(1)
- ✅ Product of Array Except Self
- ✅ Gas Station
- ✅ Candy
- ✅ Trapping Rain Water
- ✅ Roman to Integer / Integer to Roman
- ✅ Length of Last Word
- ✅ Longest Common Prefix
- ✅ Reverse Words in a String
- ✅ Zigzag Conversion
- ✅ Find the Index of the First Occurrence in a String
- ✅ Text Justification

---

## 2. Two Pointers

### Core Theory

Two pointers is an optimization technique that uses two indices to traverse data structures, reducing time complexity from O(n²) to O(n) for many problems.

**Types of Two Pointers:**

1. **Opposite Direction (Convergent)**:
   ```python
   left, right = 0, len(arr) - 1
   while left < right:
       if condition_met:
           # Process and move both pointers
           left += 1
           right -= 1
       elif need_smaller_sum:
           left += 1
       else:
           right -= 1
   ```

2. **Same Direction (Fast & Slow)**:
   ```python
   slow = fast = 0
   while fast < len(arr):
       if condition:
           arr[slow] = arr[fast]
           slow += 1
       fast += 1
   ```

**Key Applications:**
- **Two Sum on Sorted Array**: Use opposite pointers with sum comparison
- **Three Sum**: Fix one element, use two pointers on remaining sorted array
- **Palindrome Checking**: Compare characters from both ends
- **Container With Most Water**: Greedy approach - always move the pointer at shorter height

**Why Two Pointers Work:**
For sorted arrays and two-sum problems, if `nums[left] + nums[right] > target`, we know that `nums[left] + nums[i]` (where `i > right`) will also be too large, so we can safely move `right` pointer left.

### Template for Two Sum Pattern
```python
def twoSum(nums, target):
    nums.sort()  # If not already sorted
    left, right = 0, len(nums) - 1
    
    while left < right:
        current_sum = nums[left] + nums[right]
        if current_sum == target:
            return [left, right]
        elif current_sum < target:
            left += 1
        else:
            right -= 1
    
    return []  # Not found
```

### Questions from Top 150 (25–29)
- ✅ Valid Palindrome
- ✅ Is Subsequence
- ✅ Two Sum II - Input Array Is Sorted
- ✅ Container With Most Water
- ✅ 3Sum

---

## 3. Sliding Window

### Core Theory

Sliding Window is an optimization technique for problems involving contiguous subarrays/substrings. Instead of checking every possible subarray (O(n²) or O(n³)), we maintain a "window" that slides across the data structure.

**Types of Sliding Windows:**

1. **Fixed Size Window**:
   ```python
   def fixedWindow(arr, k):
       window_sum = sum(arr[:k])  # Initial window
       max_sum = window_sum
       
       for i in range(k, len(arr)):
           window_sum += arr[i] - arr[i - k]  # Slide window
           max_sum = max(max_sum, window_sum)
       
       return max_sum
   ```

2. **Variable Size Window (Expand/Shrink)**:
   ```python
   def variableWindow(arr, target):
       left = 0
       current_sum = 0
       min_length = float('inf')
       
       for right in range(len(arr)):
           current_sum += arr[right]  # Expand window
           
           while current_sum >= target:  # Shrink window
               min_length = min(min_length, right - left + 1)
               current_sum -= arr[left]
               left += 1
       
       return min_length if min_length != float('inf') else 0
   ```

**Key Patterns:**

1. **Character Frequency Window** (using HashMap):
   ```python
   def longestSubstring(s):
       char_count = {}
       left = 0
       max_length = 0
       
       for right in range(len(s)):
           char_count[s[right]] = char_count.get(s[right], 0) + 1
           
           while len(char_count) > k or has_duplicates(char_count):
               char_count[s[left]] -= 1
               if char_count[s[left]] == 0:
                   del char_count[s[left]]
               left += 1
           
           max_length = max(max_length, right - left + 1)
       
       return max_length
   ```

2. **Minimum Window Template**:
   ```python
   def minWindow(s, t):
       need = Counter(t)
       have = {}
       required = len(need)
       formed = 0
       
       left = right = 0
       min_len = float('inf')
       min_window = ""
       
       while right < len(s):
           # Expand window
           char = s[right]
           have[char] = have.get(char, 0) + 1
           if char in need and have[char] == need[char]:
               formed += 1
           
           # Shrink window
           while formed == required:
               if right - left + 1 < min_len:
                   min_len = right - left + 1
                   min_window = s[left:right+1]
               
               char = s[left]
               have[char] -= 1
               if char in need and have[char] < need[char]:
                   formed -= 1
               left += 1
           
           right += 1
       
       return min_window
   ```

**Time Complexity**: O(n) - each element is visited at most twice (once by right pointer, once by left pointer)

### Questions from Top 150 (30–33)
- ✅ Minimum Size Subarray Sum
- ✅ Longest Substring Without Repeating Characters
- ✅ Substring with Concatenation of All Words
- ✅ Minimum Window Substring

---

## 4. Matrix

### Core Theory

A **matrix** is a 2D array where elements are accessed using two indices: `matrix[row][col]`. Understanding coordinate systems and traversal patterns is crucial for matrix problems.

**Key Traversal Patterns:**

1. **Spiral Traversal**:
   ```python
   def spiralOrder(matrix):
       if not matrix: return []
       
       result = []
       top, bottom = 0, len(matrix) - 1
       left, right = 0, len(matrix[0]) - 1
       
       while top <= bottom and left <= right:
           # Traverse right
           for col in range(left, right + 1):
               result.append(matrix[top][col])
           top += 1
           
           # Traverse down
           for row in range(top, bottom + 1):
               result.append(matrix[row][right])
           right -= 1
           
           if top <= bottom:
               # Traverse left
               for col in range(right, left - 1, -1):
                   result.append(matrix[bottom][col])
               bottom -= 1
           
           if left <= right:
               # Traverse up
               for row in range(bottom, top - 1, -1):
                   result.append(matrix[row][left])
               left += 1
       
       return result
   ```

2. **Matrix Rotation (90° Clockwise)**:
   ```python
   def rotate(matrix):
       n = len(matrix)
       
       # Step 1: Transpose (swap matrix[i][j] with matrix[j][i])
       for i in range(n):
           for j in range(i, n):
               matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
       
       # Step 2: Reverse each row
       for i in range(n):
           matrix[i].reverse()
   ```

3. **Using First Row/Column as Markers** (Set Matrix Zeroes):
   ```python
   def setZeroes(matrix):
       rows, cols = len(matrix), len(matrix[0])
       first_row_zero = first_col_zero = False
       
       # Check if first row/column should be zero
       for j in range(cols):
           if matrix[0][j] == 0:
               first_row_zero = True
               break
       
       for i in range(rows):
           if matrix[i][0] == 0:
               first_col_zero = True
               break
       
       # Use first row/column as markers
       for i in range(1, rows):
           for j in range(1, cols):
               if matrix[i][j] == 0:
                   matrix[0][j] = matrix[i][0] = 0
       
       # Set zeros based on markers
       for i in range(1, rows):
           for j in range(1, cols):
               if matrix[0][j] == 0 or matrix[i][0] == 0:
                   matrix[i][j] = 0
       
       # Handle first row/column
       if first_row_zero:
           for j in range(cols):
               matrix[0][j] = 0
       if first_col_zero:
           for i in range(rows):
               matrix[i][0] = 0
   ```

**Common Tricks:**
- Use row/column indices as hash keys for constraints (Sudoku)
- Modify in-place by using processed positions as markers
- For game simulations, count neighbors using direction arrays

**Direction Arrays for 8-directional movement**:
```python
directions = [(-1,-1), (-1,0), (-1,1), (0,-1), (0,1), (1,-1), (1,0), (1,1)]
for dx, dy in directions:
    new_row, new_col = row + dx, col + dy
    if 0 <= new_row < rows and 0 <= new_col < cols:
        # Process neighbor
```

### Questions from Top 150 (34–38)
- ✅ Valid Sudoku
- ✅ Spiral Matrix
- ✅ Rotate Image
- ✅ Set Matrix Zeroes
- ✅ Game of Life

---

## 5. Hash Map / Hash Set

### Core Theory

**Hash tables** provide average O(1) time complexity for insertion, deletion, and lookup operations. They work by using a hash function to map keys to array indices.

**Hash Function Properties:**
- **Deterministic**: Same input always produces same output
- **Uniform Distribution**: Keys should be evenly distributed
- **Fast Computation**: Hash function should be quick to compute

**Collision Resolution:**
1. **Chaining**: Store multiple elements in same bucket (linked list)
2. **Open Addressing**: Find another empty slot (linear probing, quadratic probing)

**Key Patterns:**

1. **Frequency Counting**:
   ```python
   def countFrequency(arr):
       freq = {}
       for item in arr:
           freq[item] = freq.get(item, 0) + 1
       return freq
   ```

2. **Two Sum Pattern** (Complement Lookup):
   ```python
   def twoSum(nums, target):
       seen = {}
       for i, num in enumerate(nums):
           complement = target - num
           if complement in seen:
               return [seen[complement], i]
           seen[num] = i
       return []
   ```

3. **Group Anagrams** (Canonical Key):
   ```python
   def groupAnagrams(strs):
       groups = {}
       for s in strs:
           key = ''.join(sorted(s))  # Canonical form
           if key not in groups:
               groups[key] = []
           groups[key].append(s)
       return list(groups.values())
   ```

4. **Longest Consecutive Sequence**:
   ```python
   def longestConsecutive(nums):
       num_set = set(nums)
       longest = 0
       
       for num in num_set:
           if num - 1 not in num_set:  # Start of sequence
               current_num = num
               current_length = 1
               
               while current_num + 1 in num_set:
                   current_num += 1
                   current_length += 1
               
               longest = max(longest, current_length)
       
       return longest
   ```

**Design Patterns:**
- **Isomorphic Mapping**: Map characters bidirectionally
- **Sliding Window with HashMap**: Track character frequencies
- **Union-Find Alternative**: Use HashMap for connected components

**Time Complexity:**
- Average case: O(1) for all operations
- Worst case: O(n) if all keys hash to same bucket
- Space: O(n) for storing n elements

### Questions from Top 150 (39–47)
- ✅ Ransom Note
- ✅ Isomorphic Strings
- ✅ Word Pattern
- ✅ Valid Anagram
- ✅ Group Anagrams
- ✅ Two Sum
- ✅ Happy Number
- ✅ Contains Duplicate II
- ✅ Longest Consecutive Sequence

---

## 6. Intervals

### Core Theory

**Interval problems** involve ranges of numbers, typically represented as `[start, end]`. Key insight: sorting intervals by start time often reveals the solution structure.

**Common Patterns:**

1. **Merging Overlapping Intervals**:
   ```python
   def merge(intervals):
       if not intervals:
           return []
       
       intervals.sort(key=lambda x: x[0])  # Sort by start time
       merged = [intervals[0]]
       
       for current in intervals[1:]:
           last = merged[-1]
           
           if current[0] <= last[1]:  # Overlapping
               last[1] = max(last[1], current[1])  # Merge
           else:
               merged.append(current)  # No overlap
       
       return merged
   ```

2. **Insert Interval**:
   ```python
   def insert(intervals, newInterval):
       result = []
       i = 0
       
       # Add all intervals that end before newInterval starts
       while i < len(intervals) and intervals[i][1] < newInterval[0]:
           result.append(intervals[i])
           i += 1
       
       # Merge all overlapping intervals with newInterval
       while i < len(intervals) and intervals[i][0] <= newInterval[1]:
           newInterval[0] = min(newInterval[0], intervals[i][0])
           newInterval[1] = max(newInterval[1], intervals[i][1])
           i += 1
       
       result.append(newInterval)
       
       # Add remaining intervals
       while i < len(intervals):
           result.append(intervals[i])
           i += 1
       
       return result
   ```

3. **Minimum Points to Cover Intervals** (Greedy):
   ```python
   def findMinArrowShots(points):
       if not points:
           return 0
       
       points.sort(key=lambda x: x[1])  # Sort by end point
       arrows = 1
       last_arrow = points[0][1]
       
       for start, end in points[1:]:
           if start > last_arrow:  # Need new arrow
               arrows += 1
               last_arrow = end
       
       return arrows
   ```

**Key Insights:**
- **Sort by start**: Good for merging intervals
- **Sort by end**: Good for greedy selection (activity selection problem)
- **Overlap detection**: `interval1[1] >= interval2[0]`
- **Greedy approach**: Often optimal for interval scheduling problems

**Sweep Line Algorithm:**
For complex interval problems, imagine a vertical line sweeping from left to right, processing events (interval starts/ends) in chronological order.

### Questions from Top 150 (48–51)
- ✅ Summary Ranges
- ✅ Merge Intervals
- ✅ Insert Interval
- ✅ Minimum Number of Arrows to Burst Balloons

---

## 7. Stack

### Core Theory

A **Stack** is a Last-In-First-Out (LIFO) data structure. Elements are added and removed from the same end (called the "top").

**Basic Operations:**
- `push(item)`: Add item to top - O(1)
- `pop()`: Remove and return top item - O(1)  
- `peek()/top()`: Return top item without removing - O(1)
- `isEmpty()`: Check if stack is empty - O(1)

**Key Applications:**

1. **Matching Brackets/Parentheses**:
   ```python
   def isValid(s):
       stack = []
       mapping = {')': '(', '}': '{', ']': '['}
       
       for char in s:
           if char in mapping:  # Closing bracket
               if not stack or stack.pop() != mapping[char]:
                   return False
           else:  # Opening bracket
               stack.append(char)
       
       return not stack
   ```

2. **Monotonic Stack** (Next Greater Element):
   ```python
   def nextGreaterElements(nums):
       result = [-1] * len(nums)
       stack = []  # Store indices
       
       for i in range(len(nums)):
           while stack and nums[i] > nums[stack[-1]]:
               idx = stack.pop()
               result[idx] = nums[i]
           stack.append(i)
       
       return result
   ```

3. **Expression Evaluation (Reverse Polish Notation)**:
   ```python
   def evalRPN(tokens):
       stack = []
       operators = {'+', '-', '*', '/'}
       
       for token in tokens:
           if token in operators:
               b = stack.pop()
               a = stack.pop()
               if token == '+': result = a + b
               elif token == '-': result = a - b
               elif token == '*': result = a * b
               elif token == '/': result = int(a / b)  # Truncate towards zero
               stack.append(result)
           else:
               stack.append(int(token))
       
       return stack[0]
   ```

4. **Min Stack (O(1) minimum)**:
   ```python
   class MinStack:
       def __init__(self):
           self.stack = []
           self.min_stack = []
       
       def push(self, val):
           self.stack.append(val)
           if not self.min_stack or val <= self.min_stack[-1]:
               self.min_stack.append(val)
       
       def pop(self):
           if self.stack.pop() == self.min_stack[-1]:
               self.min_stack.pop()
       
       def top(self):
           return self.stack[-1]
       
       def getMin(self):
           return self.min_stack[-1]
   ```

**Advanced Patterns:**
- **Function Call Stack**: Simulating recursion iteratively
- **Parsing**: Building parse trees, handling operator precedence
- **Path Simplification**: Unix path with `..` and `.`

**When to Use Stack:**
- Problems involving nested structures
- Undo operations
- Converting recursion to iteration
- Expression parsing and evaluation
- Backtracking (implicit stack via recursion)

### Questions from Top 150 (52–56)
- ✅ Valid Parentheses
- ✅ Simplify Path
- ✅ Min Stack
- ✅ Evaluate Reverse Polish Notation
- ✅ Basic Calculator

---

## 8. Linked List

### Core Theory

A **Linked List** is a linear data structure where elements (nodes) are stored in sequence, but not in contiguous memory locations. Each node contains data and a reference (pointer) to the next node.

**Node Structure**:
```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next
```

**Key Techniques:**

1. **Dummy Head Technique**:
   ```python
   def mergeTwoLists(list1, list2):
       dummy = ListNode(0)
       current = dummy
       
       while list1 and list2:
           if list1.val <= list2.val:
               current.next = list1
               list1 = list1.next
           else:
               current.next = list2
               list2 = list2.next
           current = current.next
       
       current.next = list1 if list1 else list2
       return dummy.next
   ```

2. **Fast & Slow Pointers (Floyd's Cycle Detection)**:
   ```python
   def hasCycle(head):
       if not head or not head.next:
           return False
       
       slow = fast = head
       while fast and fast.next:
           slow = slow.next
           fast = fast.next.next
           if slow == fast:
               return True
       return False
   ```

3. **Reversing Linked List**:
   ```python
   # Iterative
   def reverseList(head):
       prev = None
       current = head
       
       while current:
           next_temp = current.next
           current.next = prev
           prev = current
           current = next_temp
       
       return prev
   
   # Recursive
   def reverseListRecursive(head):
       if not head or not head.next:
           return head
       
       new_head = reverseListRecursive(head.next)
       head.next.next = head
       head.next = None
       return new_head
   ```

4. **Finding Middle of Linked List**:
   ```python
   def findMiddle(head):
       slow = fast = head
       while fast and fast.next:
           slow = slow.next
           fast = fast.next.next
       return slow
   ```

5. **Remove Nth Node from End**:
   ```python
   def removeNthFromEnd(head, n):
       dummy = ListNode(0)
       dummy.next = head
       first = second = dummy
       
       # Move first pointer n+1 steps ahead
       for _ in range(n + 1):
           first = first.next
       
       # Move both pointers until first reaches end
       while first:
           first = first.next
           second = second.next
       
       # Remove the nth node from end
       second.next = second.next.next
       return dummy.next
   ```

**Advanced Patterns:**

1. **Deep Copy with Random Pointers**:
   ```python
   def copyRandomList(head):
       if not head:
           return None
       
       # Phase 1: Create new nodes and interweave with original
       current = head
       while current:
           new_node = Node(current.val)
           new_node.next = current.next
           current.next = new_node
           current = new_node.next
       
       # Phase 2: Set random pointers
       current = head
       while current:
           if current.random:
               current.next.random = current.random.next
           current = current.next.next
       
       # Phase 3: Separate the lists
       dummy = Node(0)
       current_new = dummy
       current = head
       
       while current:
           current_new.next = current.next
           current.next = current.next.next
           current_new = current_new.next
           current = current.next
       
       return dummy.next
   ```

2. **LRU Cache** (HashMap + Doubly Linked List):
   ```python
   class LRUCache:
       def __init__(self, capacity):
           self.capacity = capacity
           self.cache = {}  # key -> node
           # Create dummy head and tail
           self.head = DLinkedNode()
           self.tail = DLinkedNode()
           self.head.next = self.tail
           self.tail.prev = self.head
       
       def addNode(self, node):
           # Add node right after head
           node.prev = self.head
           node.next = self.head.next
           self.head.next.prev = node
           self.head.next = node
       
       def removeNode(self, node):
           # Remove an existing node
           prev_node = node.prev
           new_node = node.next
           prev_node.next = new_node
           new_node.prev = prev_node
       
       def moveToHead(self, node):
           # Move node to head (mark as recently used)
           self.removeNode(node)
           self.addNode(node)
       
       def popTail(self):
           # Remove last node
           last_node = self.tail.prev
           self.removeNode(last_node)
           return last_node
   ```

**Time Complexities:**
- Access: O(n) - must traverse from head
- Search: O(n)
- Insertion: O(1) if position known
- Deletion: O(1) if node reference available

### Questions from Top 150 (57–67)
- ✅ Linked List Cycle
- ✅ Add Two Numbers
- ✅ Merge Two Sorted Lists
- ✅ Copy List with Random Pointer
- ✅ Reverse Linked List II
- ✅ Reverse Nodes in k-Group
- ✅ Remove Nth Node From End of List
- ✅ Remove Duplicates from Sorted List II
- ✅ Rotate List
- ✅ Partition List
- ✅ LRU Cache

---

## 9. Binary Tree (General / DFS)

### Core Theory

A **Binary Tree** is a hierarchical data structure where each node has at most two children: left and right. Trees are naturally recursive structures.

**Tree Node Structure**:
```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right
```

**Tree Traversals:**

1. **Depth-First Search (DFS)**:
   ```python
   # Preorder (Root → Left → Right)
   def preorder(root):
       if not root:
           return []
       return [root.val] + preorder(root.left) + preorder(root.right)
   
   # Inorder (Left → Root → Right)
   def inorder(root):
       if not root:
           return []
       return inorder(root.left) + [root.val] + inorder(root.right)
   
   # Postorder (Left → Right → Root)
   def postorder(root):
       if not root:
           return []
       return postorder(root.left) + postorder(root.right) + [root.val]
   ```

2. **Iterative Traversals with Stack**:
   ```python
   def preorderIterative(root):
       if not root:
           return []
       
       result = []
       stack = [root]
       
       while stack:
           node = stack.pop()
           result.append(node.val)
           
           if node.right:  # Push right first
               stack.append(node.right)
           if node.left:   # Push left second (processed first)
               stack.append(node.left)
       
       return result
   ```

**Key Patterns:**

1. **Tree Construction from Traversals**:
   ```python
   def buildTree(preorder, inorder):
       if not preorder or not inorder:
           return None
       
       root = TreeNode(preorder[0])
       mid = inorder.index(preorder[0])
       
       root.left = buildTree(preorder[1:mid+1], inorder[:mid])
       root.right = buildTree(preorder[mid+1:], inorder[mid+1:])
       
       return root
   ```

2. **Path Sum Problems**:
   ```python
   def hasPathSum(root, targetSum):
       if not root:
           return False
       
       if not root.left and not root.right:  # Leaf node
           return root.val == targetSum
       
       targetSum -= root.val
       return (hasPathSum(root.left, targetSum) or 
               hasPathSum(root.right, targetSum))
   ```

3. **Maximum Path Sum** (Return Info Up Pattern):
   ```python
   def maxPathSum(root):
       max_sum = float('-inf')
       
       def helper(node):
           nonlocal max_sum
           if not node:
               return 0
           
           # Get max path sums from left and right subtrees
           left_sum = max(0, helper(node.left))   # Ignore negative paths
           right_sum = max(0, helper(node.right))
           
           # Current path sum including this node as highest point
           current_path = node.val + left_sum + right_sum
           max_sum = max(max_sum, current_path)
           
           # Return max path sum going through this node (for parent)
           return node.val + max(left_sum, right_sum)
       
       helper(root)
       return max_sum
   ```

4. **Lowest Common Ancestor**:
   ```python
   def lowestCommonAncestor(root, p, q):
       if not root or root == p or root == q:
           return root
       
       left = lowestCommonAncestor(root.left, p, q)
       right = lowestCommonAncestor(root.right, p, q)
       
       if left and right:  # p and q in different subtrees
           return root
       
       return left if left else right  # Both in same subtree
   ```

**Tree Modification Patterns:**
- **Invert Binary Tree**: Swap left and right children recursively
- **Flatten to Linked List**: Use preorder traversal and restructure
- **Connect Next Pointers**: Level-by-level connection

**Time Complexities:**
- Traversal: O(n) - visit each node once
- Search: O(h) where h is height (O(log n) balanced, O(n) skewed)
- Insertion/Deletion: O(h)

### Questions from Top 150 (68–81)
- ✅ Maximum Depth of Binary Tree
- ✅ Same Tree
- ✅ Invert Binary Tree
- ✅ Symmetric Tree
- ✅ Construct Binary Tree from Preorder and Inorder Traversal
- ✅ Construct Binary Tree from Inorder and Postorder Traversal
- ✅ Populating Next Right Pointers in Each Node II
- ❌ Flatten Binary Tree to Linked List
- ❌ Path Sum
- ❌ Sum Root to Leaf Numbers
- ❌ Binary Tree Maximum Path Sum
- ❌ Binary Search Tree Iterator
- ❌ Count Complete Tree Nodes
- ❌ Lowest Common Ancestor of a Binary Tree

---

## 10. Binary Tree BFS

### Core Theory

**Breadth-First Search (BFS)** on trees explores nodes level by level, using a queue data structure. This is also called **Level-Order Traversal**.

**Basic BFS Template**:
```python
from collections import deque

def levelOrder(root):
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        current_level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(current_level)
    
    return result
```

**Key BFS Patterns:**

1. **Right Side View** (Last Node at Each Level):
   ```python
   def rightSideView(root):
       if not root:
           return []
       
       result = []
       queue = deque([root])
       
       while queue:
           level_size = len(queue)
           
           for i in range(level_size):
               node = queue.popleft()
               
               if i == level_size - 1:  # Last node in level
                   result.append(node.val)
               
               if node.left:
                   queue.append(node.left)
               if node.right:
                   queue.append(node.right)
       
       return result
   ```

2. **Zigzag Level Order** (Alternate Direction):
   ```python
   def zigzagLevelOrder(root):
       if not root:
           return []
       
       result = []
       queue = deque([root])
       left_to_right = True
       
       while queue:
           level_size = len(queue)
           current_level = deque()
           
           for _ in range(level_size):
               node = queue.popleft()
               
               if left_to_right:
                   current_level.append(node.val)
               else:
                   current_level.appendleft(node.val)
               
               if node.left:
                   queue.append(node.left)
               if node.right:
                   queue.append(node.right)
           
           result.append(list(current_level))
           left_to_right = not left_to_right
       
       return result
   ```

3. **Average of Levels**:
   ```python
   def averageOfLevels(root):
       if not root:
           return []
       
       result = []
       queue = deque([root])
       
       while queue:
           level_size = len(queue)
           level_sum = 0
           
           for _ in range(level_size):
               node = queue.popleft()
               level_sum += node.val
               
               if node.left:
                   queue.append(node.left)
               if node.right:
                   queue.append(node.right)
           
           result.append(level_sum / level_size)
       
       return result
   ```

**BFS vs DFS:**
- **BFS**: Level-by-level, uses queue, good for shortest path, level-related problems
- **DFS**: Depth-first, uses stack/recursion, good for path problems, tree construction

**When to Use BFS:**
- Level-order traversal
- Finding shortest path (in unweighted trees/graphs)
- Problems requiring level-by-level processing
- Finding nodes at specific level/distance

**Space Complexity**: O(w) where w is maximum width of tree (can be O(n) in worst case)

### Questions from Top 150 (82–85)
- ❌ Binary Tree Right Side View
- ❌ Average of Levels in Binary Tree
- ❌ Binary Tree Level Order Traversal
- ❌ Binary Tree Zigzag Level Order Traversal

---

## 11. Binary Search Tree

### Core Theory

A **Binary Search Tree (BST)** is a binary tree with the ordering property:
- All nodes in left subtree < root
- All nodes in right subtree > root
- Both left and right subtrees are also BSTs

**BST Properties:**
- **Inorder traversal** of BST gives nodes in sorted order
- **Search/Insert/Delete**: O(h) where h is height
- **Best case**: h = log n (balanced tree)
- **Worst case**: h = n (skewed tree)

**Key Operations:**

1. **Search in BST**:
   ```python
   def searchBST(root, val):
       if not root or root.val == val:
           return root
       
       if val < root.val:
           return searchBST(root.left, val)
       else:
           return searchBST(root.right, val)
   ```

2. **Insert into BST**:
   ```python
   def insertIntoBST(root, val):
       if not root:
           return TreeNode(val)
       
       if val < root.val:
           root.left = insertIntoBST(root.left, val)
       else:
           root.right = insertIntoBST(root.right, val)
       
       return root
   ```

3. **Validate BST**:
   ```python
   def isValidBST(root):
       def validate(node, min_val, max_val):
           if not node:
               return True
           
           if node.val <= min_val or node.val >= max_val:
               return False
           
           return (validate(node.left, min_val, node.val) and
                   validate(node.right, node.val, max_val))
       
       return validate(root, float('-inf'), float('inf'))
   ```

4. **Kth Smallest Element** (Inorder Traversal):
   ```python
   def kthSmallest(root, k):
       def inorder(node):
           if not node:
               return []
           return inorder(node.left) + [node.val] + inorder(node.right)
       
       return inorder(root)[k-1]
   
   # Optimized version (early termination)
   def kthSmallestOptimized(root, k):
       stack = []
       current = root
       
       while current or stack:
           while current:
               stack.append(current)
               current = current.left
           
           current = stack.pop()
           k -= 1
           if k == 0:
               return current.val
           
           current = current.right
       
       return -1  # Not found
   ```

5. **BST Iterator** (Inorder Simulation):
   ```python
   class BSTIterator:
       def __init__(self, root):
           self.stack = []
           self._leftmost_inorder(root)
       
       def _leftmost_inorder(self, root):
           while root:
               self.stack.append(root)
               root = root.left
       
       def next(self):
           topmost_node = self.stack.pop()
           if topmost_node.right:
               self._leftmost_inorder(topmost_node.right)
           return topmost_node.val
       
       def hasNext(self):
           return len(self.stack) > 0
   ```

**Common BST Patterns:**
- **Inorder traversal** for sorted access
- **Predecessor/Successor**: Largest in left subtree / smallest in right subtree
- **Range queries**: Use BST properties to prune search space
- **Convert to balanced**: Get inorder, build balanced BST

**BST vs Hash Table:**
- BST: O(log n) operations, sorted iteration, range queries
- Hash Table: O(1) average operations, no ordering

### Questions from Top 150 (86–88)
- ❌ Minimum Absolute Difference in BST
- ❌ Kth Smallest Element in a BST  
- ❌ Validate Binary Search Tree

---

## 12. Graph (General / DFS & Union-Find)

### Core Theory

A **Graph** is a collection of vertices (nodes) connected by edges. Graphs can be **directed** or **undirected**, **weighted** or **unweighted**.

**Graph Representations:**

1. **Adjacency List** (Most Common):
   ```python
   # For unweighted graph
   graph = {
       'A': ['B', 'C'],
       'B': ['A', 'D'],
       'C': ['A', 'D'],
       'D': ['B', 'C']
   }
   
   # For weighted graph
   graph = {
       'A': [('B', 4), ('C', 2)],
       'B': [('A', 4), ('D', 3)],
       'C': [('A', 2), ('D', 1)],
       'D': [('B', 3), ('C', 1)]
   }
   ```

2. **Adjacency Matrix**:
   ```python
   # graph[i][j] = 1 if edge exists from i to j
   graph = [
       [0, 1, 1, 0],
       [1, 0, 0, 1],
       [1, 0, 0, 1],
       [0, 1, 1, 0]
   ]
   ```

**Graph Traversal Algorithms:**

1. **Depth-First Search (DFS)**:
   ```python
   def dfs(graph, start, visited=None):
       if visited is None:
           visited = set()
       
       visited.add(start)
       print(start, end=' ')
       
       for neighbor in graph[start]:
           if neighbor not in visited:
               dfs(graph, neighbor, visited)
       
       return visited
   
   # Iterative DFS with stack
   def dfs_iterative(graph, start):
       visited = set()
       stack = [start]
       
       while stack:
           node = stack.pop()
           if node not in visited:
               visited.add(node)
               print(node, end=' ')
               
               for neighbor in graph[node]:
                   if neighbor not in visited:
                       stack.append(neighbor)
       
       return visited
   ```

2. **Connected Components** (DFS):
   ```python
   def numIslands(grid):
       if not grid:
           return 0
       
       rows, cols = len(grid), len(grid[0])
       islands = 0
       
       def dfs(r, c):
           if (r < 0 or r >= rows or c < 0 or c >= cols or 
               grid[r][c] != '1'):
               return
           
           grid[r][c] = '0'  # Mark as visited
           
           # Explore 4 directions
           dfs(r+1, c)
           dfs(r-1, c)
           dfs(r, c+1)
           dfs(r, c-1)
       
       for r in range(rows):
           for c in range(cols):
               if grid[r][c] == '1':
                   dfs(r, c)
                   islands += 1
       
       return islands
   ```

**Topological Sort** (For DAGs):

1. **Kahn's Algorithm** (BFS-based):
   ```python
   from collections import deque, defaultdict
   
   def topological_sort(graph, n):
       # Calculate in-degrees
       indegree = [0] * n
       for node in graph:
           for neighbor in graph[node]:
               indegree[neighbor] += 1
       
       # Start with nodes having 0 in-degree
       queue = deque([i for i in range(n) if indegree[i] == 0])
       result = []
       
       while queue:
           node = queue.popleft()
           result.append(node)
           
           for neighbor in graph[node]:
               indegree[neighbor] -= 1
               if indegree[neighbor] == 0:
                   queue.append(neighbor)
       
       return result if len(result) == n else []  # Cycle detection
   ```

2. **DFS-based Topological Sort**:
   ```python
   def topological_sort_dfs(graph, n):
       WHITE, GRAY, BLACK = 0, 1, 2
       color = [WHITE] * n
       result = []
       has_cycle = False
       
       def dfs(node):
           nonlocal has_cycle
           if color[node] == GRAY:  # Back edge - cycle detected
               has_cycle = True
               return
           if color[node] == BLACK:  # Already processed
               return
           
           color[node] = GRAY
           for neighbor in graph[node]:
               dfs(neighbor)
           
           color[node] = BLACK
           result.append(node)
       
       for i in range(n):
           if color[i] == WHITE:
               dfs(i)
       
       return result[::-1] if not has_cycle else []
   ```

**Union-Find (Disjoint Set Union)**:
```python
class UnionFind:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0] * n
        self.components = n
    
    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # Path compression
        return self.parent[x]
    
    def union(self, x, y):
        px, py = self.find(x), self.find(y)
        if px == py:
            return False
        
        # Union by rank
        if self.rank[px] < self.rank[py]:
            self.parent[px] = py
        elif self.rank[px] > self.rank[py]:
            self.parent[py] = px
        else:
            self.parent[py] = px
            self.rank[px] += 1
        
        self.components -= 1
        return True
    
    def connected(self, x, y):
        return self.find(x) == self.find(y)
```

**Graph Cloning**:
```python
def cloneGraph(node):
    if not node:
        return None
    
    visited = {}
    
    def dfs(node):
        if node in visited:
            return visited[node]
        
        clone = Node(node.val)
        visited[node] = clone
        
        for neighbor in node.neighbors:
            clone.neighbors.append(dfs(neighbor))
        
        return clone
    
    return dfs(node)
```

### Questions from Top 150 (89–94)
- ❌ Number of Islands
- ❌ Surrounded Regions  
- ❌ Clone Graph
- ❌ Evaluate Division
- ❌ Course Schedule
- ❌ Course Schedule II

---

## 13. Graph BFS

### Core Theory

**Breadth-First Search (BFS)** explores graph level by level using a queue. It's optimal for finding **shortest paths** in **unweighted graphs**.

**Basic BFS Template**:
```python
from collections import deque

def bfs(graph, start):
    visited = set([start])
    queue = deque([start])
    
    while queue:
        node = queue.popleft()
        print(node, end=' ')
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

**Shortest Path in Unweighted Graph**:
```python
def shortest_path(graph, start, target):
    if start == target:
        return 0
    
    visited = set([start])
    queue = deque([(start, 0)])  # (node, distance)
    
    while queue:
        node, dist = queue.popleft()
        
        for neighbor in graph[node]:
            if neighbor == target:
                return dist + 1
            
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append((neighbor, dist + 1))
    
    return -1  # Not reachable
```

**Key BFS Patterns:**

1. **Grid BFS** (4-directional):
   ```python
   def bfs_grid(grid, start_r, start_c):
       rows, cols = len(grid), len(grid[0])
       visited = set()
       queue = deque([(start_r, start_c, 0)])  # (row, col, steps)
       directions = [(0,1), (1,0), (0,-1), (-1,0)]
       
       while queue:
           r, c, steps = queue.popleft()
           
           if (r, c) in visited:
               continue
           
           visited.add((r, c))
           
           for dr, dc in directions:
               nr, nc = r + dr, c + dc
               if (0 <= nr < rows and 0 <= nc < cols and 
                   (nr, nc) not in visited and grid[nr][nc] != 'X'):
                   queue.append((nr, nc, steps + 1))
   ```

2. **Multi-Source BFS**:
   ```python
   def multi_source_bfs(grid):
       rows, cols = len(grid), len(grid[0])
       queue = deque()
       
       # Add all sources to queue initially
       for r in range(rows):
           for c in range(cols):
               if grid[r][c] == 'SOURCE':
                   queue.append((r, c, 0))
       
       while queue:
           r, c, dist = queue.popleft()
           
           for dr, dc in [(0,1), (1,0), (0,-1), (-1,0)]:
               nr, nc = r + dr, c + dc
               if (0 <= nr < rows and 0 <= nc < cols and 
                   grid[nr][nc] == 'EMPTY'):
                   grid[nr][nc] = dist + 1
                   queue.append((nr, nc, dist + 1))
   ```

3. **Word Ladder Pattern** (Transformation Graph):
   ```python
   def ladderLength(beginWord, endWord, wordList):
       if endWord not in wordList:
           return 0
       
       wordSet = set(wordList)
       queue = deque([(beginWord, 1)])
       visited = set([beginWord])
       
       while queue:
           word, length = queue.popleft()
           
           if word == endWord:
               return length
           
           # Try all possible one-character changes
           for i in range(len(word)):
               for c in 'abcdefghijklmnopqrstuvwxyz':
                   if c != word[i]:
                       new_word = word[:i] + c + word[i+1:]
                       
                       if new_word in wordSet and new_word not in visited:
                           visited.add(new_word)
                           queue.append((new_word, length + 1))
       
       return 0
   ```

4. **Bidirectional BFS** (Optimization for shortest path):
   ```python
   def bidirectional_bfs(graph, start, target):
       if start == target:
           return 0
       
       # Two sets: one from start, one from target
       front_queue = deque([(start, 0)])
       back_queue = deque([(target, 0)])
       front_visited = {start: 0}
       back_visited = {target: 0}
       
       while front_queue or back_queue:
           # Expand the smaller frontier
           if len(front_queue) <= len(back_queue):
               node, dist = front_queue.popleft()
               
               for neighbor in graph[node]:
                   if neighbor in back_visited:
                       return dist + 1 + back_visited[neighbor]
                   
                   if neighbor not in front_visited:
                       front_visited[neighbor] = dist + 1
                       front_queue.append((neighbor, dist + 1))
           else:
               node, dist = back_queue.popleft()
               
               for neighbor in graph[node]:
                   if neighbor in front_visited:
                       return dist + 1 + front_visited[neighbor]
                   
                   if neighbor not in back_visited:
                       back_visited[neighbor] = dist + 1
                       back_queue.append((neighbor, dist + 1))
       
       return -1
   ```

**BFS Applications:**
- **Shortest path** in unweighted graphs
- **Level-order traversal** in trees
- **Connected components** in graphs
- **Bipartite graph** checking (2-coloring)
- **Snake and Ladders** type problems

**Time Complexity**: O(V + E) where V is vertices and E is edges
**Space Complexity**: O(V) for visited set and queue

### Questions from Top 150 (95–97)
- ❌ Snakes and Ladders
- ❌ Minimum Genetic Mutation
- ❌ Word Ladder

---

## 14. Trie

### Core Theory

A **Trie** (Prefix Tree) is a tree-like data structure used for storing and searching strings efficiently. Each node represents a character, and paths from root to nodes represent prefixes.

**Trie Node Structure**:
```python
class TrieNode:
    def __init__(self):
        self.children = {}  # or [None] * 26 for only lowercase letters
        self.is_end_of_word = False
        self.word = None  # Optional: store complete word at end nodes
```

**Basic Trie Implementation**:
```python
class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end_of_word = True
    
    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end_of_word
    
    def startsWith(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
```

**Advanced Trie Patterns:**

1. **Wildcard Search** (with '.' as any character):
   ```python
   class WordDictionary:
       def __init__(self):
           self.root = TrieNode()
       
       def addWord(self, word):
           node = self.root
           for char in word:
               if char not in node.children:
                   node.children[char] = TrieNode()
               node = node.children[char]
           node.is_end_of_word = True
       
       def search(self, word):
           def dfs(node, i):
               if i == len(word):
                   return node.is_end_of_word
               
               char = word[i]
               if char == '.':
                   # Try all possible characters
                   for child in node.children.values():
                       if dfs(child, i + 1):
                           return True
                   return False
               else:
                   if char not in node.children:
                       return False
                   return dfs(node.children[char], i + 1)
           
           return dfs(self.root, 0)
   ```

2. **Word Search II** (Trie + DFS on Grid):
   ```python
   def findWords(board, words):
       # Build Trie
       root = TrieNode()
       for word in words:
           node = root
           for char in word:
               if char not in node.children:
                   node.children[char] = TrieNode()
               node = node.children[char]
           node.is_end_of_word = True
           node.word = word
       
       rows, cols = len(board), len(board[0])
       result = []
       
       def dfs(r, c, node):
           if node.is_end_of_word:
               result.append(node.word)
               node.is_end_of_word = False  # Avoid duplicates
           
           if r < 0 or r >= rows or c < 0 or c >= cols:
               return
           
           char = board[r][c]
           if char not in node.children:
               return
           
           board[r][c] = '#'  # Mark as visited
           child_node = node.children[char]
           
           # Explore 4 directions
           dfs(r+1, c, child_node)
           dfs(r-1, c, child_node)
           dfs(r, c+1, child_node)
           dfs(r, c-1, child_node)
           
           board[r][c] = char  # Restore
           
           # Optimization: remove empty nodes
           if not child_node.children and not child_node.is_end_of_word:
               del node.children[char]
       
       for r in range(rows):
           for c in range(cols):
               if board[r][c] in root.children:
                   dfs(r, c, root)
       
       return result
   ```

3. **Auto-Complete** (Find all words with prefix):
   ```python
   def find_words_with_prefix(self, prefix):
       def dfs(node, current_word):
           words = []
           if node.is_end_of_word:
               words.append(current_word)
           
           for char, child in node.children.items():
               words.extend(dfs(child, current_word + char))
           
           return words
       
       # Navigate to prefix
       node = self.root
       for char in prefix:
           if char not in node.children:
               return []
           node = node.children[char]
       
       return dfs(node, prefix)
   ```

4. **Longest Common Prefix**:
   ```python
   def longestCommonPrefix(strs):
       if not strs:
           return ""
       
       trie = Trie()
       for word in strs:
           trie.insert(word)
       
       prefix = ""
       node = trie.root
       
       while len(node.children) == 1 and not node.is_end_of_word:
           char = next(iter(node.children))
           prefix += char
           node = node.children[char]
       
       return prefix
   ```

**Trie Applications:**
- **Auto-complete/Auto-suggestion**
- **Spell checkers**
- **IP routing tables**
- **Word games** (Scrabble, Boggle)
- **String matching** with wildcards

**Time Complexities:**
- **Insert**: O(m) where m is string length
- **Search**: O(m)
- **Space**: O(ALPHABET_SIZE × N × M) in worst case

**Trie vs Hash Table:**
- **Trie**: Good for prefix operations, sorted traversal, memory efficient for common prefixes
- **Hash Table**: Faster for exact matches, simpler implementation

### Questions from Top 150 (98–100)
- ❌ Implement Trie (Prefix Tree)
- ❌ Design Add and Search Words Data Structure
- ❌ Word Search II

---

## 15. Backtracking

### Core Theory

**Backtracking** is a problem-solving technique that incrementally builds solutions and abandons ("backtracks") paths that cannot lead to valid solutions. It's essentially a refined brute-force approach with pruning.

**Backtracking Template**:
```python
def backtrack(candidate):
    if is_solution(candidate):
        process_solution(candidate)
        return
    
    for next_choice in get_choices(candidate):
        # Make choice
        candidate.add(next_choice)
        
        # Recurse
        if is_valid(candidate):
            backtrack(candidate)
        
        # Unmake choice (backtrack)
        candidate.remove(next_choice)
```

**Key Backtracking Problems:**

1. **Generate Parentheses**:
   ```python
   def generateParenthesis(n):
       result = []
       
       def backtrack(current, open_count, close_count):
           if len(current) == 2 * n:
               result.append(current)
               return
           
           if open_count < n:
               backtrack(current + '(', open_count + 1, close_count)
           
           if close_count < open_count:
               backtrack(current + ')', open_count, close_count + 1)
       
       backtrack('', 0, 0)
       return result
   ```

2. **Combinations**:
   ```python
   def combine(n, k):
       result = []
       
       def backtrack(start, path):
           if len(path) == k:
               result.append(path[:])  # Make a copy
               return
           
           for i in range(start, n + 1):
               path.append(i)
               backtrack(i + 1, path)  # i+1 to avoid duplicates
               path.pop()
       
       backtrack(1, [])
       return result
   ```

3. **Permutations**:
   ```python
   def permute(nums):
       result = []
       
       def backtrack(path):
           if len(path) == len(nums):
               result.append(path[:])
               return
           
           for num in nums:
               if num not in path:
                   path.append(num)
                   backtrack(path)
                   path.pop()
       
       backtrack([])
       return result
   
   # Optimized version with used array
   def permuteOptimized(nums):
       result = []
       used = [False] * len(nums)
       
       def backtrack(path):
           if len(path) == len(nums):
               result.append(path[:])
               return
           
           for i in range(len(nums)):
               if not used[i]:
                   used[i] = True
                   path.append(nums[i])
                   backtrack(path)
                   path.pop()
                   used[i] = False
       
       backtrack([])
       return result
   ```

4. **Combination Sum** (Elements can be reused):
   ```python
   def combinationSum(candidates, target):
       result = []
       
       def backtrack(start, path, remaining):
           if remaining == 0:
               result.append(path[:])
               return
           
           for i in range(start, len(candidates)):
               if candidates[i] <= remaining:
                   path.append(candidates[i])
                   # Same index can be reused (i, not i+1)
                   backtrack(i, path, remaining - candidates[i])
                   path.pop()
       
       backtrack(0, [], target)
       return result
   ```

5. **N-Queens II** (Count solutions):
   ```python
   def totalNQueens(n):
       def is_safe(row, col, positions):
           for r, c in positions:
               if (c == col or                    # Same column
                   r - c == row - col or          # Same diagonal
                   r + c == row + col):           # Same anti-diagonal
                   return False
           return True
       
       def backtrack(row, positions):
           if row == n:
               return 1
           
           count = 0
           for col in range(n):
               if is_safe(row, col, positions):
                   positions.append((row, col))
                   count += backtrack(row + 1, positions)
                   positions.pop()
           
           return count
       
       return backtrack(0, [])
   
   # Optimized with sets for O(1) conflict checking
   def totalNQueensOptimized(n):
       def backtrack(row, cols, diag1, diag2):
           if row == n:
               return 1
           
           count = 0
           for col in range(n):
               if col not in cols and (row - col) not in diag1 and (row + col) not in diag2:
                   cols.add(col)
                   diag1.add(row - col)
                   diag2.add(row + col)
                   
                   count += backtrack(row + 1, cols, diag1, diag2)
                   
                   cols.remove(col)
                   diag1.remove(row - col)
                   diag2.remove(row + col)
           
           return count
       
       return backtrack(0, set(), set(), set())
   ```

6. **Word Search** (2D Grid):
   ```python
   def exist(board, word):
       rows, cols = len(board), len(board[0])
       
       def backtrack(r, c, index):
           if index == len(word):
               return True
           
           if (r < 0 or r >= rows or c < 0 or c >= cols or 
               board[r][c] != word[index]):
               return False
           
           # Mark as visited
           temp = board[r][c]
           board[r][c] = '#'
           
           # Explore 4 directions
           found = (backtrack(r+1, c, index+1) or
                   backtrack(r-1, c, index+1) or
                   backtrack(r, c+1, index+1) or
                   backtrack(r, c-1, index+1))
           
           # Restore
           board[r][c] = temp
           
           return found
       
       for r in range(rows):
           for c in range(cols):
               if backtrack(r, c, 0):
                   return True
       
       return False
   ```

7. **Letter Combinations of Phone Number**:
   ```python
   def letterCombinations(digits):
       if not digits:
           return []
       
       phone = {
           '2': 'abc', '3': 'def', '4': 'ghi', '5': 'jkl',
           '6': 'mno', '7': 'pqrs', '8': 'tuv', '9': 'wxyz'
       }
       
       result = []
       
       def backtrack(index, path):
           if index == len(digits):
               result.append(path)
               return
           
           digit = digits[index]
           for letter in phone[digit]:
               backtrack(index + 1, path + letter)
       
       backtrack(0, '')
       return result
   ```

**Backtracking Optimization Techniques:**
1. **Early Termination**: Stop exploring if current path cannot lead to solution
2. **Constraint Propagation**: Use constraints to prune search space
3. **Ordering**: Try most promising candidates first
4. **Symmetry Breaking**: Avoid exploring equivalent states

**Time Complexity**: Often exponential O(b^d) where b is branching factor and d is depth, but pruning can significantly reduce actual runtime.

### Questions from Top 150 (101–107)
- ❌ Letter Combinations of a Phone Number
- ❌ Combinations
- ❌ Permutations
- ❌ Combination Sum
- ❌ N-Queens II
- ❌ Generate Parentheses
- ❌ Word Search

---

## 16. Divide & Conquer

### Core Theory

**Divide & Conquer** breaks down problems into smaller subproblems of the same type, solves them recursively, and combines results. The approach follows three steps:
1. **Divide**: Break problem into smaller subproblems
2. **Conquer**: Solve subproblems recursively  
3. **Combine**: Merge solutions of subproblems

**Master Theorem** (for analyzing time complexity):
For recurrence T(n) = aT(n/b) + f(n):
- If f(n) = O(n^c) where c < log_b(a): T(n) = O(n^(log_b(a)))
- If f(n) = O(n^c) where c = log_b(a): T(n) = O(n^c * log n)  
- If f(n) = O(n^c) where c > log_b(a): T(n) = O(f(n))

**Classic Divide & Conquer Problems:**

1. **Merge Sort**:
   ```python
   def mergeSort(arr):
       if len(arr) <= 1:
           return arr
       
       mid = len(arr) // 2
       left = mergeSort(arr[:mid])
       right = mergeSort(arr[mid:])
       
       return merge(left, right)
   
   def merge(left, right):
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

2. **Sort Linked List** (Merge Sort on Linked List):
   ```python
   def sortList(head):
       if not head or not head.next:
           return head
       
       # Find middle using slow/fast pointers
       slow = fast = head
       prev = None
       
       while fast and fast.next:
           prev = slow
           slow = slow.next
           fast = fast.next.next
       
       # Split the list
       prev.next = None
       
       # Recursively sort both halves
       left = sortList(head)
       right = sortList(slow)
       
       # Merge sorted halves
       return mergeTwoLists(left, right)
   
   def mergeTwoLists(l1, l2):
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

3. **Convert Sorted Array to BST**:
   ```python
   def sortedArrayToBST(nums):
       if not nums:
           return None
       
       mid = len(nums) // 2
       root = TreeNode(nums[mid])
       
       root.left = sortedArrayToBST(nums[:mid])
       root.right = sortedArrayToBST(nums[mid+1:])
       
       return root
   ```

4. **Merge k Sorted Lists** (Divide & Conquer approach):
   ```python
   def mergeKLists(lists):
       if not lists:
           return None
       
       while len(lists) > 1:
           merged_lists = []
           
           for i in range(0, len(lists), 2):
               l1 = lists[i]
               l2 = lists[i+1] if i+1 < len(lists) else None
               merged_lists.append(mergeTwoLists(l1, l2))
           
           lists = merged_lists
       
       return lists[0]
   
   # Alternative recursive approach
   def mergeKListsRecursive(lists):
       if not lists:
           return None
       if len(lists) == 1:
           return lists[0]
       
       mid = len(lists) // 2
       left = mergeKListsRecursive(lists[:mid])
       right = mergeKListsRecursive(lists[mid:])
       
       return mergeTwoLists(left, right)
   ```

5. **Quick Sort**:
   ```python
   def quickSort(arr, low, high):
       if low < high:
           pi = partition(arr, low, high)
           quickSort(arr, low, pi - 1)
           quickSort(arr, pi + 1, high)
   
   def partition(arr, low, high):
       pivot = arr[high]
       i = low - 1
       
       for j in range(low, high):
           if arr[j] <= pivot:
               i += 1
               arr[i], arr[j] = arr[j], arr[i]
       
       arr[i + 1], arr[high] = arr[high], arr[i + 1]
       return i + 1
   ```

6. **QuadTree Construction**:
   ```python
   def construct(grid):
       n = len(grid)
       
       def is_uniform(r, c, size):
           val = grid[r][c]
           for i in range(r, r + size):
               for j in range(c, c + size):
                   if grid[i][j] != val:
                       return False
           return True
       
       def build(r, c, size):
           if is_uniform(r, c, size):
               return Node(grid[r][c] == 1, True)  # isLeaf = True
           
           half = size // 2
           return Node(
               False,  # val doesn't matter for internal nodes
               False,  # isLeaf = False
               build(r, c, half),              # topLeft
               build(r, c + half, half),       # topRight  
               build(r + half, c, half),       # bottomLeft
               build(r + half, c + half, half) # bottomRight
           )
       
       return build(0, 0, n)
   ```

7. **Maximum Subarray** (Divide & Conquer approach):
   ```python
   def maxSubArrayDivideConquer(nums):
       def helper(nums, left, right):
           if left == right:
               return nums[left]
           
           mid = (left + right) // 2
           
           # Maximum subarray ending at mid (going left)
           left_sum = float('-inf')
           current_sum = 0
           for i in range(mid, left - 1, -1):
               current_sum += nums[i]
               left_sum = max(left_sum, current_sum)
           
           # Maximum subarray starting at mid+1 (going right)
           right_sum = float('-inf')
           current_sum = 0
           for i in range(mid + 1, right + 1):
               current_sum += nums[i]
               right_sum = max(right_sum, current_sum)
           
           # Maximum crossing the middle
           cross_sum = left_sum + right_sum
           
           # Recursively find max in left and right halves
           left_max = helper(nums, left, mid)
           right_max = helper(nums, mid + 1, right)
           
           return max(cross_sum, left_max, right_max)
       
       return helper(nums, 0, len(nums) - 1)
   ```

**Advantages of Divide & Conquer:**
- Often leads to optimal algorithms
- Natural for recursive problems
- Can be parallelized
- Clear structure and analysis

**When to Use:**
- Problem can be broken into similar subproblems
- Optimal substructure exists
- Overlapping subproblems are minimal (otherwise use DP)

### Questions from Top 150 (108–111)
- ❌ Convert Sorted Array to Binary Search Tree
- ❌ Sort List
- ❌ Construct Quad Tree  
- ❌ Merge k Sorted Lists

---

## 17. Kadane's Algorithm

### Core Theory

**Kadane's Algorithm** finds the maximum sum of any contiguous subarray in O(n) time. It's a specific application of dynamic programming that uses optimal substructure.

**Core Insight**: At each position, we decide whether to:
- Start a new subarray from current element, or  
- Extend the existing subarray by including current element

**Basic Kadane's Algorithm**:
```python
def maxSubArray(nums):
    max_sum = nums[0]
    current_sum = nums[0]
    
    for i in range(1, len(nums)):
        # Either extend existing subarray or start new one
        current_sum = max(nums[i], current_sum + nums[i])
        max_sum = max(max_sum, current_sum)
    
    return max_sum

# Alternative implementation
def maxSubArrayAlt(nums):
    max_ending_here = max_so_far = nums[0]
    
    for i in range(1, len(nums)):
        max_ending_here = max(nums[i], max_ending_here + nums[i])
        max_so_far = max(max_so_far, max_ending_here)
    
    return max_so_far
```

**Why Kadane's Algorithm Works**:
- If `current_sum + nums[i] < nums[i]`, then starting fresh from `nums[i]` is better
- We keep track of the maximum sum seen so far
- This greedy choice at each step leads to global optimum

**Kadane's Algorithm Variations:**

1. **Track Indices** (Start and end of max subarray):
   ```python
   def maxSubArrayWithIndices(nums):
       max_sum = current_sum = nums[0]
       start = end = 0
       temp_start = 0
       
       for i in range(1, len(nums)):
           if current_sum < 0:
               current_sum = nums[i]
               temp_start = i
           else:
               current_sum += nums[i]
           
           if current_sum > max_sum:
               max_sum = current_sum
               start = temp_start
               end = i
       
       return max_sum, start, end
   ```

2. **Handle All Negative Numbers**:
   ```python
   def maxSubArrayAllNegative(nums):
       if not nums:
           return 0
       
       max_sum = float('-inf')
       current_sum = 0
       
       for num in nums:
           current_sum = max(num, current_sum + num)
           max_sum = max(max_sum, current_sum)
       
       return max_sum
   ```

3. **Maximum Sum Circular Subarray**:
   ```python
   def maxSubarraySumCircular(arr):
       def kadane_max(arr):
           max_kadane = arr[0]
           curr_max = arr[0]
           for i in range(1, len(arr)):
               curr_max = max(arr[i], curr_max + arr[i])
               max_kadane = max(max_kadane, curr_max)
           return max_kadane
       
       def kadane_min(arr):
           min_kadane = arr[0]
           curr_min = arr[0]
           for i in range(1, len(arr)):
               curr_min = min(arr[i], curr_min + arr[i])
               min_kadane = min(min_kadane, curr_min)
           return min_kadane
       
       # Case 1: Maximum subarray is non-circular
       max_kadane = kadane_max(arr)
       
       # Case 2: Maximum subarray is circular
       # Max circular = Total sum - Min subarray
       total_sum = sum(arr)
       min_kadane = kadane_min(arr)
       max_circular = total_sum - min_kadane
       
       # If all elements are negative, max_circular would be 0
       if max_circular == 0:
           return max_kadane
       
       return max(max_kadane, max_circular)
   ```

4. **Maximum Product Subarray** (Modified Kadane's):
   ```python
   def maxProduct(nums):
       max_so_far = min_so_far = result = nums[0]
       
       for i in range(1, len(nums)):
           # If current number is negative, swap max and min
           if nums[i] < 0:
               max_so_far, min_so_far = min_so_far, max_so_far
           
           max_so_far = max(nums[i], max_so_far * nums[i])
           min_so_far = min(nums[i], min_so_far * nums[i])
           
           result = max(result, max_so_far)
       
       return result
   ```

5. **Maximum Sum of k Subarrays** (Extended Kadane's):
   ```python
   def maxSumOfKSubarrays(arr, k):
       n = len(arr)
       # dp[i][j] = max sum using j subarrays from first i elements
       dp = [[-float('inf')] * (k + 1) for _ in range(n + 1)]
       
       # Base case: 0 subarrays from any elements = 0
       for i in range(n + 1):
           dp[i][0] = 0
       
       for i in range(1, n + 1):
           for j in range(1, min(i, k) + 1):
               # Don't include arr[i-1]
               dp[i][j] = dp[i-1][j]
               
               # Include arr[i-1] in a subarray
               max_sum = 0
               for l in range(i, 0, -1):
                   max_sum += arr[l-1]
                   if l-1 >= 0:  # Can form j-1 subarrays before position l-1
                       dp[i][j] = max(dp[i][j], dp[l-1][j-1] + max_sum)
       
       return dp[n][k]
   ```

**Applications of Kadane's Algorithm:**
- **Stock trading**: Maximum profit from single buy/sell
- **Image processing**: Finding brightest rectangular region
- **Bioinformatics**: Finding regions of DNA with specific properties
- **Load balancing**: Distributing tasks to minimize maximum load

**Time Complexity**: O(n)
**Space Complexity**: O(1)

**Key Insight**: Kadane's algorithm is a specific case of dynamic programming where we only need to remember the previous state, making it space-efficient.

### Questions from Top 150 (112–113)
- ❌ Maximum Subarray
- ❌ Maximum Sum Circular Subarray

---

## 18. Binary Search

### Core Theory

**Binary Search** is a divide-and-conquer algorithm that finds a target value in a sorted array by repeatedly dividing the search interval in half.

**Basic Binary Search Template**:
```python
def binarySearch(arr, target):
    left, right = 0, len(arr) - 1
    
    while left <= right:
        mid = left + (right - left) // 2  # Avoid overflow
        
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    
    return -1  # Not found
```

**Alternative Templates:**

1. **Left-biased** (Find first occurrence):
   ```python
   def findFirst(arr, target):
       left, right = 0, len(arr) - 1
       result = -1
       
       while left <= right:
           mid = left + (right - left) // 2
           
           if arr[mid] == target:
               result = mid
               right = mid - 1  # Continue searching left
           elif arr[mid] < target:
               left = mid + 1
           else:
               right = mid - 1
       
       return result
   ```

2. **Right-biased** (Find last occurrence):
   ```python
   def findLast(arr, target):
       left, right = 0, len(arr) - 1
       result = -1
       
       while left <= right:
           mid = left + (right - left) // 2
           
           if arr[mid] == target:
               result = mid
               left = mid + 1  # Continue searching right
           elif arr[mid] < target:
               left = mid + 1
           else:
               right = mid - 1
       
       return result
   ```

**Advanced Binary Search Patterns:**

1. **Search Insert Position**:
   ```python
   def searchInsert(nums, target):
       left, right = 0, len(nums) - 1
       
       while left <= right:
           mid = left + (right - left) // 2
           
           if nums[mid] == target:
               return mid
           elif nums[mid] < target:
               left = mid + 1
           else:
               right = mid - 1
       
       return left  # Insert position
   ```

2. **Search in Rotated Sorted Array**:
   ```python
   def search(nums, target):
       left, right = 0, len(nums) - 1
       
       while left <= right:
           mid = left + (right - left) // 2
           
           if nums[mid] == target:
               return mid
           
           # Check which half is sorted
           if nums[left] <= nums[mid]:  # Left half is sorted
               if nums[left] <= target < nums[mid]:
                   right = mid - 1
               else:
                   left = mid + 1
           else:  # Right half is sorted
               if nums[mid] < target <= nums[right]:
                   left = mid + 1
               else:
                   right = mid - 1
       
       return -1
   ```

3. **Find Minimum in Rotated Sorted Array**:
   ```python
   def findMin(nums):
       left, right = 0, len(nums) - 1
       
       while left < right:
           mid = left + (right - left) // 2
           
           if nums[mid] > nums[right]:
               left = mid + 1
           else:
               right = mid
       
       return nums[left]
   ```

4. **Find Peak Element**:
   ```python
   def findPeakElement(nums):
       left, right = 0, len(nums) - 1
       
       while left < right:
           mid = left + (right - left) // 2
           
           if nums[mid] > nums[mid + 1]:
               right = mid  # Peak is in left half (including mid)
           else:
               left = mid + 1  # Peak is in right half
       
       return left
   ```

5. **Search 2D Matrix**:
   ```python
   def searchMatrix(matrix, target):
       if not matrix or not matrix[0]:
           return False
       
       rows, cols = len(matrix), len(matrix[0])
       left, right = 0, rows * cols - 1
       
       while left <= right:
           mid = left + (right - left) // 2
           mid_value = matrix[mid // cols][mid % cols]
           
           if mid_value == target:
               return True
           elif mid_value < target:
               left = mid + 1
           else:
               right = mid - 1
       
       return False
   ```

6. **Median of Two Sorted Arrays**:
   ```python
   def findMedianSortedArrays(nums1, nums2):
       # Ensure nums1 is the smaller array
       if len(nums1) > len(nums2):
           nums1, nums2 = nums2, nums1
       
       m, n = len(nums1), len(nums2)
       left, right = 0, m
       
       while left <= right:
           partition1 = (left + right) // 2
           partition2 = (m + n + 1) // 2 - partition1
           
           # Edge cases
           max_left1 = float('-inf') if partition1 == 0 else nums1[partition1 - 1]
           min_right1 = float('inf') if partition1 == m else nums1[partition1]
           
           max_left2 = float('-inf') if partition2 == 0 else nums2[partition2 - 1]
           min_right2 = float('inf') if partition2 == n else nums2[partition2]
           
           if max_left1 <= min_right2 and max_left2 <= min_right1:
               # Found correct partition
               if (m + n) % 2 == 0:
                   return (max(max_left1, max_left2) + min(min_right1, min_right2)) / 2
               else:
                   return max(max_left1, max_left2)
           elif max_left1 > min_right2:
               right = partition1 - 1
           else:
               left = partition1 + 1
       
       raise ValueError("Arrays are not sorted")
   ```

**Binary Search on Answer** (When search space is not explicit array):
```python
def binarySearchOnAnswer(condition_func, left, right):
    """
    Find the smallest value x in [left, right] such that condition_func(x) is True
    """
    while left < right:
        mid = left + (right - left) // 2
        
        if condition_func(mid):
            right = mid
        else:
            left = mid + 1
    
    return left

# Example: Square root using binary search
def mySqrt(x):
    if x < 2:
        return x
    
    left, right = 2, x // 2
    
    while left <= right:
        mid = left + (right - left) // 2
        square = mid * mid
        
        if square == x:
            return mid
        elif square < x:
            left = mid + 1
        else:
            right = mid - 1
    
    return right  # Floor of square root
```

**Common Binary Search Mistakes:**
1. **Integer overflow**: Use `left + (right - left) // 2` instead of `(left + right) // 2`
2. **Infinite loop**: Be careful with `left = mid` vs `left = mid + 1`  
3. **Off-by-one errors**: Pay attention to `<` vs `<=` and boundary updates
4. **Not handling duplicates**: Decide whether you want first/last occurrence

**Time Complexity**: O(log n)
**Space Complexity**: O(1) for iterative, O(log n) for recursive

### Questions from Top 150 (114–120)
- ❌ Search Insert Position
- ❌ Search a 2D Matrix
- ❌ Find Peak Element
- ❌ Search in Rotated Sorted Array
- ❌ Find First and Last Position of Element in Sorted Array
- ❌ Find Minimum in Rotated Sorted Array
- ❌ Median of Two Sorted Arrays

---

## 19. Heap / Priority Queue

### Core Theory

A **Heap** is a complete binary tree that satisfies the heap property:
- **Max Heap**: Parent ≥ children (root is maximum)
- **Min Heap**: Parent ≤ children (root is minimum)

**Heap Properties:**
- **Complete binary tree**: All levels filled except possibly the last
- **Heap operations**: Insert, extract-min/max, peek in O(log n)
- **Heapify**: Build heap from array in O(n)
- **Array representation**: Parent of node i is at i//2, children at 2i and 2i+1

**Python Heap Implementation (Min Heap)**:
```python
import heapq

# Create empty heap
heap = []

# Create heap from list (heapify)
nums = [3, 1, 4, 1, 5, 9, 2, 6]
heapq.heapify(nums)  # O(n)

# Basic operations
heapq.heappush(heap, item)    # O(log n)
smallest = heapq.heappop(heap) # O(log n)
smallest = heap[0]             # O(1) - peek without removing

# For max heap, use negative values
max_heap = []
heapq.heappush(max_heap, -item)
largest = -heapq.heappop(max_heap)
```

**Custom Heap with Comparator**:
```python
import heapq

class Task:
    def __init__(self, priority, name):
        self.priority = priority
        self.name = name
    
    def __lt__(self, other):
        return self.priority < other.priority

# Usage
heap = []
heapq.heappush(heap, Task(3, "task1"))
heapq.heappush(heap, Task(1, "task2"))

highest_priority = heapq.heappop(heap)
```

**Key Heap Patterns:**

1. **Kth Largest Element**:
   ```python
   def findKthLargest(nums, k):
       # Method 1: Min heap of size k
       heap = []
       for num in nums:
           heapq.heappush(heap, num)
           if len(heap) > k:
               heapq.heappop(heap)
       return heap[0]
   
   # Method 2: Max heap, pop k-1 times
   def findKthLargestMaxHeap(nums, k):
       max_heap = [-num for num in nums]
       heapq.heapify(max_heap)
       
       for _ in range(k - 1):
           heapq.heappop(max_heap)
       
       return -max_heap[0]
   ```

2. **Merge k Sorted Lists**:
   ```python
   def mergeKLists(lists):
       heap = []
       
       # Initialize heap with first node from each list
       for i, lst in enumerate(lists):
           if lst:
               heapq.heappush(heap, (lst.val, i, lst))
       
       dummy = ListNode(0)
       current = dummy
       
       while heap:
           val, i, node = heapq.heappop(heap)
           current.next = node
           current = current.next
           
           if node.next:
               heapq.heappush(heap, (node.next.val, i, node.next))
       
       return dummy.next
   ```

3. **Top K Frequent Elements**:
   ```python
   def topKFrequent(nums, k):
       from collections import Counter
       
       count = Counter(nums)
       
       # Min heap of size k
       heap = []
       for num, freq in count.items():
           heapq.heappush(heap, (freq, num))
           if len(heap) > k:
               heapq.heappop(heap)
       
       return [num for freq, num in heap]
   ```

4. **Find Median from Data Stream**:
   ```python
   class MedianFinder:
       def __init__(self):
           self.small = []  # Max heap (store negative values)
           self.large = []  # Min heap
       
       def addNum(self, num):
           # Add to max heap (small)
           heapq.heappush(self.small, -num)
           
           # Balance: ensure all elements in small <= all in large
           if (self.small and self.large and 
               (-self.small[0]) > self.large[0]):
               val = -heapq.heappop(self.small)
               heapq.heappush(self.large, val)
           
           # Balance sizes
           if len(self.small) > len(self.large) + 1:
               val = -heapq.heappop(self.small)
               heapq.heappush(self.large, val)
           
           if len(self.large) > len(self.small) + 1:
               val = heapq.heappop(self.large)
               heapq.heappush(self.small, -val)
       
       def findMedian(self):
           if len(self.small) > len(self.large):
               return -self.small[0]
           elif len(self.large) > len(self.small):
               return self.large[0]
           else:
               return (-self.small[0] + self.large[0]) / 2.0
   ```

5. **Task Scheduler with Priority**:
   ```python
   def leastInterval(tasks, n):
       from collections import Counter
       
       count = Counter(tasks)
       max_heap = [-freq for freq in count.values()]
       heapq.heapify(max_heap)
       
       time = 0
       queue = []  # (frequency, available_time)
       
       while max_heap or queue:
           time += 1
           
           if max_heap:
               freq = heapq.heappop(max_heap)
               freq += 1  # Decrement frequency (was negative)
               
               if freq < 0:  # Still has tasks remaining
                   queue.append((freq, time + n))
           
           if queue and queue[0][1] == time:
               freq, _ = queue.pop(0)
               heapq.heappush(max_heap, freq)
       
       return time
   ```

6. **K Closest Points to Origin**:
   ```python
   def kClosest(points, k):
       # Max heap to keep k closest points
       heap = []
       
       for point in points:
           dist = point[0]**2 + point[1]**2
           
           if len(heap) < k:
               heapq.heappush(heap, (-dist, point))
           elif dist < -heap[0][0]:
               heapq.heapreplace(heap, (-dist, point))
       
       return [point for _, point in heap]
   ```

7. **IPO Problem** (Maximize capital):
   ```python
   def findMaximizedCapital(k, w, profits, capital):
       # Min heap for available projects (by capital requirement)
       available = [(capital[i], profits[i]) for i in range(len(profits))]
       heapq.heapify(available)
       
       # Max heap for profits of affordable projects
       affordable = []
       
       for _ in range(k):
           # Move all affordable projects to max heap
           while available and available[0][0] <= w:
               cap, profit = heapq.heappop(available)
               heapq.heappush(affordable, -profit)
           
           if not affordable:
               break
           
           # Take most profitable project
           w += -heapq.heappop(affordable)
       
       return w
   ```

**Heap Applications:**
- **Priority queues** (task scheduling)
- **Graph algorithms** (Dijkstra's, Prim's MST)
- **Streaming algorithms** (top-K, median)
- **Memory management** (LRU cache variants)
- **Load balancing**

**Time Complexities:**
- **Build heap**: O(n)
- **Insert**: O(log n)
- **Extract min/max**: O(log n)
- **Peek**: O(1)
- **Heapsort**: O(n log n)

### Questions from Top 150 (121–124)
- ❌ Kth Largest Element in an Array
- ❌ IPO
- ❌ Find K Pairs with Smallest Sums
- ❌ Find Median from Data Stream

---

## 20. Bit Manipulation

### Core Theory

**Bit Manipulation** operates on individual bits of binary numbers. Understanding binary representations and bitwise operations is crucial for optimization and certain problem types.

**Bitwise Operators:**
- **AND (&)**: Both bits must be 1 → Result is 1
- **OR (|)**: At least one bit is 1 → Result is 1  
- **XOR (^)**: Bits are different → Result is 1
- **NOT (~)**: Flips all bits
- **Left Shift (<<)**: Multiply by 2^n
- **Right Shift (>>)**: Divide by 2^n (arithmetic)

**Truth Tables:**
```
A | B | A&B | A|B | A^B
0 | 0 |  0  |  0  |  0
0 | 1 |  0  |  1  |  1
1 | 0 |  0  |  1  |  1
1 | 1 |  1  |  1  |  0
```

**Essential Bit Manipulation Tricks:**

1. **Check if bit is set**:
   ```python
   def is_bit_set(num, i):
       return (num & (1 << i)) != 0
   ```

2. **Set bit**:
   ```python
   def set_bit(num, i):
       return num | (1 << i)
   ```

3. **Clear bit**:
   ```python
   def clear_bit(num, i):
       return num & ~(1 << i)
   ```

4. **Toggle bit**:
   ```python
   def toggle_bit(num, i):
       return num ^ (1 << i)
   ```

5. **Check if power of 2**:
   ```python
   def is_power_of_2(n):
       return n > 0 and (n & (n - 1)) == 0
   ```

6. **Count set bits (Hamming Weight)**:
   ```python
   def hammingWeight(n):
       count = 0
       while n:
           count += 1
           n &= n - 1  # Brian Kernighan's algorithm
       return count
   
   # Alternative: built-in function
   def hammingWeightBuiltin(n):
       return bin(n).count('1')
   ```

**Key Bit Manipulation Problems:**

1. **Single Number** (XOR properties):
   ```python
   def singleNumber(nums):
       result = 0
       for num in nums:
           result ^= num  # XOR cancels out pairs
       return result
   ```

2. **Single Number II** (All appear 3 times except one):
   ```python
   def singleNumber2(nums):
       ones = twos = 0
       
       for num in nums:
           ones = (ones ^ num) & ~twos
           twos = (twos ^ num) & ~ones
       
       return ones
   
   # Alternative approach: bit counting
   def singleNumber2Alt(nums):
       result = 0
       for i in range(32):
           count = 0
           for num in nums:
               if num & (1 << i):
                   count += 1
           
           if count % 3 == 1:
               result |= (1 << i)
       
       # Handle negative numbers in Python
       if result >= 2**31:
           result -= 2**32
       
       return result
   ```

3. **Reverse Bits**:
   ```python
   def reverseBits(n):
       result = 0
       for _ in range(32):
           result = (result << 1) | (n & 1)
           n >>= 1
       return result
   
   # Optimized version using bit manipulation
   def reverseBitsOptimized(n):
       # Swap every two bits
       n = ((n & 0xAAAAAAAA) >> 1) | ((n & 0x55555555) << 1)
       # Swap every four bits  
       n = ((n & 0xCCCCCCCC) >> 2) | ((n & 0x33333333) << 2)
       # Swap every eight bits
       n = ((n & 0xF0F0F0F0) >> 4) | ((n & 0x0F0F0F0F) << 4)
       # Swap every sixteen bits
       n = ((n & 0xFF00FF00) >> 8) | ((n & 0x00FF00FF) << 8)
       # Swap every thirty-two bits
       n = (n >> 16) | (n << 16)
       
       return n & 0xFFFFFFFF
   ```

4. **Add Binary**:
   ```python
   def addBinary(a, b):
       result = []
       carry = 0
       i, j = len(a) - 1, len(b) - 1
       
       while i >= 0 or j >= 0 or carry:
           total = carry
           
           if i >= 0:
               total += int(a[i])
               i -= 1
           
           if j >= 0:
               total += int(b[j])
               j -= 1
           
           result.append(str(total % 2))
           carry = total // 2
       
       return ''.join(reversed(result))
   
   # Using built-in functions
   def addBinaryBuiltin(a, b):
       return bin(int(a, 2) + int(b, 2))[2:]
   ```

5. **Bitwise AND of Numbers Range**:
   ```python
   def rangeBitwiseAnd(left, right):
       shift = 0
       
       # Find common prefix
       while left != right:
           left >>= 1
           right >>= 1
           shift += 1
       
       return left << shift
   
   # Alternative approach
   def rangeBitwiseAndAlt(left, right):
       while left < right:
           right &= right - 1  # Remove rightmost set bit
       return right
   ```

6. **Maximum XOR of Two Numbers**:
   ```python
   def findMaximumXOR(nums):
       max_xor = 0
       mask = 0
       
       for i in range(31, -1, -1):
           mask |= (1 << i)  # Update mask to include current bit
           prefixes = {num & mask for num in nums}
           
           temp = max_xor | (1 << i)  # Try to set current bit in result
           
           # Check if temp can be formed by XOR of two prefixes
           for prefix in prefixes:
               if temp ^ prefix in prefixes:
                   max_xor = temp
                   break
       
       return max_xor
   ```

7. **Gray Code**:
   ```python
   def grayCode(n):
       result = []
       for i in range(1 << n):  # 2^n numbers
           result.append(i ^ (i >> 1))
       return result
   ```

8. **Subset Generation using Bits**:
   ```python
   def subsets(nums):
       result = []
       n = len(nums)
       
       for i in range(1 << n):  # 2^n subsets
           subset = []
           for j in range(n):
               if i & (1 << j):  # Check if j-th bit is set
                   subset.append(nums[j])
           result.append(subset)
       
       return result
   ```

**Advanced Bit Manipulation:**

1. **Fast Exponentiation**:
   ```python
   def myPow(x, n):
       if n < 0:
           x = 1 / x
           n = -n
       
       result = 1
       while n:
           if n & 1:  # If n is odd
               result *= x
           x *= x
           n >>= 1
       
       return result
   ```

2. **Bit Manipulation for DP**:
   ```python
   # Traveling Salesman Problem using bitmask DP
   def tsp(graph, n):
       # dp[mask][i] = minimum cost to visit all cities in mask, ending at i
       dp = {}
       
       def solve(mask, pos):
           if mask == (1 << n) - 1:  # All cities visited
               return graph[pos][0]  # Return to start
           
           if (mask, pos) in dp:
               return dp[(mask, pos)]
           
           ans = float('inf')
           for city in range(n):
               if mask & (1 << city) == 0:  # City not visited
                   new_mask = mask | (1 << city)
                   ans = min(ans, graph[pos][city] + solve(new_mask, city))
           
           dp[(mask, pos)] = ans
           return ans
       
       return solve(1, 0)  # Start from city 0
   ```

**Bit Manipulation Applications:**
- **Cryptography** (encryption algorithms)
- **Graphics** (color manipulation, image processing)
- **Network protocols** (packet headers, checksums)
- **Compiler optimization**
- **Database indexing** (bitmap indexes)
- **Game development** (flags, state management)

**Common Patterns:**
- **XOR for finding unique elements**
- **Bit counting for subset problems**
- **Bit masking for state representation**
- **Powers of 2 checks and operations**

### Questions from Top 150 (125–130)
- ❌ Add Binary
- ❌ Reverse Bits
- ❌ Number of 1 Bits
- ❌ Single Number
- ❌ Single Number II
- ❌ Bitwise AND of Numbers Range

---

## 21. Math

### Core Theory

Mathematical problems in programming interviews often involve **number theory**, **combinatorics**, **geometry**, and **algorithmic mathematics**. The key is to find patterns and use mathematical properties to optimize solutions.

**Key Mathematical Concepts:**

1. **Modular Arithmetic**:
   - (a + b) % m = ((a % m) + (b % m)) % m
   - (a × b) % m = ((a % m) × (b % m)) % m
   - (a - b) % m = ((a % m) - (b % m) + m) % m

2. **Prime Numbers**:
   - Sieve of Eratosthenes for generating primes
   - Primality testing algorithms
   - Prime factorization

3. **GCD and LCM**:
   - Euclidean algorithm for GCD
   - LCM(a,b) = (a × b) / GCD(a,b)

4. **Fast Exponentiation**:
   - Computing a^n in O(log n) time

**Mathematical Problem Patterns:**

1. **Palindrome Number** (Without String Conversion):
   ```python
   def isPalindrome(x):
       if x < 0:
           return False
       
       original = x
       reversed_num = 0
       
       while x > 0:
           reversed_num = reversed_num * 10 + x % 10
           x //= 10
       
       return original == reversed_num
   
   # Optimized: reverse only half
   def isPalindromeOptimized(x):
       if x < 0 or (x % 10 == 0 and x != 0):
           return False
       
       reversed_half = 0
       while x > reversed_half:
           reversed_half = reversed_half * 10 + x % 10
           x //= 10
       
       # For odd digits: x == reversed_half // 10
       return x == reversed_half or x == reversed_half // 10
   ```

2. **Plus One** (Array Addition):
   ```python
   def plusOne(digits):
       for i in range(len(digits) - 1, -1, -1):
           if digits[i] < 9:
               digits[i] += 1
               return digits
           digits[i] = 0
       
       # All digits were 9, need extra digit
       return [1] + digits
   ```

3. **Factorial Trailing Zeroes**:
   ```python
   def trailingZeroes(n):
       # Count factors of 5 (factors of 2 are always more abundant)
       count = 0
       while n >= 5:
           n //= 5
           count += n
       return count
   
   # Alternative recursive approach
   def trailingZeroesRecursive(n):
       if n < 5:
           return 0
       return n // 5 + trailingZeroesRecursive(n // 5)
   ```

4. **Integer Square Root**:
   ```python
   def mySqrt(x):
       if x < 2:
           return x
       
       left, right = 2, x // 2
       
       while left <= right:
           mid = left + (right - left) // 2
           square = mid * mid
           
           if square == x:
               return mid
           elif square < x:
               left = mid + 1
           else:
               right = mid - 1
       
       return right  # Floor of square root
   
   # Newton's method (faster convergence)
   def mySqrtNewton(x):
       if x < 2:
           return x
       
       r = x
       while r * r > x:
           r = (r + x // r) // 2
       
       return r
   ```

5. **Fast Power (Exponentiation by Squaring)**:
   ```python
   def myPow(x, n):
       if n == 0:
           return 1
       if n < 0:
           x = 1 / x
           n = -n
       
       result = 1
       while n > 0:
           if n % 2 == 1:  # Odd power
               result *= x
           x *= x
           n //= 2
       
       return result
   
   # Recursive approach
   def myPowRecursive(x, n):
       if n == 0:
           return 1
       if n < 0:
           return 1 / myPowRecursive(x, -n)
       if n % 2 == 0:
           half = myPowRecursive(x, n // 2)
           return half * half
       else:
           return x * myPowRecursive(x, n - 1)
   ```

6. **Max Points on a Line** (Computational Geometry):
   ```python
   def maxPoints(points):
       if len(points) <= 2:
           return len(points)
       
       def gcd(a, b):
           while b:
               a, b = b, a % b
           return a
       
       max_points = 0
       
       for i in range(len(points)):
           slopes = {}
           duplicate = 1
           local_max = 0
           
           for j in range(i + 1, len(points)):
               dx = points[j][0] - points[i][0]
               dy = points[j][1] - points[i][1]
               
               if dx == 0 and dy == 0:  # Duplicate point
                   duplicate += 1
                   continue
               
               # Normalize slope to avoid floating point issues
               g = gcd(dx, dy)
               dx //= g
               dy //= g
               
               # Ensure consistent representation
               if dx < 0 or (dx == 0 and dy < 0):
                   dx, dy = -dx, -dy
               
               slope = (dx, dy)
               slopes[slope] = slopes.get(slope, 0) + 1
               local_max = max(local_max, slopes[slope])
           
           max_points = max(max_points, local_max + duplicate)
       
       return max_points
   ```

7. **Roman Numeral Conversion**:
   ```python
   def intToRoman(num):
       values = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
       symbols = ["M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"]
       
       result = ""
       for i in range(len(values)):
           count = num // values[i]
           if count:
               result += symbols[i] * count
               num %= values[i]
       
       return result
   
   def romanToInt(s):
       roman_to_int = {'I': 1, 'V': 5, 'X': 10, 'L': 50, 'C': 100, 'D': 500, 'M': 1000}
       
       total = 0
       prev_value = 0
       
       for char in s:
           current_value = roman_to_int[char]
           
           if current_value > prev_value:
               # Subtract twice the previous value (once to undo, once for subtraction)
               total += current_value - 2 * prev_value
           else:
               total += current_value
           
           prev_value = current_value
       
       return total
   ```

8. **Happy Number**:
   ```python
   def isHappy(n):
       def get_next(num):
           total = 0
           while num > 0:
               digit = num % 10
               total += digit * digit
               num //= 10
           return total
       
       seen = set()
       while n != 1 and n not in seen:
           seen.add(n)
           n = get_next(n)
       
       return n == 1
   
   # Floyd's cycle detection
   def isHappyFloyd(n):
       def get_next(num):
           total = 0
           while num > 0:
               digit = num % 10
               total += digit * digit
               num //= 10
           return total
       
       slow = fast = n
       
       while True:
           slow = get_next(slow)
           fast = get_next(get_next(fast))
           
           if fast == 1:
               return True
           if slow == fast:
               return False
   ```

**Advanced Mathematical Algorithms:**

1. **Sieve of Eratosthenes**:
   ```python
   def sieveOfEratosthenes(n):
       primes = [True] * (n + 1)
       primes[0] = primes[1] = False
       
       for i in range(2, int(n**0.5) + 1):
           if primes[i]:
               for j in range(i*i, n + 1, i):
                   primes[j] = False
       
       return [i for i in range(n + 1) if primes[i]]
   ```

2. **Greatest Common Divisor** (Euclidean Algorithm):
   ```python
   def gcd(a, b):
       while b:
           a, b = b, a % b
       return a
   
   # Extended Euclidean Algorithm
   def extendedGcd(a, b):
       if a == 0:
           return b, 0, 1
       
       gcd, x1, y1 = extendedGcd(b % a, a)
       x = y1 - (b // a) * x1
       y = x1
       
       return gcd, x, y
   ```

3. **Matrix Multiplication** (for Fibonacci, etc.):
   ```python
   def matrixMultiply(A, B):
       rows_A, cols_A = len(A), len(A[0])
       rows_B, cols_B = len(B), len(B[0])
       
       C = [[0] * cols_B for _ in range(rows_A)]
       
       for i in range(rows_A):
           for j in range(cols_B):
               for k in range(cols_A):
                   C[i][j] += A[i][k] * B[k][j]
       
       return C
   
   # Fast Fibonacci using matrix exponentiation
   def fibonacci(n):
       if n <= 1:
           return n
       
       def matrix_power(matrix, power):
           if power == 1:
               return matrix
           if power % 2 == 0:
               half = matrix_power(matrix, power // 2)
               return matrixMultiply(half, half)
           else:
               return matrixMultiply(matrix, matrix_power(matrix, power - 1))
       
       base_matrix = [[1, 1], [1, 0]]
       result_matrix = matrix_power(base_matrix, n)
       
       return result_matrix[0][1]
   ```

**Mathematical Optimization Techniques:**
- **Precomputation**: Store results of expensive calculations
- **Mathematical identities**: Use algebraic properties to simplify
- **Modular arithmetic**: Prevent integer overflow
- **Approximation algorithms**: When exact solutions are too expensive

### Questions from Top 150 (131–136)
- ❌ Palindrome Number
- ❌ Plus One
- ❌ Factorial Trailing Zeroes
- ❌ Sqrt(x)
- ❌ Pow(x, n)
- ❌ Max Points on a Line

---

## 22. 1D Dynamic Programming

### Core Theory

**Dynamic Programming (DP)** solves problems by breaking them down into simpler subproblems and storing results to avoid redundant calculations. DP is applicable when a problem has:

1. **Optimal Substructure**: Optimal solution contains optimal solutions to subproblems
2. **Overlapping Subproblems**: Same subproblems are solved multiple times

**DP Approaches:**
- **Top-Down (Memoization)**: Recursive with caching
- **Bottom-Up (Tabulation)**: Iterative, building from smaller problems

**Basic DP Template**:
```python
# Top-down approach
def dp_recursive(n, memo={}):
    if base_case:
        return base_value
    
    if n in memo:
        return memo[n]
    
    # Recurrence relation
    memo[n] = combine(dp_recursive(n-1, memo), dp_recursive(n-2, memo))
    return memo[n]

# Bottom-up approach  
def dp_iterative(n):
    if base_case:
        return base_value
    
    dp = [0] * (n + 1)
    dp[0], dp[1] = base_values
    
    for i in range(2, n + 1):
        dp[i] = combine(dp[i-1], dp[i-2])
    
    return dp[n]
```

**Classic 1D DP Problems:**

1. **Fibonacci Numbers**:
   ```python
   # Basic recursion (exponential time)
   def fib_recursive(n):
       if n <= 1:
           return n
       return fib_recursive(n-1) + fib_recursive(n-2)
   
   # Memoization (top-down)
   def fib_memo(n, memo={}):
       if n <= 1:
           return n
       if n in memo:
           return memo[n]
       memo[n] = fib_memo(n-1, memo) + fib_memo(n-2, memo)
       return memo[n]
   
   # Tabulation (bottom-up)
   def fib_dp(n):
       if n <= 1:
           return n
       
       dp = [0] * (n + 1)
       dp[1] = 1
       
       for i in range(2, n + 1):
           dp[i] = dp[i-1] + dp[i-2]
       
       return dp[n]
   
   # Space optimized
   def fib_optimized(n):
       if n <= 1:
           return n
       
       prev2, prev1 = 0, 1
       for _ in range(2, n + 1):
           current = prev1 + prev2
           prev2, prev1 = prev1, current
       
       return prev1
   ```

2. **Climbing Stairs**:
   ```python
   def climbStairs(n):
       if n <= 2:
           return n
       
       dp = [0] * (n + 1)
       dp[1], dp[2] = 1, 2
       
       for i in range(3, n + 1):
           dp[i] = dp[i-1] + dp[i-2]
       
       return dp[n]
   
   # Space optimized
   def climbStairsOptimized(n):
       if n <= 2:
           return n
       
       first, second = 1, 2
       for _ in range(3, n + 1):
           first, second = second, first + second
       
       return second
   ```

3. **House Robber**:
   ```python
   def rob(nums):
       if not nums:
           return 0
       if len(nums) <= 2:
           return max(nums)
       
       dp = [0] * len(nums)
       dp[0] = nums[0]
       dp[1] = max(nums[0], nums[1])
       
       for i in range(2, len(nums)):
           dp[i] = max(dp[i-1], dp[i-2] + nums[i])
       
       return dp[-1]
   
   # Space optimized
   def robOptimized(nums):
       if not nums:
           return 0
       
       prev2 = prev1 = 0
       for num in nums:
           current = max(prev1, prev2 + num)
           prev2, prev1 = prev1, current
       
       return prev1
   ```

4. **Coin Change** (Minimum coins):
   ```python
   def coinChange(coins, amount):
       dp = [float('inf')] * (amount + 1)
       dp[0] = 0
       
       for coin in coins:
           for i in range(coin, amount + 1):
               dp[i] = min(dp[i], dp[i - coin] + 1)
       
       return dp[amount] if dp[amount] != float('inf') else -1
   
   # Alternative: BFS approach
   def coinChangeBFS(coins, amount):
       if amount == 0:
           return 0
       
       from collections import deque
       queue = deque([amount])
       visited = set([amount])
       level = 0
       
       while queue:
           level += 1
           for _ in range(len(queue)):
               current = queue.popleft()
               
               for coin in coins:
                   next_amount = current - coin
                   
                   if next_amount == 0:
                       return level
                   
                   if next_amount > 0 and next_amount not in visited:
                       visited.add(next_amount)
                       queue.append(next_amount)
       
       return -1
   ```

5. **Word Break**:
   ```python
   def wordBreak(s, wordDict):
       word_set = set(wordDict)
       dp = [False] * (len(s) + 1)
       dp[0] = True  # Empty string can be segmented
       
       for i in range(1, len(s) + 1):
           for j in range(i):
               if dp[j] and s[j:i] in word_set:
                   dp[i] = True
                   break
       
       return dp[len(s)]
   
   # Alternative: memoization with suffix checking
   def wordBreakMemo(s, wordDict, memo={}):
       if s in memo:
           return memo[s]
       
       if not s:
           return True
       
       for word in wordDict:
           if s.startswith(word) and wordBreakMemo(s[len(word):], wordDict, memo):
               memo[s] = True
               return True
       
       memo[s] = False
       return False
   ```

6. **Longest Increasing Subsequence**:
   ```python
   # O(n²) DP solution
   def lengthOfLIS(nums):
       if not nums:
           return 0
       
       dp = [1] * len(nums)
       
       for i in range(1, len(nums)):
           for j in range(i):
               if nums[j] < nums[i]:
                   dp[i] = max(dp[i], dp[j] + 1)
       
       return max(dp)
   
   # O(n log n) solution using binary search
   def lengthOfLISOptimized(nums):
       if not nums:
           return 0
       
       tails = []
       
       for num in nums:
           left, right = 0, len(tails)
           
           # Binary search for insertion point
           while left < right:
               mid = (left + right) // 2
               if tails[mid] < num:
                   left = mid + 1
               else:
                   right = mid
           
           if left == len(tails):
               tails.append(num)
           else:
               tails[left] = num
       
       return len(tails)
   ```

7. **Maximum Subarray** (Kadane's Algorithm):
   ```python
   def maxSubArray(nums):
       max_sum = current_sum = nums[0]
       
       for i in range(1, len(nums)):
           current_sum = max(nums[i], current_sum + nums[i])
           max_sum = max(max_sum, current_sum)
       
       return max_sum
   
   # DP array approach
   def maxSubArrayDP(nums):
       if not nums:
           return 0
       
       dp = [0] * len(nums)
       dp[0] = nums[0]
       max_sum = nums[0]
       
       for i in range(1, len(nums)):
           dp[i] = max(nums[i], dp[i-1] + nums[i])
           max_sum = max(max_sum, dp[i])
       
       return max_sum
   ```

**DP Optimization Techniques:**

1. **Space Optimization**:
   ```python
   # Instead of storing entire DP array
   def optimized_dp(n):
       if n <= 1:
           return n
       
       # Only keep track of necessary previous values
       prev2, prev1 = 0, 1
       for _ in range(2, n + 1):
           current = prev1 + prev2
           prev2, prev1 = prev1, current
       
       return prev1
   ```

2. **Rolling Array**:
   ```python
   # When you need more than 2 previous values
   def rolling_array_dp(arr, k):
       dp = [0] * k  # Keep only k previous values
       
       for i in range(len(arr)):
           new_val = compute_new_value(dp, arr[i])
           dp[i % k] = new_val
       
       return dp[len(arr) % k]
   ```

**DP Pattern Recognition:**
- **Fibonacci-like**: f(n) depends on f(n-1), f(n-2), ...
- **Decision problems**: At each step, choose optimal action
- **Optimization problems**: Minimize/maximize some value
- **Counting problems**: How many ways to achieve something

**Time Complexity**: Usually O(n) to O(n²) for 1D problems
**Space Complexity**: Can often be optimized to O(1)

### Questions from Top 150 (137–141)
- ❌ Climbing Stairs
- ❌ House Robber
- ❌ Word Break
- ❌ Coin Change
- ❌ Longest Increasing Subsequence

---

## 23. Multidimensional DP

### Core Theory

**Multidimensional Dynamic Programming** extends DP to problems with multiple variables/dimensions. Common patterns include 2D grids, string comparisons, and problems with multiple constraints.

**2D DP Template**:
```python
def solve_2d_dp(grid):
    rows, cols = len(grid), len(grid[0])
    dp = [[0] * cols for _ in range(rows)]
    
    # Initialize base cases
    dp[0][0] = base_value
    
    # Fill first row and column
    for i in range(1, rows):
        dp[i][0] = compute_value(dp[i-1][0], grid[i][0])
    for j in range(1, cols):
        dp[0][j] = compute_value(dp[0][j-1], grid[0][j])
    
    # Fill rest of the table
    for i in range(1, rows):
        for j in range(1, cols):
            dp[i][j] = optimal_choice(dp[i-1][j], dp[i][j-1], grid[i][j])
    
    return dp[rows-1][cols-1]
```

**Classic 2D DP Problems:**

1. **Unique Paths**:
   ```python
   def uniquePaths(m, n):
       dp = [[1] * n for _ in range(m)]
       
       for i in range(1, m):
           for j in range(1, n):
               dp[i][j] = dp[i-1][j] + dp[i][j-1]
       
       return dp[m-1][n-1]
   
   # Space optimized
   def uniquePathsOptimized(m, n):
       dp = [1] * n
       
       for i in range(1, m):
           for j in range(1, n):
               dp[j] += dp[j-1]
       
       return dp[n-1]
   
   # Mathematical approach
   def uniquePathsMath(m, n):
       # C(m+n-2, m-1) = (m+n-2)! / ((m-1)! * (n-1)!)
       from math import comb
       return comb(m + n - 2, m - 1)
   ```

2. **Unique Paths II** (With obstacles):
   ```python
   def uniquePathsWithObstacles(obstacleGrid):
       rows, cols = len(obstacleGrid), len(obstacleGrid[0])
       
       if obstacleGrid[0][0] == 1:
           return 0
       
       dp = [[0] * cols for _ in range(rows)]
       dp[0][0] = 1
       
       # Fill first row
       for j in range(1, cols):
           dp[0][j] = dp[0][j-1] if obstacleGrid[0][j] == 0 else 0
       
       # Fill first column
       for i in range(1, rows):
           dp[i][0] = dp[i-1][0] if obstacleGrid[i][0] == 0 else 0
       
       # Fill rest of the table
       for i in range(1, rows):
           for j in range(1, cols):
               if obstacleGrid[i][j] == 0:
                   dp[i][j] = dp[i-1][j] + dp[i][j-1]
       
       return dp[rows-1][cols-1]
   ```

3. **Minimum Path Sum**:
   ```python
   def minPathSum(grid):
       rows, cols = len(grid), len(grid[0])
       dp = [[0] * cols for _ in range(rows)]
       
       dp[0][0] = grid[0][0]
       
       # Fill first row
       for j in range(1, cols):
           dp[0][j] = dp[0][j-1] + grid[0][j]
       
       # Fill first column
       for i in range(1, rows):
           dp[i][0] = dp[i-1][0] + grid[i][0]
       
       # Fill rest of the table
       for i in range(1, rows):
           for j in range(1, cols):
               dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j]
       
       return dp[rows-1][cols-1]
   
   # Space optimized
   def minPathSumOptimized(grid):
       rows, cols = len(grid), len(grid[0])
       dp = grid[0][:]  # Copy first row
       
       # Process first row
       for j in range(1, cols):
           dp[j] += dp[j-1]
       
       # Process remaining rows
       for i in range(1, rows):
           dp[0] += grid[i][0]  # First column
           
           for j in range(1, cols):
               dp[j] = min(dp[j], dp[j-1]) + grid[i][j]
       
       return dp[cols-1]
   ```

4. **Triangle** (Minimum path sum):
   ```python
   def minimumTotal(triangle):
       n = len(triangle)
       dp = triangle[-1][:]  # Start from bottom row
       
       # Work backwards
       for i in range(n - 2, -1, -1):
           for j in range(len(triangle[i])):
               dp[j] = min(dp[j], dp[j + 1]) + triangle[i][j]
       
       return dp[0]
   
   # Top-down approach
   def minimumTotalTopDown(triangle):
       n = len(triangle)
       dp = [[0] * len(row) for row in triangle]
       
       dp[0][0] = triangle[0][0]
       
       for i in range(1, n):
           # First element in each row
           dp[i][0] = dp[i-1][0] + triangle[i][0]
           
           # Last element in each row
           dp[i][i] = dp[i-1][i-1] + triangle[i][i]
           
           # Middle elements
           for j in range(1, i):
               dp[i][j] = min(dp[i-1][j-1], dp[i-1][j]) + triangle[i][j]
       
       return min(dp[n-1])
   ```

**String DP Problems:**

5. **Edit Distance** (Levenshtein Distance):
   ```python
   def minDistance(word1, word2):
       m, n = len(word1), len(word2)
       dp = [[0] * (n + 1) for _ in range(m + 1)]
       
       # Initialize base cases
       for i in range(m + 1):
           dp[i][0] = i  # Delete all characters from word1
       for j in range(n + 1):
           dp[0][j] = j  # Insert all characters of word2
       
       for i in range(1, m + 1):
           for j in range(1, n + 1):
               if word1[i-1] == word2[j-1]:
                   dp[i][j] = dp[i-1][j-1]  # No operation needed
               else:
                   dp[i][j] = min(
                       dp[i-1][j] + 1,      # Delete
                       dp[i][j-1] + 1,      # Insert
                       dp[i-1][j-1] + 1     # Replace
                   )
       
       return dp[m][n]
   ```

6. **Longest Common Subsequence**:
   ```python
   def longestCommonSubsequence(text1, text2):
       m, n = len(text1), len(text2)
       dp = [[0] * (n + 1) for _ in range(m + 1)]
       
       for i in range(1, m + 1):
           for j in range(1, n + 1):
               if text1[i-1] == text2[j-1]:
                   dp[i][j] = dp[i-1][j-1] + 1
               else:
                   dp[i][j] = max(dp[i-1][j], dp[i][j-1])
       
       return dp[m][n]
   
   # Space optimized
   def longestCommonSubsequenceOptimized(text1, text2):
       m, n = len(text1), len(text2)
       if m < n:
           text1, text2 = text2, text1
           m, n = n, m
       
       prev = [0] * (n + 1)
       curr = [0] * (n + 1)
       
       for i in range(1, m + 1):
           for j in range(1, n + 1):
               if text1[i-1] == text2[j-1]:
                   curr[j] = prev[j-1] + 1
               else:
                   curr[j] = max(prev[j], curr[j-1])
           prev, curr = curr, prev
       
       return prev[n]
   ```

7. **Longest Palindromic Substring**:
   ```python
   def longestPalindrome(s):
       n = len(s)
       if n <= 1:
           return s
       
       dp = [[False] * n for _ in range(n)]
       start = 0
       max_len = 1
       
       # Single characters are palindromes
       for i in range(n):
           dp[i][i] = True
       
       # Check for palindromes of length 2
       for i in range(n - 1):
           if s[i] == s[i + 1]:
               dp[i][i + 1] = True
               start = i
               max_len = 2
       
       # Check for palindromes of length 3 and more
       for length in range(3, n + 1):
           for i in range(n - length + 1):
               j = i + length - 1
               
               if s[i] == s[j] and dp[i + 1][j - 1]:
                   dp[i][j] = True
                   start = i
                   max_len = length
       
       return s[start:start + max_len]
   
   # Expand around center approach (more efficient)
   def longestPalindromeExpand(s):
       if not s:
           return ""
       
       start = end = 0
       
       for i in range(len(s)):
           len1 = expand_around_center(s, i, i)      # Odd length
           len2 = expand_around_center(s, i, i + 1)  # Even length
           max_len = max(len1, len2)
           
           if max_len > end - start:
               start = i - (max_len - 1) // 2
               end = i + max_len // 2
       
       return s[start:end + 1]
   
   def expand_around_center(s, left, right):
       while left >= 0 and right < len(s) and s[left] == s[right]:
           left -= 1
           right += 1
       return right - left - 1
   ```

8. **Interleaving String**:
   ```python
   def isInterleave(s1, s2, s3):
       m, n = len(s1), len(s2)
       
       if m + n != len(s3):
           return False
       
       dp = [[False] * (n + 1) for _ in range(m + 1)]
       dp[0][0] = True
       
       # Fill first row (using only s2)
       for j in range(1, n + 1):
           dp[0][j] = dp[0][j-1] and s2[j-1] == s3[j-1]
       
       # Fill first column (using only s1)
       for i in range(1, m + 1):
           dp[i][0] = dp[i-1][0] and s1[i-1] == s3[i-1]
       
       # Fill rest of the table
       for i in range(1, m + 1):
           for j in range(1, n + 1):
               dp[i][j] = ((dp[i-1][j] and s1[i-1] == s3[i+j-1]) or
                          (dp[i][j-1] and s2[j-1] == s3[i+j-1]))
       
       return dp[m][n]
   ```

**Stock Trading DP:**

9. **Best Time to Buy and Sell Stock III** (At most 2 transactions):
   ```python
   def maxProfit(prices):
       if not prices:
           return 0
       
       # State: [transaction][holding_stock]
       # transaction: 0 or 1 (completed transactions)
       # holding_stock: 0 (not holding) or 1 (holding)
       
       buy1 = buy2 = -prices[0]  # Cost of buying
       sell1 = sell2 = 0         # Profit from selling
       
       for price in prices:
           sell2 = max(sell2, buy2 + price)   # Sell second stock
           buy2 = max(buy2, sell1 - price)    # Buy second stock
           sell1 = max(sell1, buy1 + price)   # Sell first stock
           buy1 = max(buy1, -price)           # Buy first stock
       
       return sell2
   ```

10. **Best Time to Buy and Sell Stock IV** (At most k transactions):
    ```python
    def maxProfit(k, prices):
        if not prices or k == 0:
            return 0
        
        n = len(prices)
        
        # If k >= n//2, we can do as many transactions as we want
        if k >= n // 2:
            return sum(max(prices[i+1] - prices[i], 0) for i in range(n-1))
        
        # buy[i] = max profit after at most i+1 transactions, holding stock
        # sell[i] = max profit after at most i+1 transactions, not holding stock
        buy = [-prices[0]] * k
        sell = [0] * k
        
        for price in prices:
            for j in range(k-1, -1, -1):
                sell[j] = max(sell[j], buy[j] + price)
                buy[j] = max(buy[j], (sell[j-1] if j > 0 else 0) - price)
        
        return sell[k-1]
    ```

11. **Maximal Square**:
    ```python
    def maximalSquare(matrix):
        if not matrix or not matrix[0]:
            return 0
        
        rows, cols = len(matrix), len(matrix[0])
        dp = [[0] * cols for _ in range(rows)]
        max_side = 0
        
        for i in range(rows):
            for j in range(cols):
                if matrix[i][j] == '1':
                    if i == 0 or j == 0:
                        dp[i][j] = 1
                    else:
                        dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
                    
                    max_side = max(max_side, dp[i][j])
        
        return max_side * max_side
    
    # Space optimized
    def maximalSquareOptimized(matrix):
        if not matrix or not matrix[0]:
            return 0
        
        rows, cols = len(matrix), len(matrix[0])
        dp = [0] * cols
        max_side = 0
        prev = 0
        
        for i in range(rows):
            for j in range(cols):
                temp = dp[j]
                
                if matrix[i][j] == '1':
                    dp[j] = min(dp[j], dp[j-1] if j > 0 else 0, prev) + 1
                    max_side = max(max_side, dp[j])
                else:
                    dp[j] = 0
                
                prev = temp
        
        return max_side * max_side
    ```

**Advanced DP Optimization:**

1. **Space Optimization Patterns**:
   ```python
   # Rolling array for 2D DP
   def optimize_2d_to_1d(grid):
       rows, cols = len(grid), len(grid[0])
       dp = [0] * cols
       
       for i in range(rows):
           new_dp = [0] * cols
           for j in range(cols):
               new_dp[j] = compute_value(dp[j], dp[j-1] if j > 0 else 0)
           dp = new_dp
       
       return dp[cols-1]
   ```

2. **State Machine DP**:
   ```python
   def state_machine_dp(arr):
       # Multiple states at each position
       state1 = state2 = state3 = initial_values
       
       for item in arr:
           new_state1 = transition_function(state1, state2, state3, item)
           new_state2 = transition_function(state1, state2, state3, item)
           new_state3 = transition_function(state1, state2, state3, item)
           
           state1, state2, state3 = new_state1, new_state2, new_state3
       
       return optimal_state(state1, state2, state3)
   ```

**Time Complexity**: Usually O(n²) to O(n³) for 2D problems
**Space Complexity**: Often can be optimized from O(n²) to O(n)

### Questions from Top 150 (142–150)
- ❌ Triangle
- ❌ Minimum Path Sum
- ❌ Unique Paths II
- ❌ Longest Palindromic Substring
- ❌ Interleaving String
- ❌ Edit Distance
- ❌ Best Time to Buy and Sell Stock III
- ❌ Best Time to Buy and Sell Stock IV
- ❌ Maximal Square

---

## Study Tips and Recommendations

### Suggested Study Order

1. **Foundation (Weeks 1-2)**: Arrays & Strings, Hash Map, Two Pointers
2. **Linear Structures (Weeks 3-4)**: Sliding Window, Stack, Linked List
3. **Search & Sort (Week 5)**: Binary Search, Basic Sorting
4. **Trees (Weeks 6-7)**: Binary Tree DFS, BFS, BST
5. **Graphs (Week 8)**: Graph DFS/BFS, Union-Find
6. **Advanced Structures (Week 9)**: Heap, Trie
7. **Problem-Solving Techniques (Weeks 10-11)**: Backtracking, Divide & Conquer
8. **Dynamic Programming (Weeks 12-14)**: 1D DP, then 2D DP
9. **Specialized Topics (Week 15)**: Bit Manipulation, Math, Intervals

### Practice Strategy

1. **Read theory** for each topic before attempting problems
2. **Solve problems** in order within each topic
3. **Time yourself** after you're comfortable with the patterns
4. **Review and optimize** solutions for better time/space complexity
5. **Implement** solutions in your preferred language
6. **Practice explaining** your approach out loud

### Key Resources

- **LeetCode Top 150**: https://leetcode.com/studyplan/top-interview-150/
- **Time Complexity Reference**: Know Big O for all major algorithms
- **Programming Language Documentation**: Master your chosen language's standard library
- **Mock Interviews**: Practice with peers or platforms like Pramp, interviewing.io

### Final Notes

- **Consistency is key**: 30 minutes daily is better than 5 hours once a week
- **Focus on understanding patterns**: Don't just memorize solutions
- **Practice coding by hand**: Many interviews still use whiteboards
- **Master the fundamentals**: Strong basics will help with any problem variation
- **Stay calm under pressure**: Practice mock interviews to build confidence

Good luck with your interview preparation! 🚀

---

*Last updated: April 2026*
