# LeetCode Top 150 Interview Questions - Complete Algorithm Theory Guide
*The Ultimate Comprehensive Reference for Technical Interview Preparation*

## Table of Contents
1. [Arrays & Strings](#1-arrays--strings) - Fundamental data manipulation
2. [Two Pointers](#2-two-pointers) - Optimization technique for linear structures
3. [Sliding Window](#3-sliding-window) - Subarray/substring optimization
4. [Matrix](#4-matrix) - 2D array manipulation and traversal patterns
5. [Hash Map / Hash Set](#5-hash-map--hash-set) - Fast lookup and frequency counting
6. [Intervals](#6-intervals) - Range and scheduling problems
7. [Stack](#7-stack) - LIFO structure for parsing and backtracking
8. [Linked List](#8-linked-list) - Pointer manipulation and in-place operations
9. [Binary Tree (General / DFS)](#9-binary-tree-general--dfs) - Tree traversal and recursion
10. [Binary Tree BFS](#10-binary-tree-bfs) - Level-order processing
11. [Binary Search Tree](#11-binary-search-tree) - Ordered tree operations
12. [Graph (General / DFS & Union-Find)](#12-graph-general--dfs--union-find) - Graph traversal and connectivity
13. [Graph BFS](#13-graph-bfs) - Shortest path in unweighted graphs
14. [Trie](#14-trie) - Prefix tree for string processing
15. [Backtracking](#15-backtracking) - Exhaustive search with pruning
16. [Divide & Conquer](#16-divide--conquer) - Problem decomposition strategy
17. [Kadane's Algorithm](#17-kadanes-algorithm) - Maximum subarray problems
18. [Binary Search](#18-binary-search) - Logarithmic search in sorted spaces
19. [Heap / Priority Queue](#19-heap--priority-queue) - Efficient min/max operations
20. [Bit Manipulation](#20-bit-manipulation) - Low-level optimization techniques
21. [Math](#21-math) - Mathematical algorithms and number theory
22. [1D Dynamic Programming](#22-1d-dynamic-programming) - Sequential decision problems
23. [Multidimensional DP](#23-multidimensional-dp) - Complex state space optimization

---

## Introduction

This comprehensive guide covers all algorithmic concepts needed for the LeetCode Top 150 Interview Questions. Each section provides:

- **Deep theoretical foundations** with mathematical analysis
- **Multiple implementation approaches** with trade-offs
- **Optimization techniques** and space-time complexity analysis
- **Common patterns and templates** for quick recognition
- **Edge cases and gotchas** frequently tested in interviews
- **Real-world applications** and problem variations

**Study Approach**: Master the theory first, understand the patterns, then practice implementation. Each topic builds upon previous concepts, so follow the recommended order for optimal learning.

---

## 1. Arrays & Strings

### Core Theory & Memory Model

**Arrays** are the most fundamental data structure in computer science. They store elements in **contiguous memory locations**, which provides several key advantages:

1. **Cache Efficiency**: Adjacent elements are loaded together into CPU cache
2. **Predictable Memory Access**: Address calculation is O(1): `base_address + index * element_size`
3. **Hardware Optimization**: Modern CPUs can prefetch sequential memory efficiently

**Memory Layout**:
```
Array: [10, 20, 30, 40, 50]
Memory: [10][20][30][40][50]  ← Contiguous blocks
Index:   0   1   2   3   4
Address: 1000 1004 1008 1012 1016 (assuming 4-byte integers)
```

**Fundamental Operations Analysis**:
- **Access**: O(1) - Direct address calculation
- **Search**: O(n) unsorted, O(log n) sorted with binary search
- **Insertion**: O(n) - May require shifting elements
- **Deletion**: O(n) - May require shifting elements
- **Sorting**: O(n log n) optimal comparison-based, O(n) with constraints

### In-Place Operations Mastery

**In-place algorithms** are crucial for memory-constrained environments and demonstrate deep understanding of space optimization.

**Core In-Place Patterns**:

1. **Two-Pointer Technique (Read/Write Pointers)**:
   ```python
   def removeDuplicates(nums):
       if not nums:
           return 0
       
       write_index = 1  # Position to write next unique element
       
       for read_index in range(1, len(nums)):
           if nums[read_index] != nums[read_index - 1]:
               nums[write_index] = nums[read_index]
               write_index += 1
       
       return write_index
   ```
   **Why it works**: We maintain invariant that `nums[0:write_index]` contains unique elements.

2. **Cyclical Replacement**:
   ```python
   def rotate(nums, k):
       """Rotate array to right by k steps using cyclical replacement"""
       n = len(nums)
       k %= n
       if k == 0:
           return
       
       count = 0  # Number of elements moved
       start = 0
       
       while count < n:
           current = start
           prev = nums[start]
           
           # Follow the cycle
           while True:
               next_idx = (current + k) % n
               nums[next_idx], prev = prev, nums[next_idx]
               current = next_idx
               count += 1
               
               # Completed a cycle
               if start == current:
                   break
           
           start += 1
   ```

3. **Dutch National Flag Algorithm** (3-way partitioning):
   ```python
   def sortColors(nums):
       """Sort 0s, 1s, 2s in-place using Dutch National Flag"""
       low = current = 0
       high = len(nums) - 1
       
       while current <= high:
           if nums[current] == 0:
               nums[low], nums[current] = nums[current], nums[low]
               low += 1
               current += 1
           elif nums[current] == 1:
               current += 1
           else:  # nums[current] == 2
               nums[current], nums[high] = nums[high], nums[current]
               high -= 1
               # Don't increment current - need to check swapped element
   ```

### Advanced Array Algorithms

1. **Boyer-Moore Majority Vote Algorithm**:
   ```python
   def majorityElement(nums):
       """Find majority element (appears > n/2 times)"""
       candidate = count = 0
       
       # Phase 1: Find candidate
       for num in nums:
           if count == 0:
               candidate = num
           count += 1 if num == candidate else -1
       
       # Phase 2: Verify candidate (if not guaranteed to exist)
       # count = sum(1 for num in nums if num == candidate)
       # return candidate if count > len(nums) // 2 else None
       
       return candidate  # LeetCode guarantees majority exists
   ```
   **Mathematical Proof**: If majority element exists, it survives the voting process because it appears more than all other elements combined.

   **Extension - Find All Majority Elements (> n/3)**:
   ```python
   def majorityElementII(nums):
       """Find all elements appearing > n/3 times (at most 2 such elements)"""
       if not nums:
           return []
       
       candidate1 = candidate2 = None
       count1 = count2 = 0
       
       # Phase 1: Find up to 2 candidates
       for num in nums:
           if candidate1 is not None and num == candidate1:
               count1 += 1
           elif candidate2 is not None and num == candidate2:
               count2 += 1
           elif count1 == 0:
               candidate1, count1 = num, 1
           elif count2 == 0:
               candidate2, count2 = num, 1
           else:
               count1 -= 1
               count2 -= 1
       
       # Phase 2: Verify candidates
       result = []
       threshold = len(nums) // 3
       
       for candidate in [candidate1, candidate2]:
           if candidate is not None and nums.count(candidate) > threshold:
               result.append(candidate)
       
       return result
   ```

2. **Array Rotation Algorithms**:

   **Method 1: Reverse Algorithm** (Most Elegant):
   ```python
   def rotate(nums, k):
       """Rotate array right by k positions"""
       n = len(nums)
       k %= n
       
       def reverse(arr, start, end):
           while start < end:
               arr[start], arr[end] = arr[end], arr[start]
               start += 1
               end -= 1
       
       # Example: [1,2,3,4,5,6,7], k=3
       reverse(nums, 0, n - 1)      # [7,6,5,4,3,2,1]
       reverse(nums, 0, k - 1)      # [5,6,7,4,3,2,1]
       reverse(nums, k, n - 1)      # [5,6,7,1,2,3,4]
   ```

   **Method 2: Block Swapping** (Memory Efficient for Large Arrays):
   ```python
   def rotateBlockSwap(nums, k):
       """Rotate using block swapping algorithm"""
       def gcd(a, b):
           while b:
               a, b = b, a % b
           return a
       
       n = len(nums)
       k %= n
       if k == 0:
           return
       
       def reverse(arr, i, j):
           while i < j:
               arr[i], arr[j] = arr[j], arr[i]
               i += 1
               j -= 1
       
       def rotateUtil(arr, start, d, n):
           if d == 0 or d == n:
               return
           
           if d == n - d:  # Equal halves
               for i in range(d):
                   arr[start + i], arr[start + n - d + i] = arr[start + n - d + i], arr[start + i]
               return
           
           if d < n - d:  # Left part is smaller
               for i in range(d):
                   arr[start + i], arr[start + n - d + i] = arr[start + n - d + i], arr[start + i]
               rotateUtil(arr, start, d, n - d)
           else:  # Right part is smaller
               for i in range(n - d):
                   arr[start + i], arr[start + d + i] = arr[start + d + i], arr[start + i]
               rotateUtil(arr, start + n - d, 2 * d - n, d)
       
       rotateUtil(nums, 0, n - k, n)
   ```

3. **Prefix/Suffix Product Techniques**:
   ```python
   def productExceptSelf(nums):
       """Calculate product of all elements except self"""
       n = len(nums)
       result = [1] * n
       
       # Build prefix products
       for i in range(1, n):
           result[i] = result[i-1] * nums[i-1]
       
       # Multiply with suffix products
       suffix = 1
       for i in range(n - 1, -1, -1):
           result[i] *= suffix
           suffix *= nums[i]
       
       return result
   
   # Extension: Handle zeros and optimize for multiple queries
   def productQueries(nums, queries):
       """Handle multiple product-except-self queries efficiently"""
       n = len(nums)
       prefix = [1] * (n + 1)
       suffix = [1] * (n + 1)
       
       # Build prefix and suffix products
       for i in range(n):
           prefix[i + 1] = prefix[i] * nums[i]
       
       for i in range(n - 1, -1, -1):
           suffix[i] = suffix[i + 1] * nums[i]
       
       results = []
       for start, end in queries:
           # Product of nums[start:end+1] excluding nums[exclude]
           left_product = prefix[start]
           right_product = suffix[end + 1]
           total_product = prefix[end + 1] // prefix[start] if prefix[start] != 0 else 0
           # Handle specific exclusion logic based on query type
           results.append(left_product * right_product)
       
       return results
   ```

### String Processing Algorithms

**String Matching Algorithms**:

1. **KMP (Knuth-Morris-Pratt) Algorithm**:
   ```python
   def strStr(haystack, needle):
       """Find first occurrence of needle in haystack using KMP"""
       if not needle:
           return 0
       
       # Build failure function (LPS array)
       def buildLPS(pattern):
           lps = [0] * len(pattern)
           length = 0  # Length of previous longest prefix suffix
           i = 1
           
           while i < len(pattern):
               if pattern[i] == pattern[length]:
                   length += 1
                   lps[i] = length
                   i += 1
               else:
                   if length != 0:
                       length = lps[length - 1]
                   else:
                       lps[i] = 0
                       i += 1
           
           return lps
       
       lps = buildLPS(needle)
       i = j = 0  # i for haystack, j for needle
       
       while i < len(haystack):
           if haystack[i] == needle[j]:
               i += 1
               j += 1
           
           if j == len(needle):
               return i - j  # Found match
           elif i < len(haystack) and haystack[i] != needle[j]:
               if j != 0:
                   j = lps[j - 1]
               else:
                   i += 1
       
       return -1
   ```

2. **Rabin-Karp Algorithm** (Rolling Hash):
   ```python
   def rabinKarp(text, pattern):
       """String matching using rolling hash"""
       if len(pattern) > len(text):
           return -1
       
       BASE = 256
       PRIME = 101
       
       pattern_len = len(pattern)
       text_len = len(text)
       
       pattern_hash = 0
       text_hash = 0
       h = 1  # hash multiplier
       
       # Calculate hash multiplier: BASE^(pattern_len-1) % PRIME
       for i in range(pattern_len - 1):
           h = (h * BASE) % PRIME
       
       # Calculate initial hash values
       for i in range(pattern_len):
           pattern_hash = (BASE * pattern_hash + ord(pattern[i])) % PRIME
           text_hash = (BASE * text_hash + ord(text[i])) % PRIME
       
       # Slide pattern over text
       for i in range(text_len - pattern_len + 1):
           if pattern_hash == text_hash:
               # Hash match - verify character by character
               if text[i:i + pattern_len] == pattern:
                   return i
           
           # Calculate rolling hash for next window
           if i < text_len - pattern_len:
               text_hash = (BASE * (text_hash - ord(text[i]) * h) + ord(text[i + pattern_len])) % PRIME
               if text_hash < 0:
                   text_hash += PRIME
       
       return -1
   ```

**Advanced String Manipulation**:

1. **Text Justification** (Complex String Formatting):
   ```python
   def fullJustify(words, maxWidth):
       """Justify text to given width"""
       result = []
       current_line = []
       current_length = 0
       
       for word in words:
           # Check if adding word exceeds maxWidth
           if current_length + len(word) + len(current_line) > maxWidth:
               # Justify current line
               if len(current_line) == 1:
                   # Single word - left justify
                   line = current_line[0] + ' ' * (maxWidth - len(current_line[0]))
               else:
                   # Multiple words - distribute spaces evenly
                   total_spaces = maxWidth - current_length
                   gaps = len(current_line) - 1
                   
                   if gaps == 0:
                       line = current_line[0] + ' ' * total_spaces
                   else:
                       space_per_gap = total_spaces // gaps
                       extra_spaces = total_spaces % gaps
                       
                       line = ''
                       for i, w in enumerate(current_line[:-1]):
                           line += w + ' ' * space_per_gap
                           if i < extra_spaces:
                               line += ' '
                       line += current_line[-1]
               
               result.append(line)
               current_line = [word]
               current_length = len(word)
           else:
               current_line.append(word)
               current_length += len(word)
       
       # Handle last line (left justified)
       if current_line:
           last_line = ' '.join(current_line)
           last_line += ' ' * (maxWidth - len(last_line))
           result.append(last_line)
       
       return result
   ```

2. **Palindrome Processing**:
   ```python
   def palindromeUtilities():
       """Collection of palindrome-related algorithms"""
       
       def is_palindrome_ignore_case(s):
           """Check palindrome ignoring case and non-alphanumeric"""
           left, right = 0, len(s) - 1
           
           while left < right:
               while left < right and not s[left].isalnum():
                   left += 1
               while left < right and not s[right].isalnum():
                   right -= 1
               
               if s[left].lower() != s[right].lower():
                   return False
               
               left += 1
               right -= 1
           
           return True
       
       def longest_palindrome_manacher(s):
           """Find longest palindrome using Manacher's algorithm - O(n)"""
           # Transform string to handle even-length palindromes
           T = '#'.join('^{}$'.format(s))
           n = len(T)
           P = [0] * n  # P[i] = length of palindrome centered at i
           C = R = 0    # Center and Right boundary of current palindrome
           
           for i in range(1, n - 1):
               # Mirror of i with respect to center C
               i_mirror = 2 * C - i
               
               if R > i:
                   P[i] = min(R - i, P[i_mirror])
               
               # Try to expand palindrome centered at i
               try:
                   while T[i + (1 + P[i])] == T[i - (1 + P[i])]:
                       P[i] += 1
               except IndexError:
                   pass
               
               # If palindrome centered at i extends past R, adjust C and R
               if i + P[i] > R:
                   C, R = i, i + P[i]
           
           # Find longest palindrome
           max_len = 0
           center_index = 0
           for i in range(1, n - 1):
               if P[i] > max_len:
                   max_len = P[i]
                   center_index = i
           
           start = (center_index - max_len) // 2
           return s[start:start + max_len]
   ```

### Advanced Array Problems & Patterns

**Water Trapping & Container Problems**:

1. **Trapping Rain Water** (Multiple Approaches):
   ```python
   def trap(height):
       """Trap rainwater - multiple solution approaches"""
       
       # Approach 1: Two Pointers (Most Efficient)
       def trap_two_pointers(height):
           if len(height) < 3:
               return 0
           
           left, right = 0, len(height) - 1
           left_max = right_max = water = 0
           
           while left < right:
               if height[left] < height[right]:
                   if height[left] >= left_max:
                       left_max = height[left]
                   else:
                       water += left_max - height[left]
                   left += 1
               else:
                   if height[right] >= right_max:
                       right_max = height[right]
                   else:
                       water += right_max - height[right]
                   right -= 1
           
           return water
       
       # Approach 2: Precomputed Arrays
       def trap_precomputed(height):
           n = len(height)
           if n < 3:
               return 0
           
           left_max = [0] * n
           right_max = [0] * n
           
           left_max[0] = height[0]
           for i in range(1, n):
               left_max[i] = max(left_max[i-1], height[i])
           
           right_max[n-1] = height[n-1]
           for i in range(n-2, -1, -1):
               right_max[i] = max(right_max[i+1], height[i])
           
           water = 0
           for i in range(n):
               water += min(left_max[i], right_max[i]) - height[i]
           
           return water
       
       # Approach 3: Stack-based
       def trap_stack(height):
           stack = []
           water = 0
           
           for i, h in enumerate(height):
               while stack and h > height[stack[-1]]:
                   top = stack.pop()
                   if not stack:
                       break
                   
                   distance = i - stack[-1] - 1
                   bounded_height = min(h, height[stack[-1]]) - height[top]
                   water += distance * bounded_height
               
               stack.append(i)
           
           return water
       
       return trap_two_pointers(height)
   ```

2. **Container With Most Water**:
   ```python
   def maxArea(height):
       """Find container that holds most water"""
       left, right = 0, len(height) - 1
       max_water = 0
       
       while left < right:
           # Calculate current water
           width = right - left
           water = width * min(height[left], height[right])
           max_water = max(max_water, water)
           
           # Move pointer with smaller height
           # (moving the taller one can't improve the result)
           if height[left] < height[right]:
               left += 1
           else:
               right -= 1
       
       return max_water
   ```

**Greedy Algorithms on Arrays**:

1. **Jump Game Variations**:
   ```python
   def jumpGameSolutions():
       """Collection of jump game algorithms"""
       
       def canJump(nums):
           """Jump Game I: Can reach last index?"""
           max_reach = 0
           
           for i, jump in enumerate(nums):
               if i > max_reach:
                   return False
               max_reach = max(max_reach, i + jump)
               if max_reach >= len(nums) - 1:
                   return True
           
           return max_reach >= len(nums) - 1
       
       def jump(nums):
           """Jump Game II: Minimum jumps to reach end"""
           if len(nums) <= 1:
               return 0
           
           jumps = 0
           current_end = 0      # End of current jump
           farthest = 0         # Farthest we can reach
           
           for i in range(len(nums) - 1):  # Don't need to jump from last position
               farthest = max(farthest, i + nums[i])
               
               if i == current_end:     # Need to make a jump
                   jumps += 1
                   current_end = farthest
           
           return jumps
       
       def jumpIII(arr, start, d):
           """Jump Game III: Reach index with value 0"""
           # BFS approach for complex jump rules
           from collections import deque
           
           if arr[start] == 0:
               return True
           
           queue = deque([start])
           visited = set([start])
           
           while queue:
               pos = queue.popleft()
               
               # Can jump left or right by arr[pos] steps
               for next_pos in [pos - arr[pos], pos + arr[pos]]:
                   if (0 <= next_pos < len(arr) and 
                       next_pos not in visited):
                       
                       if arr[next_pos] == 0:
                           return True
                       
                       visited.add(next_pos)
                       queue.append(next_pos)
           
           return False
   ```

2. **Gas Station Problem**:
   ```python
   def canCompleteCircuit(gas, cost):
       """Find starting gas station to complete circular tour"""
       
       # Check if solution exists
       if sum(gas) < sum(cost):
           return -1
       
       tank = 0
       start = 0
       
       for i in range(len(gas)):
           tank += gas[i] - cost[i]
           
           # If tank becomes negative, can't start from current start
           if tank < 0:
               tank = 0
               start = i + 1
       
       return start
   
   # Alternative: Mathematical proof approach
   def canCompleteCircuitMath(gas, cost):
       """Mathematical approach with detailed analysis"""
       total_tank = 0
       current_tank = 0
       starting_station = 0
       
       for i in range(len(gas)):
           total_tank += gas[i] - cost[i]
           current_tank += gas[i] - cost[i]
           
           # If current tank is negative, reset
           if current_tank < 0:
               starting_station = i + 1
               current_tank = 0
       
       return starting_station if total_tank >= 0 else -1
   ```

### Memory Optimization Techniques

**Space-Efficient Algorithms**:

1. **In-Place Array Modification**:
   ```python
   def inPlaceTechniques():
       """Advanced in-place modification patterns"""
       
       def wiggleSort(nums):
           """Sort array in wiggle pattern: nums[0] < nums[1] > nums[2] < nums[3]..."""
           for i in range(len(nums)):
               if ((i % 2 == 1 and nums[i-1] > nums[i]) or
                   (i % 2 == 0 and i > 0 and nums[i-1] < nums[i])):
                   nums[i-1], nums[i] = nums[i], nums[i-1]
       
       def nextPermutation(nums):
           """Generate next lexicographically greater permutation in-place"""
           # Find longest non-increasing suffix
           i = len(nums) - 2
           while i >= 0 and nums[i] >= nums[i + 1]:
               i -= 1
           
           if i == -1:  # nums is the last permutation
               nums.reverse()
               return
           
           # Find rightmost character that exceeds pivot
           j = len(nums) - 1
           while nums[j] <= nums[i]:
               j -= 1
           
           nums[i], nums[j] = nums[j], nums[i]
           
           # Reverse suffix
           nums[i + 1:] = reversed(nums[i + 1:])
       
       def moveZeroes(nums):
           """Move all zeros to end while maintaining relative order"""
           write_index = 0
           
           # Move all non-zero elements to front
           for read_index in range(len(nums)):
               if nums[read_index] != 0:
                   nums[write_index] = nums[read_index]
                   write_index += 1
           
           # Fill remaining positions with zeros
           while write_index < len(nums):
               nums[write_index] = 0
               write_index += 1
   ```

**Cache-Efficient Algorithms**:
- **Spatial Locality**: Process adjacent elements together
- **Temporal Locality**: Reuse recently accessed data
- **Loop Blocking**: Break large loops into cache-sized chunks

### Time Complexity Analysis Deep Dive

**Amortized Analysis Examples**:

1. **Dynamic Array Growth**:
   - Individual insertion: O(1) amortized, O(n) worst case
   - Total cost for n insertions: O(n)
   - Amortized cost per insertion: O(1)

2. **Array Rotation Cost Analysis**:
   - Reverse method: O(n) time, O(1) space
   - Cyclic replacement: O(n) time, O(1) space
   - Block swap: O(n) time, O(1) space, cache-friendly

### Questions from Top 150 (1–24)
**Completed ✅ / Remaining ❌ Breakdown:**
- ✅ Merge Sorted Array - Two pointers, in-place merging
- ✅ Remove Element - Two pointers, in-place removal
- ✅ Remove Duplicates from Sorted Array (I, II) - Multiple pointer techniques
- ✅ Majority Element - Boyer-Moore voting algorithm
- ✅ Rotate Array - Three reversal technique
- ✅ Best Time to Buy and Sell Stock (I, II) - Greedy and DP approaches
- ✅ Jump Game (I, II) - Greedy algorithms, BFS variations
- ✅ H-Index - Sorting and counting, bucket sort optimization
- ✅ Insert Delete GetRandom O(1) - Hash table + dynamic array hybrid
- ✅ Product of Array Except Self - Prefix/suffix products
- ✅ Gas Station - Greedy algorithm with mathematical proof
- ✅ Candy - Two-pass greedy algorithm
- ✅ Trapping Rain Water - Two pointers, stack, precomputation
- ✅ Roman to Integer / Integer to Roman - State machine, greedy conversion
- ✅ Length of Last Word - String parsing
- ✅ Longest Common Prefix - Vertical/horizontal scanning, divide & conquer
- ✅ Reverse Words in a String - Two pointers, in-place reversal
- ✅ Zigzag Conversion - Mathematical pattern recognition
- ✅ Find the Index of the First Occurrence in a String - KMP, Rabin-Karp
- ✅ Text Justification - Complex string formatting, greedy space distribution

### Practice Strategy for Arrays & Strings
1. **Master the fundamentals**: Two pointers, in-place operations
2. **Pattern recognition**: Identify when to use specific algorithms
3. **Optimization focus**: Always consider space complexity improvements
4. **Edge cases**: Empty arrays, single elements, all same elements
5. **Mathematical insights**: Use mathematical properties for elegant solutions

---

## 2. Two Pointers

### Core Theory & Mathematical Foundation

The **Two Pointers technique** is a powerful algorithmic paradigm that reduces time complexity from O(n²) to O(n) for many problems by using two indices to traverse data structures intelligently.

**Mathematical Principle**: Instead of checking all possible pairs (i, j) which gives us n(n-1)/2 = O(n²) combinations, two pointers eliminate impossible combinations through logical deduction, typically reducing to O(n) single passes.

**Why Two Pointers Work - Mathematical Proof**:
Consider the Two Sum problem on a sorted array:
- If `nums[left] + nums[right] > target`, then `nums[left] + nums[k] > target` for all k > right
- This eliminates `right - left` combinations in one step
- Similarly, if `nums[left] + nums[right] < target`, we eliminate `right - left` combinations
- Total eliminations: n + (n-1) + (n-2) + ... + 1 = O(n)

### Types of Two Pointers Algorithms

**1. Opposite Direction (Convergent Pointers)**

Used when we need to find pairs or check symmetry properties.

```python
def twoPointerConvergent():
    """Master template for convergent two pointers"""
    
    def twoSum(nums, target):
        """Two Sum on sorted array - foundational example"""
        left, right = 0, len(nums) - 1
        
        while left < right:
            current_sum = nums[left] + nums[right]
            if current_sum == target:
                return [left, right]
            elif current_sum < target:
                left += 1  # Need larger sum
            else:
                right -= 1  # Need smaller sum
        
        return []  # Not found
    
    def threeSumClosest(nums, target):
        """3Sum Closest - advanced convergent pointers"""
        nums.sort()
        n = len(nums)
        closest_sum = float('inf')
        
        for i in range(n - 2):
            left, right = i + 1, n - 1
            
            while left < right:
                current_sum = nums[i] + nums[left] + nums[right]
                
                # Update closest if current is closer to target
                if abs(current_sum - target) < abs(closest_sum - target):
                    closest_sum = current_sum
                
                if current_sum < target:
                    left += 1
                elif current_sum > target:
                    right -= 1
                else:
                    return current_sum  # Exact match
        
        return closest_sum
    
    def fourSum(nums, target):
        """4Sum - extending to four pointers"""
        nums.sort()
        n = len(nums)
        result = []
        
        for i in range(n - 3):
            # Skip duplicates for first number
            if i > 0 and nums[i] == nums[i - 1]:
                continue
                
            for j in range(i + 1, n - 2):
                # Skip duplicates for second number
                if j > i + 1 and nums[j] == nums[j - 1]:
                    continue
                
                left, right = j + 1, n - 1
                
                while left < right:
                    current_sum = nums[i] + nums[j] + nums[left] + nums[right]
                    
                    if current_sum == target:
                        result.append([nums[i], nums[j], nums[left], nums[right]])
                        
                        # Skip duplicates for third and fourth numbers
                        while left < right and nums[left] == nums[left + 1]:
                            left += 1
                        while left < right and nums[right] == nums[right - 1]:
                            right -= 1
                        
                        left += 1
                        right -= 1
                    elif current_sum < target:
                        left += 1
                    else:
                        right -= 1
        
        return result
```

**2. Same Direction (Fast & Slow Pointers)**

Used for in-place modifications and cycle detection.

```python
def twoPointerSameDirection():
    """Master template for same-direction pointers"""
    
    def removeDuplicates(nums):
        """Remove duplicates in-place from sorted array"""
        if len(nums) <= 1:
            return len(nums)
        
        slow = 1  # Position to place next unique element
        
        for fast in range(1, len(nums)):
            if nums[fast] != nums[fast - 1]:
                nums[slow] = nums[fast]
                slow += 1
        
        return slow
    
    def removeDuplicatesII(nums):
        """Allow at most 2 duplicates"""
        if len(nums) <= 2:
            return len(nums)
        
        slow = 2  # Position to place next element
        
        for fast in range(2, len(nums)):
            # Compare with element 2 positions back
            if nums[fast] != nums[slow - 2]:
                nums[slow] = nums[fast]
                slow += 1
        
        return slow
    
    def moveZeroes(nums):
        """Move zeros to end maintaining relative order"""
        slow = 0  # Position to place next non-zero element
        
        # Move all non-zero elements to front
        for fast in range(len(nums)):
            if nums[fast] != 0:
                nums[slow], nums[fast] = nums[fast], nums[slow]
                slow += 1
```

**3. Sliding Window with Two Pointers**

Bridge between two pointers and sliding window techniques.

```python
def twoPointerWindow():
    """Two pointers forming dynamic windows"""
    
    def lengthOfLongestSubstring(s):
        """Longest substring without repeating characters"""
        char_index = {}
        left = max_length = 0
        
        for right, char in enumerate(s):
            if char in char_index and char_index[char] >= left:
                left = char_index[char] + 1
            
            char_index[char] = right
            max_length = max(max_length, right - left + 1)
        
        return max_length
    
    def minWindow(s, t):
        """Minimum window substring containing all characters of t"""
        if not s or not t:
            return ""
        
        # Dictionary to keep count of characters in t
        dict_t = {}
        for char in t:
            dict_t[char] = dict_t.get(char, 0) + 1
        
        required = len(dict_t)
        formed = 0
        window_counts = {}
        
        left = right = 0
        ans = float("inf"), None, None
        
        while right < len(s):
            # Add character from right to window
            character = s[right]
            window_counts[character] = window_counts.get(character, 0) + 1
            
            # Check if this character's frequency matches requirement
            if character in dict_t and window_counts[character] == dict_t[character]:
                formed += 1
            
            # Try to contract window until it's no longer valid
            while left <= right and formed == required:
                character = s[left]
                
                # Save the smallest window
                if right - left + 1 < ans[0]:
                    ans = (right - left + 1, left, right)
                
                # Remove leftmost character
                window_counts[character] -= 1
                if character in dict_t and window_counts[character] < dict_t[character]:
                    formed -= 1
                
                left += 1
            
            right += 1
        
        return "" if ans[0] == float("inf") else s[ans[1]:ans[2] + 1]
```

### Advanced Two Pointers Patterns

**1. Cycle Detection (Floyd's Tortoise and Hare)**

```python
def floydCycleDetection():
    """Advanced cycle detection algorithms"""
    
    def hasCycle(head):
        """Detect cycle in linked list"""
        if not head or not head.next:
            return False
        
        slow = fast = head
        
        # Phase 1: Detect if cycle exists
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            
            if slow == fast:
                return True
        
        return False
    
    def detectCycle(head):
        """Find the start of cycle in linked list"""
        if not head or not head.next:
            return None
        
        # Phase 1: Detect cycle
        slow = fast = head
        
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            
            if slow == fast:
                break
        else:
            return None  # No cycle
        
        # Phase 2: Find cycle start
        # Mathematical proof: distance from head to cycle start
        # equals distance from meeting point to cycle start
        finder = head
        while finder != slow:
            finder = finder.next
            slow = slow.next
        
        return slow
    
    def findDuplicate(nums):
        """Find duplicate number using cycle detection"""
        # Treat array as linked list where nums[i] points to nums[nums[i]]
        slow = fast = nums[0]
        
        # Phase 1: Find intersection point
        while True:
            slow = nums[slow]
            fast = nums[nums[fast]]
            if slow == fast:
                break
        
        # Phase 2: Find entrance to cycle
        slow = nums[0]
        while slow != fast:
            slow = nums[slow]
            fast = nums[fast]
        
        return slow
```

**2. Palindrome Algorithms**

```python
def palindromeAlgorithms():
    """Advanced palindrome detection and processing"""
    
    def isPalindrome(s):
        """Check if string is palindrome (ignore case and non-alphanumeric)"""
        left, right = 0, len(s) - 1
        
        while left < right:
            # Skip non-alphanumeric characters
            while left < right and not s[left].isalnum():
                left += 1
            while left < right and not s[right].isalnum():
                right -= 1
            
            # Compare characters (case insensitive)
            if s[left].lower() != s[right].lower():
                return False
            
            left += 1
            right -= 1
        
        return True
    
    def longestPalindrome(s):
        """Find longest palindromic substring using expand around center"""
        if not s:
            return ""
        
        start = end = 0
        
        for i in range(len(s)):
            # Check for odd-length palindromes (center at i)
            len1 = expandAroundCenter(s, i, i)
            # Check for even-length palindromes (center between i and i+1)
            len2 = expandAroundCenter(s, i, i + 1)
            
            max_len = max(len1, len2)
            if max_len > end - start:
                start = i - (max_len - 1) // 2
                end = i + max_len // 2
        
        return s[start:end + 1]
    
    def expandAroundCenter(s, left, right):
        """Helper function to expand around center"""
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return right - left - 1
    
    def countPalindromicSubstrings(s):
        """Count all palindromic substrings"""
        count = 0
        
        for i in range(len(s)):
            # Count odd-length palindromes
            count += expandAndCount(s, i, i)
            # Count even-length palindromes
            count += expandAndCount(s, i, i + 1)
        
        return count
    
    def expandAndCount(s, left, right):
        """Count palindromes by expanding around center"""
        count = 0
        while left >= 0 and right < len(s) and s[left] == s[right]:
            count += 1
            left -= 1
            right += 1
        return count
```

**3. Partition and Sorting Problems**

```python
def partitionAlgorithms():
    """Advanced partitioning using two pointers"""
    
    def sortColors(nums):
        """Sort 0s, 1s, 2s in-place (Dutch National Flag)"""
        low = mid = 0
        high = len(nums) - 1
        
        while mid <= high:
            if nums[mid] == 0:
                nums[low], nums[mid] = nums[mid], nums[low]
                low += 1
                mid += 1
            elif nums[mid] == 1:
                mid += 1
            else:  # nums[mid] == 2
                nums[mid], nums[high] = nums[high], nums[mid]
                high -= 1
                # Don't increment mid - need to check swapped element
    
    def partitionArray(nums, k):
        """Partition array around value k"""
        left = 0
        right = len(nums) - 1
        
        while left <= right:
            while left <= right and nums[left] < k:
                left += 1
            while left <= right and nums[right] > k:
                right -= 1
            
            if left <= right:
                nums[left], nums[right] = nums[right], nums[left]
                left += 1
                right -= 1
        
        return left  # Return partition point
    
    def quickSelect(nums, k):
        """Find kth smallest element using partition"""
        def partition(left, right):
            pivot = nums[right]
            i = left
            
            for j in range(left, right):
                if nums[j] <= pivot:
                    nums[i], nums[j] = nums[j], nums[i]
                    i += 1
            
            nums[i], nums[right] = nums[right], nums[i]
            return i
        
        def select(left, right, k):
            if left == right:
                return nums[left]
            
            pivot_index = partition(left, right)
            
            if k == pivot_index:
                return nums[k]
            elif k < pivot_index:
                return select(left, pivot_index - 1, k)
            else:
                return select(pivot_index + 1, right, k)
        
        return select(0, len(nums) - 1, k)
```

### Geometric Two Pointers

```python
def geometricTwoPointers():
    """Two pointers for geometric problems"""
    
    def containerWithMostWater(height):
        """Find container that holds most water"""
        left, right = 0, len(height) - 1
        max_area = 0
        
        while left < right:
            # Calculate current area
            width = right - left
            current_height = min(height[left], height[right])
            area = width * current_height
            max_area = max(max_area, area)
            
            # Move pointer with smaller height
            # Proof: Moving the taller pointer cannot increase area
            # because width decreases and height is limited by shorter line
            if height[left] < height[right]:
                left += 1
            else:
                right -= 1
        
        return max_area
    
    def maxAreaOfIsland(grid):
        """Find maximum area of island using DFS with implicit pointers"""
        if not grid:
            return 0
        
        def dfs(i, j):
            if (i < 0 or i >= len(grid) or 
                j < 0 or j >= len(grid[0]) or 
                grid[i][j] != 1):
                return 0
            
            grid[i][j] = 0  # Mark as visited
            
            # Explore all 4 directions
            return (1 + dfs(i+1, j) + dfs(i-1, j) + 
                    dfs(i, j+1) + dfs(i, j-1))
        
        max_area = 0
        for i in range(len(grid)):
            for j in range(len(grid[0])):
                if grid[i][j] == 1:
                    max_area = max(max_area, dfs(i, j))
        
        return max_area
```

### Two Pointers Optimization Techniques

**1. Early Termination Strategies**
```python
def optimizedTwoPointers():
    """Advanced optimization techniques"""
    
    def threeSumWithEarlyTermination(nums, target):
        """3Sum with multiple optimizations"""
        nums.sort()
        n = len(nums)
        result = []
        
        for i in range(n - 2):
            # Skip duplicates for first number
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            
            # Early termination: if smallest possible sum > target
            if nums[i] + nums[i + 1] + nums[i + 2] > target:
                break
            
            # Early termination: if largest possible sum < target
            if nums[i] + nums[n - 2] + nums[n - 1] < target:
                continue
            
            left, right = i + 1, n - 1
            
            while left < right:
                current_sum = nums[i] + nums[left] + nums[right]
                
                if current_sum == target:
                    result.append([nums[i], nums[left], nums[right]])
                    
                    # Skip duplicates for second number
                    while left < right and nums[left] == nums[left + 1]:
                        left += 1
                    # Skip duplicates for third number
                    while left < right and nums[right] == nums[right - 1]:
                        right -= 1
                    
                    left += 1
                    right -= 1
                elif current_sum < target:
                    left += 1
                else:
                    right -= 1
        
        return result
```

**2. Memory Access Optimization**
- Process elements in cache-friendly order
- Minimize pointer jumps and irregular access patterns
- Use spatial locality for better performance

### Mathematical Analysis of Two Pointers

**Time Complexity Analysis**:
- **Best Case**: O(n) - Each element visited once
- **Average Case**: O(n) - Linear scan with early termination
- **Worst Case**: O(n) - All elements must be examined

**Space Complexity**: Usually O(1) - Only using pointer variables

**Optimality Proof**:
Two pointers is optimal for many problems because:
1. Each decision eliminates the maximum possible number of invalid combinations
2. No backtracking is needed due to monotonic properties
3. Every element is processed at most twice (by each pointer)

### Common Pitfalls and Edge Cases

1. **Integer Overflow**: Be careful with sum calculations
   ```python
   # Instead of: if nums[i] + nums[j] > target
   # Use: if nums[i] > target - nums[j]
   ```

2. **Array Bounds**: Always check pointer validity
   ```python
   while left < right and left >= 0 and right < len(nums):
   ```

3. **Duplicate Handling**: Consistent duplicate skipping strategy
   ```python
   # Skip duplicates after finding a valid solution
   while left < right and nums[left] == nums[left + 1]:
       left += 1
   ```

4. **Empty Arrays**: Handle edge cases gracefully
   ```python
   if not nums or len(nums) < 2:
       return []
   ```

### Questions from Top 150 (25–29)
- ✅ Valid Palindrome - Case-insensitive character comparison
- ✅ Is Subsequence - Greedy two-pointer matching
- ✅ Two Sum II - Input Array Is Sorted - Classic convergent pointers
- ✅ Container With Most Water - Geometric optimization
- ✅ 3Sum - Fixed element + two pointers with duplicate handling

### Advanced Practice Strategy
1. **Master the templates**: Memorize convergent and same-direction patterns
2. **Prove correctness**: Understand why each pointer movement is valid
3. **Optimize systematically**: Start with brute force, then apply two pointers
4. **Handle edge cases**: Empty arrays, duplicates, single elements
5. **Mathematical insight**: Look for monotonic properties and sorting opportunities

---

## 3. Sliding Window

### Core Theory & Mathematical Foundation

**Sliding Window** is an algorithmic technique that transforms nested loops with O(n²) or O(n³) complexity into single-pass O(n) solutions. The fundamental insight is to maintain a "window" of elements and slide it across the data structure, updating the window's state incrementally.

**Mathematical Principle**: 
Instead of recalculating properties for every possible subarray (which gives us n(n+1)/2 total subarrays), we:
1. Maintain a window with desired properties
2. Expand the window by including new elements
3. Contract the window when properties are violated
4. Update our result based on current window state

**Why Sliding Window Works**:
The technique exploits the **monotonic property**: if we know the state of window [i, j], we can efficiently compute the state of window [i, j+1] or [i+1, j] in O(1) time.

### Types of Sliding Window Problems

**1. Fixed Size Window**

When the window size is predetermined and constant.

```python
def fixedSizeWindow():
    """Master patterns for fixed-size sliding windows"""
    
    def maxSumSubarray(arr, k):
        """Maximum sum of subarray of size k"""
        if len(arr) < k:
            return 0
        
        # Calculate sum of first window
        window_sum = sum(arr[:k])
        max_sum = window_sum
        
        # Slide window and update sum
        for i in range(k, len(arr)):
            window_sum += arr[i] - arr[i - k]  # Add new, remove old
            max_sum = max(max_sum, window_sum)
        
        return max_sum
    
    def averageOfSubarrays(arr, k):
        """Average of all subarrays of size k"""
        if len(arr) < k:
            return []
        
        result = []
        window_sum = sum(arr[:k])
        result.append(window_sum / k)
        
        for i in range(k, len(arr)):
            window_sum += arr[i] - arr[i - k]
            result.append(window_sum / k)
        
        return result
    
    def maxSlidingWindow(nums, k):
        """Maximum element in each sliding window of size k"""
        from collections import deque
        
        if not nums or k == 0:
            return []
        
        # Use deque to store indices of array elements
        # Maintain decreasing order of values
        dq = deque()
        result = []
        
        for i in range(len(nums)):
            # Remove indices that are out of current window
            while dq and dq[0] < i - k + 1:
                dq.popleft()
            
            # Remove indices whose corresponding values are smaller
            # than current element (they can never be maximum)
            while dq and nums[dq[-1]] < nums[i]:
                dq.pop()
            
            dq.append(i)
            
            # The front of deque has index of maximum element
            if i >= k - 1:
                result.append(nums[dq[0]])
        
        return result
```

**2. Variable Size Window (Expand/Contract)**

Most common and versatile pattern - window size changes based on conditions.

```python
def variableSizeWindow():
    """Master patterns for variable-size sliding windows"""
    
    def minSubarraySum(target, nums):
        """Minimum size subarray with sum >= target"""
        left = 0
        min_length = float('inf')
        current_sum = 0
        
        for right in range(len(nums)):
            current_sum += nums[right]  # Expand window
            
            # Contract window while condition is satisfied
            while current_sum >= target:
                min_length = min(min_length, right - left + 1)
                current_sum -= nums[left]
                left += 1
        
        return min_length if min_length != float('inf') else 0
    
    def maxSubarraySum(nums, k):
        """Maximum sum subarray with at most k distinct elements"""
        from collections import defaultdict
        
        left = 0
        max_sum = 0
        current_sum = 0
        char_count = defaultdict(int)
        
        for right in range(len(nums)):
            current_sum += nums[right]
            char_count[nums[right]] += 1
            
            # Contract window if we have more than k distinct elements
            while len(char_count) > k:
                char_count[nums[left]] -= 1
                if char_count[nums[left]] == 0:
                    del char_count[nums[left]]
                current_sum -= nums[left]
                left += 1
            
            max_sum = max(max_sum, current_sum)
        
        return max_sum
    
    def longestSubarrayWithAtMostKDistinct(s, k):
        """Longest substring with at most k distinct characters"""
        if not s or k == 0:
            return 0
        
        from collections import defaultdict
        
        left = 0
        max_length = 0
        char_count = defaultdict(int)
        
        for right in range(len(s)):
            char_count[s[right]] += 1
            
            # Contract window if we have more than k distinct characters
            while len(char_count) > k:
                char_count[s[left]] -= 1
                if char_count[s[left]] == 0:
                    del char_count[s[left]]
                left += 1
            
            max_length = max(max_length, right - left + 1)
        
        return max_length
```

**3. Dynamic Window with Complex Conditions**

Advanced patterns where window behavior depends on multiple conditions.

```python
def dynamicSlidingWindow():
    """Advanced dynamic sliding window patterns"""
    
    def longestSubstringWithoutRepeating(s):
        """Longest substring without repeating characters"""
        char_index = {}  # Store last seen index of each character
        left = 0
        max_length = 0
        
        for right, char in enumerate(s):
            # If character is repeated and is within current window
            if char in char_index and char_index[char] >= left:
                left = char_index[char] + 1
            
            char_index[char] = right
            max_length = max(max_length, right - left + 1)
        
        return max_length
    
    def characterReplacement(s, k):
        """Longest substring with at most k character replacements"""
        from collections import defaultdict
        
        left = 0
        max_length = 0
        char_count = defaultdict(int)
        max_char_count = 0
        
        for right in range(len(s)):
            char_count[s[right]] += 1
            max_char_count = max(max_char_count, char_count[s[right]])
            
            # If window size - most frequent char count > k, shrink window
            if right - left + 1 - max_char_count > k:
                char_count[s[left]] -= 1
                left += 1
            
            max_length = max(max_length, right - left + 1)
        
        return max_length
    
    def minWindowSubstring(s, t):
        """Minimum window substring containing all characters of t"""
        if not s or not t:
            return ""
        
        from collections import Counter
        
        dict_t = Counter(t)
        required = len(dict_t)
        
        # Sliding window
        left = right = 0
        formed = 0  # Number of unique characters in window with desired frequency
        
        window_counts = {}
        ans = float("inf"), None, None  # (window size, left, right)
        
        while right < len(s):
            # Add character from the right to the window
            character = s[right]
            window_counts[character] = window_counts.get(character, 0) + 1
            
            # Check if the frequency of current character matches requirement
            if character in dict_t and window_counts[character] == dict_t[character]:
                formed += 1
            
            # Try to contract the window from left
            while left <= right and formed == required:
                character = s[left]
                
                # Save the smallest window so far
                if right - left + 1 < ans[0]:
                    ans = (right - left + 1, left, right)
                
                # Remove leftmost character from window
                window_counts[character] -= 1
                if character in dict_t and window_counts[character] < dict_t[character]:
                    formed -= 1
                
                left += 1
            
            right += 1
        
        return "" if ans[0] == float("inf") else s[ans[1]:ans[2] + 1]
    
    def findAnagrams(s, p):
        """Find all anagrams of p in s using sliding window"""
        if len(p) > len(s):
            return []
        
        from collections import Counter
        
        p_count = Counter(p)
        s_count = Counter()
        result = []
        
        # Initialize window
        for i in range(len(p)):
            s_count[s[i]] += 1
        
        # Check first window
        if s_count == p_count:
            result.append(0)
        
        # Slide window
        for i in range(len(p), len(s)):
            # Add new character
            s_count[s[i]] += 1
            
            # Remove old character
            old_char = s[i - len(p)]
            s_count[old_char] -= 1
            if s_count[old_char] == 0:
                del s_count[old_char]
            
            # Check if current window is an anagram
            if s_count == p_count:
                result.append(i - len(p) + 1)
        
        return result
```

### Advanced Sliding Window Algorithms

**1. Two-Pointer Sliding Window**
```python
def twoPointerSlidingWindow():
    """Combining two pointers with sliding window"""
    
    def numSubarraysWithSum(nums, goal):
        """Number of subarrays with sum equal to goal"""
        def atMost(goal):
            if goal < 0:
                return 0
            
            left = 0
            result = 0
            current_sum = 0
            
            for right in range(len(nums)):
                current_sum += nums[right]
                
                while current_sum > goal:
                    current_sum -= nums[left]
                    left += 1
                
                # All subarrays ending at right with sum <= goal
                result += right - left + 1
            
            return result
        
        # Exactly goal = AtMost(goal) - AtMost(goal-1)
        return atMost(goal) - atMost(goal - 1)
    
    def subarraysWithKDistinct(nums, k):
        """Number of subarrays with exactly k distinct integers"""
        def atMostK(k):
            from collections import defaultdict
            
            left = 0
            result = 0
            count = defaultdict(int)
            
            for right in range(len(nums)):
                if count[nums[right]] == 0:
                    k -= 1
                count[nums[right]] += 1
                
                while k < 0:
                    count[nums[left]] -= 1
                    if count[nums[left]] == 0:
                        k += 1
                    left += 1
                
                result += right - left + 1
            
            return result
        
        return atMostK(k) - atMostK(k - 1)
```

**2. Monotonic Sliding Window**
```python
def monotonicSlidingWindow():
    """Using monotonic deque for sliding window problems"""
    
    def shortestSubarray(nums, k):
        """Shortest subarray with sum >= k (handles negative numbers)"""
        from collections import deque
        
        n = len(nums)
        prefix_sum = [0] * (n + 1)
        
        # Calculate prefix sums
        for i in range(n):
            prefix_sum[i + 1] = prefix_sum[i] + nums[i]
        
        dq = deque()  # Store indices of prefix_sum
        min_length = float('inf')
        
        for i in range(n + 1):
            # Check if we can form a valid subarray ending at i-1
            while dq and prefix_sum[i] - prefix_sum[dq[0]] >= k:
                min_length = min(min_length, i - dq.popleft())
            
            # Maintain increasing order of prefix sums in deque
            while dq and prefix_sum[i] <= prefix_sum[dq[-1]]:
                dq.pop()
            
            dq.append(i)
        
        return min_length if min_length != float('inf') else -1
    
    def longestSubarray(nums, limit):
        """Longest subarray where max - min <= limit"""
        from collections import deque
        
        max_deque = deque()  # Decreasing order (max at front)
        min_deque = deque()  # Increasing order (min at front)
        
        left = 0
        max_length = 0
        
        for right in range(len(nums)):
            # Update max deque
            while max_deque and nums[max_deque[-1]] <= nums[right]:
                max_deque.pop()
            max_deque.append(right)
            
            # Update min deque
            while min_deque and nums[min_deque[-1]] >= nums[right]:
                min_deque.pop()
            min_deque.append(right)
            
            # Shrink window if difference exceeds limit
            while nums[max_deque[0]] - nums[min_deque[0]] > limit:
                if max_deque[0] == left:
                    max_deque.popleft()
                if min_deque[0] == left:
                    min_deque.popleft()
                left += 1
            
            max_length = max(max_length, right - left + 1)
        
        return max_length
```

### String Pattern Matching with Sliding Window

```python
def stringPatternMatching():
    """Advanced string pattern matching using sliding window"""
    
    def findSubstring(s, words):
        """Substring with concatenation of all words"""
        if not s or not words:
            return []
        
        word_len = len(words[0])
        total_len = word_len * len(words)
        word_count = {}
        result = []
        
        # Count occurrences of each word
        for word in words:
            word_count[word] = word_count.get(word, 0) + 1
        
        # Check each possible starting position
        for i in range(len(s) - total_len + 1):
            seen = {}
            j = 0
            
            # Check each word in the potential substring
            while j < len(words):
                word = s[i + j * word_len:i + (j + 1) * word_len]
                
                if word not in word_count:
                    break
                
                seen[word] = seen.get(word, 0) + 1
                
                if seen[word] > word_count[word]:
                    break
                
                j += 1
            
            if j == len(words):
                result.append(i)
        
        return result
    
    def checkInclusion(s1, s2):
        """Check if permutation of s1 exists as substring in s2"""
        if len(s1) > len(s2):
            return False
        
        from collections import Counter
        
        s1_count = Counter(s1)
        window_count = Counter()
        
        # Initialize window
        for i in range(len(s1)):
            window_count[s2[i]] += 1
        
        if window_count == s1_count:
            return True
        
        # Slide window
        for i in range(len(s1), len(s2)):
            # Add new character
            window_count[s2[i]] += 1
            
            # Remove old character
            old_char = s2[i - len(s1)]
            window_count[old_char] -= 1
            if window_count[old_char] == 0:
                del window_count[old_char]
            
            if window_count == s1_count:
                return True
        
        return False
```

### Sliding Window Optimization Techniques

**1. Frequency Map Optimization**
```python
def optimizedFrequencyTracking():
    """Efficient frequency tracking in sliding windows"""
    
    def minWindow(s, t):
        """Optimized minimum window with better space usage"""
        if not s or not t:
            return ""
        
        # Use ASCII array instead of hash map for better performance
        need = [0] * 128  # ASCII characters
        have = [0] * 128
        
        need_count = 0
        for char in t:
            if need[ord(char)] == 0:
                need_count += 1
            need[ord(char)] += 1
        
        left = 0
        have_count = 0
        min_len = float('inf')
        min_start = 0
        
        for right in range(len(s)):
            char = ord(s[right])
            have[char] += 1
            
            if need[char] > 0 and have[char] == need[char]:
                have_count += 1
            
            # Try to shrink window
            while have_count == need_count:
                if right - left + 1 < min_len:
                    min_len = right - left + 1
                    min_start = left
                
                left_char = ord(s[left])
                have[left_char] -= 1
                
                if need[left_char] > 0 and have[left_char] < need[left_char]:
                    have_count -= 1
                
                left += 1
        
        return "" if min_len == float('inf') else s[min_start:min_start + min_len]
```

**2. Early Termination and Pruning**
```python
def slidingWindowOptimizations():
    """Advanced optimization techniques"""
    
    def longestRepeatingCharacterReplacement(s, k):
        """With early termination optimizations"""
        if not s:
            return 0
        
        from collections import defaultdict
        
        # Try each character as the target character
        max_length = 0
        
        for target_char in set(s):
            left = 0
            replacements = 0
            
            for right in range(len(s)):
                if s[right] != target_char:
                    replacements += 1
                
                # Shrink window if too many replacements needed
                while replacements > k:
                    if s[left] != target_char:
                        replacements -= 1
                    left += 1
                
                max_length = max(max_length, right - left + 1)
        
        return max_length
```

### Mathematical Analysis

**Time Complexity Analysis**:
- **Fixed Window**: O(n) - Each element visited exactly once
- **Variable Window**: O(n) - Each element enters and leaves window at most once
- **Amortized Analysis**: Even with nested while loops, total operations = O(n)

**Space Complexity**:
- **Basic Pattern**: O(1) - Only tracking window boundaries
- **With Hash Map**: O(k) where k is the number of distinct elements in window
- **With Deque**: O(w) where w is the maximum window size

**Optimality Proof**:
Sliding window is optimal because:
1. We never recompute overlapping work
2. Each element is processed at most twice (entry and exit)
3. Window state transitions are O(1) amortized

### Advanced Problem Patterns

**1. Multiple Windows**
```python
def multipleWindows():
    """Managing multiple sliding windows simultaneously"""
    
    def maxSumTwoNoOverlap(nums, firstLen, secondLen):
        """Maximum sum of two non-overlapping subarrays"""
        def maxSum(arr, first, second):
            # Calculate all possible sums for first subarray
            first_sums = []
            current_sum = sum(arr[:first])
            first_sums.append(current_sum)
            
            for i in range(first, len(arr)):
                current_sum += arr[i] - arr[i - first]
                first_sums.append(current_sum)
            
            # Track maximum first sum seen so far
            max_first = [0] * len(first_sums)
            max_first[0] = first_sums[0]
            
            for i in range(1, len(first_sums)):
                max_first[i] = max(max_first[i-1], first_sums[i])
            
            # Calculate maximum sum with second subarray
            max_total = 0
            if len(arr) >= first + second:
                second_sum = sum(arr[first:first + second])
                max_total = max(max_total, max_first[0] + second_sum)
                
                for i in range(first + second, len(arr)):
                    second_sum += arr[i] - arr[i - second]
                    max_total = max(max_total, max_first[i - second] + second_sum)
            
            return max_total
        
        return max(maxSum(nums, firstLen, secondLen),
                  maxSum(nums, secondLen, firstLen))
```

### Debugging and Common Pitfalls

**Common Mistakes**:
1. **Off-by-one errors**: Window boundaries and indices
2. **Infinite loops**: Incorrect window contraction logic
3. **Hash map cleanup**: Forgetting to remove zero-count entries
4. **Integer overflow**: Large sums in window calculations

**Debugging Techniques**:
```python
def debugSlidingWindow():
    """Template with debugging capabilities"""
    
    def debuggingTemplate(arr, condition):
        left = 0
        result = 0
        window_state = {}  # Track current window state
        
        for right in range(len(arr)):
            # Debug: Print window expansion
            print(f"Expanding window to include arr[{right}] = {arr[right]}")
            
            # Update window state
            window_state = updateWindowState(window_state, arr[right])
            
            # Contract window if necessary
            while not condition(window_state):
                print(f"Contracting window, removing arr[{left}] = {arr[left]}")
                window_state = removeFromWindowState(window_state, arr[left])
                left += 1
            
            # Update result
            current_window_size = right - left + 1
            print(f"Current window: [{left}, {right}], size: {current_window_size}")
            result = max(result, current_window_size)
        
        return result
```

### Questions from Top 150 (30–33)
- ✅ Minimum Size Subarray Sum - Variable window with sum constraint
- ✅ Longest Substring Without Repeating Characters - Dynamic window with uniqueness
- ✅ Substring with Concatenation of All Words - Fixed window with pattern matching
- ✅ Minimum Window Substring - Variable window with coverage requirement

### Master Strategy for Sliding Window Problems
1. **Identify the pattern**: Fixed vs variable window size
2. **Define window state**: What data structure tracks the window?
3. **Expansion condition**: When do we expand the window?
4. **Contraction condition**: When do we shrink the window?
5. **Result update**: When and how do we update our answer?
6. **Optimization**: Can we eliminate redundant computations?

**Template Selection Guide**:
- Use **fixed window** when size is given explicitly
- Use **variable window** for "longest/shortest with condition" problems
- Use **monotonic deque** when tracking min/max in window
- Use **frequency maps** for character/element counting problems

---

## 4. Matrix

### Core Theory & Coordinate Systems

A **Matrix** is a 2D array where elements are accessed using row and column coordinates: `matrix[row][col]`. Understanding coordinate systems, memory layout, and traversal patterns is fundamental to matrix algorithms.

**Memory Layout & Cache Efficiency**:
```
Matrix (3x4):  
[1, 2, 3, 4]
[5, 6, 7, 8]    →  Memory: [1][2][3][4][5][6][7][8][9][10][11][12]
[9,10,11,12]       (Row-major order in most languages)

Access patterns:
- Row-wise: Cache-friendly, consecutive memory access
- Column-wise: Cache-unfriendly, stride access pattern
```

**Coordinate System Conventions**:
- **Matrix indexing**: `matrix[row][col]` where `0 ≤ row < rows, 0 ≤ col < cols`
- **Mathematical**: Often (x, y) where x is column, y is row
- **Image processing**: (x, y) where x is horizontal, y is vertical
- **Game coordinates**: Various conventions depending on context

### Matrix Traversal Patterns

**1. Basic Traversal Algorithms**

```python
def matrixTraversals():
    """Comprehensive matrix traversal patterns"""
    
    def spiralOrder(matrix):
        """Traverse matrix in spiral order (clockwise)"""
        if not matrix or not matrix[0]:
            return []
        
        result = []
        top, bottom = 0, len(matrix) - 1
        left, right = 0, len(matrix[0]) - 1
        
        while top <= bottom and left <= right:
            # Traverse right along top row
            for col in range(left, right + 1):
                result.append(matrix[top][col])
            top += 1
            
            # Traverse down along right column
            for row in range(top, bottom + 1):
                result.append(matrix[row][right])
            right -= 1
            
            # Traverse left along bottom row (if we still have rows)
            if top <= bottom:
                for col in range(right, left - 1, -1):
                    result.append(matrix[bottom][col])
                bottom -= 1
            
            # Traverse up along left column (if we still have columns)
            if left <= right:
                for row in range(bottom, top - 1, -1):
                    result.append(matrix[row][left])
                left += 1
        
        return result
    
    def spiralOrderII(n):
        """Generate n×n matrix filled in spiral order"""
        matrix = [[0] * n for _ in range(n)]
        
        top, bottom = 0, n - 1
        left, right = 0, n - 1
        num = 1
        
        while top <= bottom and left <= right:
            # Fill top row
            for col in range(left, right + 1):
                matrix[top][col] = num
                num += 1
            top += 1
            
            # Fill right column
            for row in range(top, bottom + 1):
                matrix[row][right] = num
                num += 1
            right -= 1
            
            # Fill bottom row
            if top <= bottom:
                for col in range(right, left - 1, -1):
                    matrix[bottom][col] = num
                    num += 1
                bottom -= 1
            
            # Fill left column
            if left <= right:
                for row in range(bottom, top - 1, -1):
                    matrix[row][left] = num
                    num += 1
                left += 1
        
        return matrix
    
    def diagonalTraverse(matrix):
        """Traverse matrix diagonally"""
        if not matrix or not matrix[0]:
            return []
        
        rows, cols = len(matrix), len(matrix[0])
        result = []
        
        # Traverse diagonals
        for d in range(rows + cols - 1):
            diagonal = []
            
            if d % 2 == 0:  # Even diagonal (bottom-left to top-right)
                row = min(d, rows - 1)
                col = d - row
                
                while row >= 0 and col < cols:
                    diagonal.append(matrix[row][col])
                    row -= 1
                    col += 1
            else:  # Odd diagonal (top-right to bottom-left)
                col = min(d, cols - 1)
                row = d - col
                
                while col >= 0 and row < rows:
                    diagonal.append(matrix[row][col])
                    row += 1
                    col -= 1
            
            result.extend(diagonal)
        
        return result
    
    def zigzagTraverse(matrix):
        """Traverse matrix in zigzag pattern"""
        if not matrix or not matrix[0]:
            return []
        
        rows, cols = len(matrix), len(matrix[0])
        result = []
        
        for row in range(rows):
            if row % 2 == 0:  # Even rows: left to right
                for col in range(cols):
                    result.append(matrix[row][col])
            else:  # Odd rows: right to left
                for col in range(cols - 1, -1, -1):
                    result.append(matrix[row][col])
        
        return result
```

**2. Advanced Traversal with Direction Arrays**

```python
def directionBasedTraversals():
    """Using direction arrays for flexible traversal"""
    
    def numIslands(grid):
        """Count islands using DFS with direction arrays"""
        if not grid or not grid[0]:
            return 0
        
        rows, cols = len(grid), len(grid[0])
        directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]  # Right, Down, Left, Up
        islands = 0
        
        def dfs(r, c):
            if (r < 0 or r >= rows or c < 0 or c >= cols or 
                grid[r][c] != '1'):
                return
            
            grid[r][c] = '0'  # Mark as visited
            
            # Explore all 4 directions
            for dr, dc in directions:
                dfs(r + dr, c + dc)
        
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == '1':
                    dfs(r, c)
                    islands += 1
        
        return islands
    
    def shortestPathBinaryMatrix(grid):
        """Shortest path in binary matrix using BFS"""
        if not grid or grid[0][0] == 1 or grid[-1][-1] == 1:
            return -1
        
        n = len(grid)
        if n == 1:
            return 1
        
        from collections import deque
        
        # 8-directional movement
        directions = [(-1,-1), (-1,0), (-1,1), (0,-1), (0,1), (1,-1), (1,0), (1,1)]
        
        queue = deque([(0, 0, 1)])  # (row, col, distance)
        visited = set([(0, 0)])
        
        while queue:
            row, col, dist = queue.popleft()
            
            for dr, dc in directions:
                new_row, new_col = row + dr, col + dc
                
                if (0 <= new_row < n and 0 <= new_col < n and 
                    grid[new_row][new_col] == 0 and 
                    (new_row, new_col) not in visited):
                    
                    if new_row == n - 1 and new_col == n - 1:
                        return dist + 1
                    
                    visited.add((new_row, new_col))
                    queue.append((new_row, new_col, dist + 1))
        
        return -1
    
    def maxAreaOfIsland(grid):
        """Find maximum area of island"""
        if not grid or not grid[0]:
            return 0
        
        rows, cols = len(grid), len(grid[0])
        directions = [(0,1), (1,0), (0,-1), (-1,0)]
        max_area = 0
        
        def dfs(r, c):
            if (r < 0 or r >= rows or c < 0 or c >= cols or 
                grid[r][c] != 1):
                return 0
            
            grid[r][c] = 0  # Mark as visited
            area = 1
            
            for dr, dc in directions:
                area += dfs(r + dr, c + dc)
            
            return area
        
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == 1:
                    max_area = max(max_area, dfs(r, c))
        
        return max_area
```

### Matrix Transformations

**1. Rotation Algorithms**

```python
def matrixRotations():
    """Comprehensive matrix rotation algorithms"""
    
    def rotate90Clockwise(matrix):
        """Rotate matrix 90 degrees clockwise in-place"""
        n = len(matrix)
        
        # Method 1: Transpose then reverse each row
        # Step 1: Transpose matrix (swap matrix[i][j] with matrix[j][i])
        for i in range(n):
            for j in range(i, n):
                matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
        
        # Step 2: Reverse each row
        for i in range(n):
            matrix[i].reverse()
        
        return matrix
    
    def rotate90ClockwiseLayered(matrix):
        """Rotate using layer-by-layer approach"""
        n = len(matrix)
        
        # Process each layer (ring) from outside to inside
        for layer in range(n // 2):
            first = layer
            last = n - 1 - layer
            
            for i in range(first, last):
                offset = i - first
                
                # Save top element
                top = matrix[first][i]
                
                # Top = Left
                matrix[first][i] = matrix[last - offset][first]
                
                # Left = Bottom
                matrix[last - offset][first] = matrix[last][last - offset]
                
                # Bottom = Right
                matrix[last][last - offset] = matrix[i][last]
                
                # Right = Top (saved)
                matrix[i][last] = top
        
        return matrix
    
    def rotateArbitrary(matrix, angle):
        """Rotate matrix by arbitrary angle (90, 180, 270 degrees)"""
        if angle % 90 != 0:
            raise ValueError("Angle must be multiple of 90")
        
        times = (angle % 360) // 90
        
        for _ in range(times):
            matrix = rotate90Clockwise([row[:] for row in matrix])
        
        return matrix
    
    def flipHorizontal(matrix):
        """Flip matrix horizontally (mirror left-right)"""
        for row in matrix:
            row.reverse()
        return matrix
    
    def flipVertical(matrix):
        """Flip matrix vertically (mirror top-bottom)"""
        return matrix[::-1]
    
    def flipDiagonal(matrix):
        """Flip matrix along main diagonal (transpose)"""
        n = len(matrix)
        for i in range(n):
            for j in range(i, n):
                matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
        return matrix
```

**2. Matrix State Modifications**

```python
def matrixStateModifications():
    """In-place matrix state modifications"""
    
    def setZeroes(matrix):
        """Set entire row and column to zero if element is zero"""
        if not matrix or not matrix[0]:
            return
        
        rows, cols = len(matrix), len(matrix[0])
        
        # Use first row and column as markers
        first_row_zero = any(matrix[0][j] == 0 for j in range(cols))
        first_col_zero = any(matrix[i][0] == 0 for i in range(rows))
        
        # Mark rows and columns to be zeroed
        for i in range(1, rows):
            for j in range(1, cols):
                if matrix[i][j] == 0:
                    matrix[0][j] = 0  # Mark column
                    matrix[i][0] = 0  # Mark row
        
        # Set zeros based on markers
        for i in range(1, rows):
            for j in range(1, cols):
                if matrix[0][j] == 0 or matrix[i][0] == 0:
                    matrix[i][j] = 0
        
        # Handle first row
        if first_row_zero:
            for j in range(cols):
                matrix[0][j] = 0
        
        # Handle first column
        if first_col_zero:
            for i in range(rows):
                matrix[i][0] = 0
    
    def gameOfLife(board):
        """Conway's Game of Life - simulate one step"""
        if not board or not board[0]:
            return
        
        rows, cols = len(board), len(board[0])
        directions = [(-1,-1), (-1,0), (-1,1), (0,-1), (0,1), (1,-1), (1,0), (1,1)]
        
        # Use additional states: 2 = was 0, now 1; 3 = was 1, now 0
        for row in range(rows):
            for col in range(cols):
                live_neighbors = 0
                
                # Count live neighbors
                for dr, dc in directions:
                    nr, nc = row + dr, col + dc
                    if (0 <= nr < rows and 0 <= nc < cols and 
                        board[nr][nc] in [1, 3]):  # Currently or originally alive
                        live_neighbors += 1
                
                # Apply rules
                if board[row][col] == 1:  # Currently alive
                    if live_neighbors < 2 or live_neighbors > 3:
                        board[row][col] = 3  # Dies
                else:  # Currently dead
                    if live_neighbors == 3:
                        board[row][col] = 2  # Becomes alive
        
        # Update to final states
        for row in range(rows):
            for col in range(cols):
                if board[row][col] == 2:
                    board[row][col] = 1
                elif board[row][col] == 3:
                    board[row][col] = 0
```

### Advanced Matrix Algorithms

**1. Matrix Search Algorithms**

```python
def matrixSearchAlgorithms():
    """Efficient search algorithms for matrices"""
    
    def searchMatrix(matrix, target):
        """Search in row-wise and column-wise sorted matrix"""
        if not matrix or not matrix[0]:
            return False
        
        rows, cols = len(matrix), len(matrix[0])
        
        # Treat as 1D sorted array
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
    
    def searchMatrixII(matrix, target):
        """Search in matrix sorted row-wise and column-wise"""
        if not matrix or not matrix[0]:
            return False
        
        rows, cols = len(matrix), len(matrix[0])
        
        # Start from top-right corner
        row, col = 0, cols - 1
        
        while row < rows and col >= 0:
            if matrix[row][col] == target:
                return True
            elif matrix[row][col] > target:
                col -= 1  # Move left
            else:
                row += 1  # Move down
        
        return False
    
    def findPeakElement2D(matrix):
        """Find peak element in 2D matrix"""
        if not matrix or not matrix[0]:
            return None
        
        rows, cols = len(matrix), len(matrix[0])
        
        def findMaxInColumn(col):
            max_row = 0
            for row in range(1, rows):
                if matrix[row][col] > matrix[max_row][col]:
                    max_row = row
            return max_row
        
        left, right = 0, cols - 1
        
        while left <= right:
            mid_col = left + (right - left) // 2
            max_row = findMaxInColumn(mid_col)
            
            left_val = matrix[max_row][mid_col - 1] if mid_col > 0 else -1
            right_val = matrix[max_row][mid_col + 1] if mid_col < cols - 1 else -1
            current_val = matrix[max_row][mid_col]
            
            if current_val > left_val and current_val > right_val:
                return [max_row, mid_col]
            elif current_val < left_val:
                right = mid_col - 1
            else:
                left = mid_col + 1
        
        return None
```

**2. Matrix Path Algorithms**

```python
def matrixPathAlgorithms():
    """Path finding and counting in matrices"""
    
    def uniquePaths(m, n):
        """Count unique paths from top-left to bottom-right"""
        # Dynamic programming approach
        dp = [[1] * n for _ in range(m)]
        
        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = dp[i-1][j] + dp[i][j-1]
        
        return dp[m-1][n-1]
    
    def uniquePathsWithObstacles(obstacleGrid):
        """Unique paths with obstacles"""
        if not obstacleGrid or obstacleGrid[0][0] == 1:
            return 0
        
        rows, cols = len(obstacleGrid), len(obstacleGrid[0])
        dp = [[0] * cols for _ in range(rows)]
        
        # Initialize first cell
        dp[0][0] = 1
        
        # Fill first row
        for j in range(1, cols):
            dp[0][j] = dp[0][j-1] if obstacleGrid[0][j] == 0 else 0
        
        # Fill first column
        for i in range(1, rows):
            dp[i][0] = dp[i-1][0] if obstacleGrid[i][0] == 0 else 0
        
        # Fill rest of matrix
        for i in range(1, rows):
            for j in range(1, cols):
                if obstacleGrid[i][j] == 0:
                    dp[i][j] = dp[i-1][j] + dp[i][j-1]
        
        return dp[rows-1][cols-1]
    
    def minPathSum(grid):
        """Minimum path sum from top-left to bottom-right"""
        if not grid or not grid[0]:
            return 0
        
        rows, cols = len(grid), len(grid[0])
        
        # Modify in-place to save space
        for i in range(1, rows):
            grid[i][0] += grid[i-1][0]
        
        for j in range(1, cols):
            grid[0][j] += grid[0][j-1]
        
        for i in range(1, rows):
            for j in range(1, cols):
                grid[i][j] += min(grid[i-1][j], grid[i][j-1])
        
        return grid[rows-1][cols-1]
    
    def maxPathSum(grid):
        """Maximum path sum with various movement constraints"""
        rows, cols = len(grid), len(grid[0])
        
        # Can move right, down, or diagonally down-right
        dp = [[float('-inf')] * cols for _ in range(rows)]
        dp[0][0] = grid[0][0]
        
        # Fill first row (can only come from left)
        for j in range(1, cols):
            dp[0][j] = dp[0][j-1] + grid[0][j]
        
        # Fill first column (can only come from above)
        for i in range(1, rows):
            dp[i][0] = dp[i-1][0] + grid[i][0]
        
        # Fill rest considering all three directions
        for i in range(1, rows):
            for j in range(1, cols):
                dp[i][j] = grid[i][j] + max(
                    dp[i-1][j],      # From above
                    dp[i][j-1],      # From left
                    dp[i-1][j-1]     # From diagonal
                )
        
        return dp[rows-1][cols-1]
```

### Matrix Pattern Recognition

**1. Sudoku and Constraint Validation**

```python
def sudokuAlgorithms():
    """Sudoku validation and solving algorithms"""
    
    def isValidSudoku(board):
        """Validate 9x9 Sudoku board"""
        # Use sets to track seen numbers
        rows = [set() for _ in range(9)]
        cols = [set() for _ in range(9)]
        boxes = [set() for _ in range(9)]
        
        for i in range(9):
            for j in range(9):
                val = board[i][j]
                if val == '.':
                    continue
                
                # Calculate box index
                box_index = (i // 3) * 3 + j // 3
                
                # Check if number already exists
                if (val in rows[i] or val in cols[j] or val in boxes[box_index]):
                    return False
                
                # Add to sets
                rows[i].add(val)
                cols[j].add(val)
                boxes[box_index].add(val)
        
        return True
    
    def solveSudoku(board):
        """Solve Sudoku using backtracking"""
        def is_valid(board, row, col, num):
            # Check row
            for j in range(9):
                if board[row][j] == num:
                    return False
            
            # Check column
            for i in range(9):
                if board[i][col] == num:
                    return False
            
            # Check 3x3 box
            start_row, start_col = 3 * (row // 3), 3 * (col // 3)
            for i in range(start_row, start_row + 3):
                for j in range(start_col, start_col + 3):
                    if board[i][j] == num:
                        return False
            
            return True
        
        def solve():
            for i in range(9):
                for j in range(9):
                    if board[i][j] == '.':
                        for num in '123456789':
                            if is_valid(board, i, j, num):
                                board[i][j] = num
                                
                                if solve():
                                    return True
                                
                                board[i][j] = '.'  # Backtrack
                        
                        return False
            return True
        
        solve()
        return board
```

**2. Advanced Matrix Problems**

```python
def advancedMatrixProblems():
    """Complex matrix algorithms"""
    
    def longestIncreasingPath(matrix):
        """Longest increasing path in matrix using DFS + memoization"""
        if not matrix or not matrix[0]:
            return 0
        
        rows, cols = len(matrix), len(matrix[0])
        directions = [(0,1), (1,0), (0,-1), (-1,0)]
        memo = {}
        
        def dfs(i, j):
            if (i, j) in memo:
                return memo[(i, j)]
            
            max_path = 1  # At least the current cell
            
            for di, dj in directions:
                ni, nj = i + di, j + dj
                
                if (0 <= ni < rows and 0 <= nj < cols and 
                    matrix[ni][nj] > matrix[i][j]):
                    max_path = max(max_path, 1 + dfs(ni, nj))
            
            memo[(i, j)] = max_path
            return max_path
        
        result = 0
        for i in range(rows):
            for j in range(cols):
                result = max(result, dfs(i, j))
        
        return result
    
    def maximalRectangle(matrix):
        """Find largest rectangle in binary matrix"""
        if not matrix or not matrix[0]:
            return 0
        
        rows, cols = len(matrix), len(matrix[0])
        heights = [0] * cols
        max_area = 0
        
        def largestRectangleInHistogram(heights):
            stack = []
            max_area = 0
            
            for i, h in enumerate(heights):
                while stack and heights[stack[-1]] > h:
                    height = heights[stack.pop()]
                    width = i if not stack else i - stack[-1] - 1
                    max_area = max(max_area, height * width)
                stack.append(i)
            
            while stack:
                height = heights[stack.pop()]
                width = len(heights) if not stack else len(heights) - stack[-1] - 1
                max_area = max(max_area, height * width)
            
            return max_area
        
        for i in range(rows):
            for j in range(cols):
                # Update heights array
                if matrix[i][j] == '1':
                    heights[j] += 1
                else:
                    heights[j] = 0
            
            # Find max rectangle in current histogram
            max_area = max(max_area, largestRectangleInHistogram(heights))
        
        return max_area
    
    def countSquares(matrix):
        """Count squares with all 1s using DP"""
        if not matrix or not matrix[0]:
            return 0
        
        rows, cols = len(matrix), len(matrix[0])
        dp = [[0] * cols for _ in range(rows)]
        total_squares = 0
        
        for i in range(rows):
            for j in range(cols):
                if matrix[i][j] == 1:
                    if i == 0 or j == 0:
                        dp[i][j] = 1
                    else:
                        dp[i][j] = min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1
                    
                    total_squares += dp[i][j]
        
        return total_squares
```

### Performance Optimization

**Cache-Friendly Matrix Operations**:
```python
def cacheOptimizedOperations():
    """Cache-friendly matrix algorithms"""
    
    def matrixMultiply(A, B):
        """Optimized matrix multiplication"""
        n, m, p = len(A), len(B), len(B[0])
        C = [[0] * p for _ in range(n)]
        
        # Block-wise multiplication for better cache performance
        block_size = 64  # Typical cache line size
        
        for i_block in range(0, n, block_size):
            for j_block in range(0, p, block_size):
                for k_block in range(0, m, block_size):
                    
                    # Multiply blocks
                    for i in range(i_block, min(i_block + block_size, n)):
                        for j in range(j_block, min(j_block + block_size, p)):
                            for k in range(k_block, min(k_block + block_size, m)):
                                C[i][j] += A[i][k] * B[k][j]
        
        return C
    
    def transposeOptimized(matrix):
        """Cache-friendly matrix transpose"""
        n = len(matrix)
        
        # Use blocking for large matrices
        block_size = 32
        
        for i_block in range(0, n, block_size):
            for j_block in range(0, n, block_size):
                
                # Transpose block
                for i in range(i_block, min(i_block + block_size, n)):
                    for j in range(j_block, min(j_block + block_size, n)):
                        if i != j:
                            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
        
        return matrix
```

### Questions from Top 150 (34–38)
- ✅ Valid Sudoku - Constraint validation with sets
- ✅ Spiral Matrix - Boundary-based traversal algorithm
- ✅ Rotate Image - In-place 90-degree rotation
- ✅ Set Matrix Zeroes - In-place modification using first row/column
- ✅ Game of Life - State transition with temporary encoding

### Matrix Problem Solving Strategy
1. **Identify the pattern**: Traversal, transformation, search, or path-finding
2. **Choose coordinate system**: Consistent indexing throughout solution
3. **Consider space optimization**: In-place modifications when possible
4. **Handle edge cases**: Empty matrices, single element, boundary conditions
5. **Optimize for cache**: Row-major access patterns when possible
6. **Use appropriate data structures**: Sets for constraints, queues for BFS, recursion for DFS

---

## 5. Hash Map / Hash Set

### Core Theory & Hash Function Design

**Hash Tables** are one of the most important data structures in computer science, providing average O(1) time complexity for insertion, deletion, and lookup operations. They work by mapping keys to array indices using a **hash function**.

**Mathematical Foundation**:
```
Hash Function: h(key) → [0, m-1] where m is table size
Load Factor: α = n/m where n = number of elements, m = table size
Expected probe distance ≈ 1/(1-α) for good hash functions
```

**Essential Hash Function Properties**:

1. **Deterministic**: Same input always produces same output
2. **Uniform Distribution**: Keys should be evenly distributed across buckets
3. **Fast Computation**: Hash function should be O(1)
4. **Avalanche Effect**: Small input changes cause large output changes

**Common Hash Functions**:

```python
def hashFunctions():
    """Collection of hash function implementations"""
    
    def djb2Hash(key):
        """DJB2 hash function - good general-purpose hash"""
        hash_value = 5381
        for char in str(key):
            hash_value = ((hash_value << 5) + hash_value) + ord(char)
        return hash_value
    
    def fnvHash(key):
        """FNV-1a hash function - fast and good distribution"""
        FNV_PRIME = 16777619
        FNV_OFFSET = 2166136261
        
        hash_value = FNV_OFFSET
        for byte in str(key).encode():
            hash_value ^= byte
            hash_value *= FNV_PRIME
            hash_value &= 0xFFFFFFFF  # Keep 32-bit
        
        return hash_value
    
    def polynomialRollingHash(string, base=31, mod=10**9 + 7):
        """Rolling hash for strings - useful for pattern matching"""
        hash_value = 0
        power = 1
        
        for char in string:
            hash_value = (hash_value + (ord(char) - ord('a') + 1) * power) % mod
            power = (power * base) % mod
        
        return hash_value
    
    def universalHash(key, a, b, p, m):
        """Universal hash function family"""
        # h_a,b(k) = ((ak + b) mod p) mod m
        # where p is prime > max(keys), a ∈ [1, p-1], b ∈ [0, p-1]
        return ((a * hash(key) + b) % p) % m
```

### Collision Resolution Strategies

**1. Separate Chaining (Open Hashing)**

```python
class HashMapChaining:
    """Hash map with separate chaining collision resolution"""
    
    def __init__(self, initial_capacity=16):
        self.capacity = initial_capacity
        self.size = 0
        self.buckets = [[] for _ in range(self.capacity)]
    
    def _hash(self, key):
        """Simple hash function"""
        return hash(key) % self.capacity
    
    def _resize(self):
        """Resize when load factor exceeds threshold"""
        old_buckets = self.buckets
        self.capacity *= 2
        self.size = 0
        self.buckets = [[] for _ in range(self.capacity)]
        
        # Rehash all elements
        for bucket in old_buckets:
            for key, value in bucket:
                self.put(key, value)
    
    def put(self, key, value):
        """Insert or update key-value pair"""
        if self.size >= 0.75 * self.capacity:  # Load factor threshold
            self._resize()
        
        bucket_index = self._hash(key)
        bucket = self.buckets[bucket_index]
        
        # Update existing key
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return
        
        # Add new key
        bucket.append((key, value))
        self.size += 1
    
    def get(self, key):
        """Retrieve value by key"""
        bucket_index = self._hash(key)
        bucket = self.buckets[bucket_index]
        
        for k, v in bucket:
            if k == key:
                return v
        
        raise KeyError(key)
    
    def remove(self, key):
        """Remove key-value pair"""
        bucket_index = self._hash(key)
        bucket = self.buckets[bucket_index]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                del bucket[i]
                self.size -= 1
                return v
        
        raise KeyError(key)
```

**2. Open Addressing (Closed Hashing)**

```python
class HashMapOpenAddressing:
    """Hash map with open addressing collision resolution"""
    
    def __init__(self, initial_capacity=16):
        self.capacity = initial_capacity
        self.size = 0
        self.keys = [None] * self.capacity
        self.values = [None] * self.capacity
        self.deleted = [False] * self.capacity
    
    def _hash(self, key):
        return hash(key) % self.capacity
    
    def _probe(self, key, probe_type="linear"):
        """Different probing strategies"""
        index = self._hash(key)
        
        if probe_type == "linear":
            while (self.keys[index] is not None and 
                   self.keys[index] != key and 
                   not self.deleted[index]):
                index = (index + 1) % self.capacity
        
        elif probe_type == "quadratic":
            i = 0
            while (self.keys[index] is not None and 
                   self.keys[index] != key and 
                   not self.deleted[index]):
                i += 1
                index = (self._hash(key) + i * i) % self.capacity
        
        elif probe_type == "double":
            # Double hashing: h1(key) + i * h2(key)
            h1 = self._hash(key)
            h2 = 7 - (hash(key) % 7)  # Second hash function
            i = 0
            
            while (self.keys[index] is not None and 
                   self.keys[index] != key and 
                   not self.deleted[index]):
                i += 1
                index = (h1 + i * h2) % self.capacity
        
        return index
    
    def put(self, key, value):
        """Insert or update key-value pair"""
        if self.size >= 0.5 * self.capacity:  # Lower threshold for open addressing
            self._resize()
        
        index = self._probe(key)
        
        if self.keys[index] is None or self.deleted[index]:
            self.size += 1
        
        self.keys[index] = key
        self.values[index] = value
        self.deleted[index] = False
    
    def get(self, key):
        """Retrieve value by key"""
        index = self._probe(key)
        
        if self.keys[index] == key and not self.deleted[index]:
            return self.values[index]
        
        raise KeyError(key)
    
    def remove(self, key):
        """Remove key-value pair using lazy deletion"""
        index = self._probe(key)
        
        if self.keys[index] == key and not self.deleted[index]:
            self.deleted[index] = True
            self.size -= 1
            return self.values[index]
        
        raise KeyError(key)
```

### Advanced Hash Map Applications

**1. Frequency Counting and Statistical Analysis**

```python
def frequencyAnalysisPatterns():
    """Advanced frequency counting patterns"""
    
    def topKFrequent(nums, k):
        """Find k most frequent elements using hash map + heap"""
        from collections import Counter
        import heapq
        
        # Count frequencies
        count = Counter(nums)
        
        # Use min-heap of size k
        heap = []
        for num, freq in count.items():
            heapq.heappush(heap, (freq, num))
            if len(heap) > k:
                heapq.heappop(heap)
        
        return [num for freq, num in heap]
    
    def topKFrequentBucketSort(nums, k):
        """Alternative using bucket sort - O(n) time"""
        from collections import Counter
        
        count = Counter(nums)
        
        # Create buckets for each frequency
        bucket = [[] for _ in range(len(nums) + 1)]
        for num, freq in count.items():
            bucket[freq].append(num)
        
        result = []
        for freq in range(len(bucket) - 1, 0, -1):
            result.extend(bucket[freq])
            if len(result) >= k:
                break
        
        return result[:k]
    
    def findAnagrams(s, p):
        """Find all anagrams using frequency maps"""
        from collections import Counter
        
        if len(p) > len(s):
            return []
        
        p_count = Counter(p)
        window_count = Counter()
        result = []
        
        # Initialize window
        for i in range(len(p)):
            window_count[s[i]] += 1
        
        if window_count == p_count:
            result.append(0)
        
        # Slide window
        for i in range(len(p), len(s)):
            # Add new character
            window_count[s[i]] += 1
            
            # Remove old character
            old_char = s[i - len(p)]
            window_count[old_char] -= 1
            if window_count[old_char] == 0:
                del window_count[old_char]
            
            if window_count == p_count:
                result.append(i - len(p) + 1)
        
        return result
    
    def groupAnagrams(strs):
        """Group anagrams using canonical form as key"""
        from collections import defaultdict
        
        anagram_groups = defaultdict(list)
        
        for s in strs:
            # Create canonical form (sorted characters)
            key = ''.join(sorted(s))
            anagram_groups[key].append(s)
        
        return list(anagram_groups.values())
    
    def groupAnagramsFrequency(strs):
        """Alternative grouping using character frequency as key"""
        from collections import defaultdict, Counter
        
        anagram_groups = defaultdict(list)
        
        for s in strs:
            # Use frequency count as key
            count = Counter(s)
            key = tuple(sorted(count.items()))
            anagram_groups[key].append(s)
        
        return list(anagram_groups.values())
```

**2. Two Sum Variants and Extensions**

```python
def twoSumPatterns():
    """Comprehensive two sum problem solutions"""
    
    def twoSum(nums, target):
        """Classic two sum using hash map"""
        seen = {}
        
        for i, num in enumerate(nums):
            complement = target - num
            if complement in seen:
                return [seen[complement], i]
            seen[num] = i
        
        return []
    
    def twoSumII(nums, target):
        """Two sum on sorted array - two pointers approach"""
        left, right = 0, len(nums) - 1
        
        while left < right:
            current_sum = nums[left] + nums[right]
            if current_sum == target:
                return [left + 1, right + 1]  # 1-indexed
            elif current_sum < target:
                left += 1
            else:
                right -= 1
        
        return []
    
    def twoSumAllPairs(nums, target):
        """Find all unique pairs that sum to target"""
        from collections import Counter
        
        count = Counter(nums)
        result = []
        
        for num in count:
            complement = target - num
            
            if complement in count:
                if num == complement and count[num] >= 2:
                    result.append([num, complement])
                elif num < complement:  # Avoid duplicates
                    result.append([num, complement])
        
        return result
    
    def twoSumCount(nums, target):
        """Count number of pairs that sum to target"""
        from collections import Counter
        
        count = Counter(nums)
        result = 0
        
        for num in count:
            complement = target - num
            
            if complement in count:
                if num == complement:
                    # Choose 2 from count[num] items
                    result += count[num] * (count[num] - 1) // 2
                elif num < complement:  # Count each pair once
                    result += count[num] * count[complement]
        
        return result
    
    def threeSum(nums):
        """Three sum using hash map optimization"""
        nums.sort()
        result = []
        n = len(nums)
        
        for i in range(n - 2):
            if i > 0 and nums[i] == nums[i - 1]:
                continue
            
            seen = set()
            target = -nums[i]
            
            for j in range(i + 1, n):
                complement = target - nums[j]
                
                if complement in seen:
                    result.append([nums[i], complement, nums[j]])
                    
                    # Skip duplicates
                    while j + 1 < n and nums[j] == nums[j + 1]:
                        j += 1
                
                seen.add(nums[j])
        
        return result
```

**3. Design Patterns with Hash Maps**

```python
def hashMapDesignPatterns():
    """Advanced hash map design patterns"""
    
    class LRUCache:
        """Least Recently Used Cache using HashMap + Doubly Linked List"""
        
        class DLinkedNode:
            def __init__(self, key=0, value=0):
                self.key = key
                self.value = value
                self.prev = None
                self.next = None
        
        def __init__(self, capacity):
            self.capacity = capacity
            self.cache = {}  # key -> node
            
            # Create dummy head and tail
            self.head = self.DLinkedNode()
            self.tail = self.DLinkedNode()
            self.head.next = self.tail
            self.tail.prev = self.head
        
        def _add_node(self, node):
            """Add node right after head"""
            node.prev = self.head
            node.next = self.head.next
            
            self.head.next.prev = node
            self.head.next = node
        
        def _remove_node(self, node):
            """Remove an existing node from linked list"""
            prev_node = node.prev
            new_node = node.next
            
            prev_node.next = new_node
            new_node.prev = prev_node
        
        def _move_to_head(self, node):
            """Move node to head (mark as recently used)"""
            self._remove_node(node)
            self._add_node(node)
        
        def _pop_tail(self):
            """Remove last node"""
            last_node = self.tail.prev
            self._remove_node(last_node)
            return last_node
        
        def get(self, key):
            node = self.cache.get(key)
            
            if node:
                # Move to head (recently used)
                self._move_to_head(node)
                return node.value
            
            return -1
        
        def put(self, key, value):
            node = self.cache.get(key)
            
            if node:
                # Update value and move to head
                node.value = value
                self._move_to_head(node)
            else:
                new_node = self.DLinkedNode(key, value)
                
                if len(self.cache) >= self.capacity:
                    # Remove least recently used
                    tail = self._pop_tail()
                    del self.cache[tail.key]
                
                self.cache[key] = new_node
                self._add_node(new_node)
    
    class LFUCache:
        """Least Frequently Used Cache"""
        
        def __init__(self, capacity):
            self.capacity = capacity
            self.min_freq = 0
            self.key_to_value = {}
            self.key_to_freq = {}
            self.freq_to_keys = {}  # frequency -> set of keys
        
        def _update_freq(self, key):
            freq = self.key_to_freq[key]
            self.freq_to_keys[freq].remove(key)
            
            if not self.freq_to_keys[freq] and freq == self.min_freq:
                self.min_freq += 1
            
            self.key_to_freq[key] = freq + 1
            if freq + 1 not in self.freq_to_keys:
                self.freq_to_keys[freq + 1] = set()
            self.freq_to_keys[freq + 1].add(key)
        
        def get(self, key):
            if key not in self.key_to_value:
                return -1
            
            self._update_freq(key)
            return self.key_to_value[key]
        
        def put(self, key, value):
            if self.capacity <= 0:
                return
            
            if key in self.key_to_value:
                self.key_to_value[key] = value
                self._update_freq(key)
                return
            
            if len(self.key_to_value) >= self.capacity:
                # Remove least frequently used key
                key_to_remove = self.freq_to_keys[self.min_freq].pop()
                del self.key_to_value[key_to_remove]
                del self.key_to_freq[key_to_remove]
            
            # Add new key
            self.key_to_value[key] = value
            self.key_to_freq[key] = 1
            if 1 not in self.freq_to_keys:
                self.freq_to_keys[1] = set()
            self.freq_to_keys[1].add(key)
            self.min_freq = 1
```

**4. Advanced String Processing with Hash Maps**

```python
def stringHashMapPatterns():
    """Advanced string processing using hash maps"""
    
    def longestConsecutive(nums):
        """Longest consecutive sequence using hash set"""
        if not nums:
            return 0
        
        num_set = set(nums)
        longest = 0
        
        for num in num_set:
            # Only start counting from the beginning of sequence
            if num - 1 not in num_set:
                current_num = num
                current_length = 1
                
                while current_num + 1 in num_set:
                    current_num += 1
                    current_length += 1
                
                longest = max(longest, current_length)
        
        return longest
    
    def longestSubstringKDistinct(s, k):
        """Longest substring with at most k distinct characters"""
        if not s or k == 0:
            return 0
        
        from collections import defaultdict
        
        char_count = defaultdict(int)
        left = 0
        max_length = 0
        
        for right in range(len(s)):
            char_count[s[right]] += 1
            
            # Contract window if more than k distinct characters
            while len(char_count) > k:
                char_count[s[left]] -= 1
                if char_count[s[left]] == 0:
                    del char_count[s[left]]
                left += 1
            
            max_length = max(max_length, right - left + 1)
        
        return max_length
    
    def findRepeatedDnaSequences(s):
        """Find repeated 10-letter DNA sequences"""
        if len(s) < 10:
            return []
        
        seen = set()
        repeated = set()
        
        for i in range(len(s) - 9):
            sequence = s[i:i+10]
            if sequence in seen:
                repeated.add(sequence)
            seen.add(sequence)
        
        return list(repeated)
    
    def wordPattern(pattern, s):
        """Check if string follows given pattern"""
        words = s.split()
        
        if len(pattern) != len(words):
            return False
        
        char_to_word = {}
        word_to_char = {}
        
        for char, word in zip(pattern, words):
            if char in char_to_word:
                if char_to_word[char] != word:
                    return False
            else:
                char_to_word[char] = word
            
            if word in word_to_char:
                if word_to_char[word] != char:
                    return False
            else:
                word_to_char[word] = char
        
        return True
    
    def isomorphicStrings(s, t):
        """Check if two strings are isomorphic"""
        if len(s) != len(t):
            return False
        
        s_to_t = {}
        t_to_s = {}
        
        for char_s, char_t in zip(s, t):
            if char_s in s_to_t:
                if s_to_t[char_s] != char_t:
                    return False
            else:
                s_to_t[char_s] = char_t
            
            if char_t in t_to_s:
                if t_to_s[char_t] != char_s:
                    return False
            else:
                t_to_s[char_t] = char_s
        
        return True
```

### Hash Table Performance Analysis

**Load Factor Analysis**:
```python
def hashTableAnalysis():
    """Performance analysis of hash table operations"""
    
    def analyzeLoadFactor():
        """Analyze performance vs load factor"""
        import random
        import time
        
        results = []
        
        for load_factor in [0.1, 0.25, 0.5, 0.75, 0.9, 0.95]:
            capacity = 1000
            num_elements = int(capacity * load_factor)
            
            # Create hash table
            hash_table = {}
            
            # Insert elements
            keys = random.sample(range(num_elements * 10), num_elements)
            
            start_time = time.time()
            for key in keys:
                hash_table[key] = key
            insert_time = time.time() - start_time
            
            # Lookup elements
            start_time = time.time()
            for key in keys:
                _ = hash_table[key]
            lookup_time = time.time() - start_time
            
            results.append({
                'load_factor': load_factor,
                'insert_time': insert_time,
                'lookup_time': lookup_time,
                'collisions': 0  # Would need custom implementation to measure
            })
        
        return results
    
    def optimalCapacity(expected_elements, target_load_factor=0.75):
        """Calculate optimal initial capacity"""
        return int(expected_elements / target_load_factor)
```

### Cryptographic and Security Applications

```python
def hashMapSecurity():
    """Security-related hash map applications"""
    
    def bloomFilter(capacity, hash_functions):
        """Simple Bloom Filter implementation"""
        import hashlib
        
        class BloomFilter:
            def __init__(self, capacity, num_hashes):
                self.capacity = capacity
                self.num_hashes = num_hashes
                self.bit_array = [False] * capacity
            
            def _hashes(self, item):
                """Generate multiple hash values"""
                hashes = []
                for i in range(self.num_hashes):
                    hash_input = f"{item}{i}".encode()
                    hash_value = int(hashlib.md5(hash_input).hexdigest(), 16)
                    hashes.append(hash_value % self.capacity)
                return hashes
            
            def add(self, item):
                """Add item to filter"""
                for hash_value in self._hashes(item):
                    self.bit_array[hash_value] = True
            
            def might_contain(self, item):
                """Check if item might be in set (no false negatives)"""
                for hash_value in self._hashes(item):
                    if not self.bit_array[hash_value]:
                        return False
                return True
        
        return BloomFilter(capacity, hash_functions)
    
    def consistentHashing():
        """Consistent hashing for distributed systems"""
        import hashlib
        from bisect import bisect_left
        
        class ConsistentHashRing:
            def __init__(self, nodes=None, replicas=100):
                self.replicas = replicas
                self.ring = {}
                self.sorted_keys = []
                
                if nodes:
                    for node in nodes:
                        self.add_node(node)
            
            def _hash(self, key):
                return int(hashlib.md5(key.encode()).hexdigest(), 16)
            
            def add_node(self, node):
                for i in range(self.replicas):
                    key = self._hash(f"{node}:{i}")
                    self.ring[key] = node
                    self.sorted_keys.append(key)
                self.sorted_keys.sort()
            
            def remove_node(self, node):
                for i in range(self.replicas):
                    key = self._hash(f"{node}:{i}")
                    del self.ring[key]
                    self.sorted_keys.remove(key)
            
            def get_node(self, key):
                if not self.ring:
                    return None
                
                hash_key = self._hash(key)
                idx = bisect_left(self.sorted_keys, hash_key)
                
                if idx == len(self.sorted_keys):
                    idx = 0
                
                return self.ring[self.sorted_keys[idx]]
        
        return ConsistentHashRing
```

### Common Hash Map Pitfalls and Optimizations

**Memory Optimization**:
```python
def hashMapOptimizations():
    """Memory and performance optimizations"""
    
    def compactHashMap():
        """Memory-efficient hash map for small keys"""
        # Use __slots__ to reduce memory overhead
        class CompactHashMap:
            __slots__ = ['_keys', '_values', '_size', '_capacity']
            
            def __init__(self, capacity=16):
                self._capacity = capacity
                self._size = 0
                self._keys = [None] * capacity
                self._values = [None] * capacity
            
            def __setitem__(self, key, value):
                # Implementation details...
                pass
        
        return CompactHashMap
    
    def robinHoodHashing():
        """Robin Hood hashing for better worst-case performance"""
        class RobinHoodHashMap:
            def __init__(self, capacity=16):
                self.capacity = capacity
                self.size = 0
                self.keys = [None] * capacity
                self.values = [None] * capacity
                self.distances = [0] * capacity  # Distance from ideal position
            
            def _probe_distance(self, key, index):
                return (index - hash(key) % self.capacity) % self.capacity
            
            def put(self, key, value):
                index = hash(key) % self.capacity
                distance = 0
                
                while True:
                    if self.keys[index] is None:
                        # Empty slot found
                        self.keys[index] = key
                        self.values[index] = value
                        self.distances[index] = distance
                        self.size += 1
                        break
                    
                    if self.keys[index] == key:
                        # Update existing key
                        self.values[index] = value
                        break
                    
                    # Robin Hood: if current distance > existing distance, swap
                    if distance > self.distances[index]:
                        # Swap current with existing
                        key, self.keys[index] = self.keys[index], key
                        value, self.values[index] = self.values[index], value
                        distance, self.distances[index] = self.distances[index], distance
                    
                    index = (index + 1) % self.capacity
                    distance += 1
        
        return RobinHoodHashMap
```

### Questions from Top 150 (39–47)
- ✅ Ransom Note - Character frequency comparison
- ✅ Isomorphic Strings - Bidirectional mapping validation
- ✅ Word Pattern - String-to-pattern mapping
- ✅ Valid Anagram - Character frequency equality
- ✅ Group Anagrams - Canonical form as hash key
- ✅ Two Sum - Complement lookup with hash map
- ✅ Happy Number - Cycle detection in number transformation
- ✅ Contains Duplicate II - Sliding window with hash map
- ✅ Longest Consecutive Sequence - Set-based sequence detection

### Hash Map Mastery Strategy
1. **Choose the right collision resolution**: Chaining vs open addressing
2. **Optimize hash functions**: Use appropriate hash for data type
3. **Monitor load factor**: Resize when performance degrades
4. **Handle edge cases**: Null keys, hash collisions, resize operations
5. **Security considerations**: Avoid hash collision attacks
6. **Memory optimization**: Use compact representations when possible

**Pattern Recognition Guide**:
- **Frequency counting** → Counter or defaultdict
- **Fast lookups** → set or dict
- **Bidirectional mapping** → Two hash maps
- **Grouping by property** → defaultdict(list)
- **Caching/memoization** → LRU cache pattern

---

## 6. Intervals

### Core Theory & Mathematical Foundations

**Intervals** represent continuous ranges of values [start, end] and are fundamental in scheduling, computational geometry, and optimization problems. The key insight is that many interval problems can be solved optimally using **greedy algorithms** combined with **sorting**.

**Mathematical Properties of Intervals**:
- **Overlap Detection**: Two intervals [a, b] and [c, d] overlap if `max(a, c) < min(b, d)`
- **Intersection**: [a, b] ∩ [c, d] = [max(a, c), min(b, d)] if they overlap
- **Union**: [a, b] ∪ [c, d] = [min(a, c), max(b, d)] if they overlap or touch
- **Length**: |[a, b]| = b - a
- **Distance**: distance([a, b], [c, d]) = max(0, max(a, c) - min(b, d))

**Fundamental Interval Relationships**:
```
Case 1: No Overlap        [----]     [----]
Case 2: Touching           [----][----]
Case 3: Partial Overlap    [----]
                              [----]
Case 4: Complete Overlap   [--------]
                             [--]
Case 5: Identical          [----]
                           [----]
```

### Core Interval Algorithms

**1. Interval Merging - The Foundation Algorithm**

```python
def intervalMergingAlgorithms():
    """Comprehensive interval merging techniques"""
    
    def merge(intervals):
        """Merge overlapping intervals - foundational algorithm"""
        if not intervals:
            return []
        
        # Sort by start time - crucial for optimal merging
        intervals.sort(key=lambda x: x[0])
        
        merged = [intervals[0]]
        
        for current in intervals[1:]:
            last = merged[-1]
            
            # Check for overlap: current start <= last end
            if current[0] <= last[1]:
                # Merge intervals by updating end time
                merged[-1] = [last[0], max(last[1], current[1])]
            else:
                # No overlap, add as new interval
                merged.append(current)
        
        return merged
    
    def mergeOptimized(intervals):
        """Memory-optimized in-place merging"""
        if not intervals:
            return []
        
        intervals.sort(key=lambda x: x[0])
        
        write_index = 0
        for i in range(1, len(intervals)):
            # Check if current interval overlaps with the last merged interval
            if intervals[i][0] <= intervals[write_index][1]:
                # Merge by updating end time
                intervals[write_index][1] = max(intervals[write_index][1], intervals[i][1])
            else:
                # No overlap, move to next position
                write_index += 1
                intervals[write_index] = intervals[i]
        
        return intervals[:write_index + 1]
    
    def insert(intervals, newInterval):
        """Insert interval into sorted list and merge"""
        result = []
        i = 0
        n = len(intervals)
        
        # Add all intervals that come before newInterval
        while i < n and intervals[i][1] < newInterval[0]:
            result.append(intervals[i])
            i += 1
        
        # Merge overlapping intervals with newInterval
        while i < n and intervals[i][0] <= newInterval[1]:
            newInterval[0] = min(newInterval[0], intervals[i][0])
            newInterval[1] = max(newInterval[1], intervals[i][1])
            i += 1
        
        result.append(newInterval)
        
        # Add remaining intervals
        while i < n:
            result.append(intervals[i])
            i += 1
        
        return result
```

**2. Interval Scheduling - Greedy Algorithms**

```python
def intervalScheduling():
    """Classic interval scheduling problems with greedy solutions"""
    
    def eraseOverlapIntervals(intervals):
        """Minimum number of intervals to remove (Activity Selection)"""
        if len(intervals) <= 1:
            return 0
        
        # Sort by end time - greedy choice for activity selection
        intervals.sort(key=lambda x: x[1])
        
        count = 0
        prev_end = intervals[0][1]
        
        for i in range(1, len(intervals)):
            if intervals[i][0] < prev_end:
                # Overlap found, remove current interval
                count += 1
            else:
                # No overlap, update end time
                prev_end = intervals[i][1]
        
        return count
    
    def findMinArrowShots(points):
        """Minimum arrows to burst all balloons"""
        if not points:
            return 0
        
        # Sort by end coordinate
        points.sort(key=lambda x: x[1])
        
        arrows = 1
        arrow_position = points[0][1]
        
        for i in range(1, len(points)):
            # If balloon doesn't intersect with arrow position
            if points[i][0] > arrow_position:
                arrows += 1
                arrow_position = points[i][1]
        
        return arrows
    
    def meetingRoomsII(intervals):
        """Minimum meeting rooms required"""
        if not intervals:
            return 0
        
        import heapq
        
        # Sort meetings by start time
        intervals.sort(key=lambda x: x[0])
        
        # Min-heap to track end times of meetings in progress
        min_heap = []
        
        for meeting in intervals:
            # If earliest meeting has ended, reuse the room
            if min_heap and min_heap[0] <= meeting[0]:
                heapq.heappop(min_heap)
            
            # Add current meeting's end time
            heapq.heappush(min_heap, meeting[1])
        
        return len(min_heap)
```

**3. Sweep Line Algorithm Applications**

```python
def sweepLineAlgorithms():
    """Advanced sweep line techniques for interval problems"""
    
    def meetingRoomsSweepLine(intervals):
        """Meeting rooms using sweep line - more intuitive approach"""
        events = []
        
        # Create events: +1 for start, -1 for end
        for start, end in intervals:
            events.append((start, 1))    # Meeting starts
            events.append((end, -1))     # Meeting ends
        
        # Sort events, with end events before start events at same time
        events.sort(key=lambda x: (x[0], x[1]))
        
        max_rooms = 0
        current_rooms = 0
        
        for time, event_type in events:
            current_rooms += event_type
            max_rooms = max(max_rooms, current_rooms)
        
        return max_rooms
    
    def employeeFreeTime(schedule):
        """Find common free time for all employees"""
        # Flatten all intervals
        all_intervals = []
        for employee_schedule in schedule:
            all_intervals.extend(employee_schedule)
        
        # Sort by start time
        all_intervals.sort(key=lambda x: x[0])
        
        # Merge overlapping intervals
        merged = []
        for interval in all_intervals:
            if not merged or merged[-1][1] < interval[0]:
                merged.append(interval)
            else:
                merged[-1][1] = max(merged[-1][1], interval[1])
        
        # Find gaps between merged intervals
        free_time = []
        for i in range(1, len(merged)):
            if merged[i-1][1] < merged[i][0]:
                free_time.append([merged[i-1][1], merged[i][0]])
        
        return free_time
    
    def intervalIntersection(firstList, secondList):
        """Find intersection of two interval lists"""
        result = []
        i = j = 0
        
        while i < len(firstList) and j < len(secondList):
            # Find intersection
            start = max(firstList[i][0], secondList[j][0])
            end = min(firstList[i][1], secondList[j][1])
            
            # Valid intersection
            if start <= end:
                result.append([start, end])
            
            # Move pointer with earlier end time
            if firstList[i][1] < secondList[j][1]:
                i += 1
            else:
                j += 1
        
        return result
```

**4. Advanced Interval Problems**

```python
def advancedIntervalProblems():
    """Complex interval algorithms requiring advanced techniques"""
    
    def carPooling(trips, capacity):
        """Car pooling optimization using sweep line"""
        events = []
        for num_passengers, start, end in trips:
            events.append((start, num_passengers))    # Pickup
            events.append((end, -num_passengers))     # Dropoff
        
        events.sort()
        
        current_passengers = 0
        for location, change in events:
            current_passengers += change
            if current_passengers > capacity:
                return False
        
        return True
    
    def videoStitching(clips, time):
        """Minimum clips to cover [0, time] interval"""
        clips.sort()  # Sort by start time
        
        clips_used = 0
        current_end = 0
        farthest_reach = 0
        i = 0
        
        while current_end < time:
            # Find the clip that can extend our coverage the most
            while i < len(clips) and clips[i][0] <= current_end:
                farthest_reach = max(farthest_reach, clips[i][1])
                i += 1
            
            # No progress possible
            if farthest_reach <= current_end:
                return -1
            
            current_end = farthest_reach
            clips_used += 1
        
        return clips_used
    
    def partitionLabels(s):
        """Partition string to maximize number of parts"""
        # Find last occurrence of each character
        last_occurrence = {}
        for i, char in enumerate(s):
            last_occurrence[char] = i
        
        partitions = []
        start = 0
        max_end = 0
        
        for i, char in enumerate(s):
            max_end = max(max_end, last_occurrence[char])
            
            # If current position is the farthest we need to go
            if i == max_end:
                partitions.append(i - start + 1)
                start = i + 1
        
        return partitions
    
    def taskScheduler(tasks, n):
        """CPU task scheduling with cooldown"""
        from collections import Counter
        import heapq
        
        task_counts = Counter(tasks)
        max_heap = [-count for count in task_counts.values()]
        heapq.heapify(max_heap)
        
        time = 0
        
        while max_heap:
            cycle_tasks = []
            
            # Execute tasks for one cooling cycle
            for _ in range(n + 1):
                if max_heap:
                    task_count = -heapq.heappop(max_heap)
                    if task_count > 1:
                        cycle_tasks.append(task_count - 1)
                time += 1
                
                # If no more tasks, break early
                if not max_heap and not cycle_tasks:
                    break
            
            # Put back remaining tasks
            for count in cycle_tasks:
                heapq.heappush(max_heap, -count)
        
        return time
```

**5. Mathematical Analysis & Optimization**

```python
def intervalComplexityAnalysis():
    """Mathematical analysis of interval algorithms"""
    
    def analyzeGreedyOptimality():
        """
        Greedy Choice Property for Activity Selection:
        
        Theorem: Always selecting the activity that finishes earliest
        (among remaining activities) leads to optimal solution.
        
        Proof by Exchange Argument:
        1. Let A be optimal solution, G be greedy solution
        2. If first activities differ, A₁ starts later than G₁
        3. Since G₁ ends earliest, we can replace A₁ with G₁
        4. This doesn't affect feasibility of remaining activities
        5. By induction, greedy choice is always safe
        
        Time Complexity: O(n log n) for sorting + O(n) for selection
        Space Complexity: O(1) auxiliary space
        """
        pass
    
    def analyzeMergeComplexity():
        """
        Merge Intervals Complexity Analysis:
        
        Time: O(n log n) - dominated by sorting
        Space: O(n) - for output array (could be O(1) if modifying in-place)
        
        Lower Bound: Ω(n log n) - reduces to element distinctness problem
        
        Optimality: Cannot do better than O(n log n) in comparison model
        """
        pass
    
    def intervalSchedulingVariants():
        """
        Weighted Activity Selection - Dynamic Programming Approach:
        
        Unlike unweighted case, greedy doesn't work for weighted intervals.
        Must use DP with optimal substructure.
        
        Recurrence: OPT(j) = max(vⱼ + OPT(p(j)), OPT(j-1))
        where p(j) is latest activity that doesn't conflict with j
        
        Time: O(n log n) with binary search for p(j)
        Space: O(n) for DP table
        """
        pass
```

**6. Real-World Applications**

```python
def realWorldIntervalApplications():
    """Practical applications of interval algorithms"""
    
    def calendarScheduling(calendar1, daily_bounds1, calendar2, daily_bounds2, meeting_duration):
        """Find available meeting slots between two calendars"""
        def merge_intervals(intervals):
            if not intervals:
                return []
            
            intervals.sort()
            merged = [intervals[0]]
            
            for current in intervals[1:]:
                if current[0] <= merged[-1][1]:
                    merged[-1] = [merged[-1][0], max(merged[-1][1], current[1])]
                else:
                    merged.append(current)
            
            return merged
        
        # Add daily bounds as busy times
        all_busy1 = calendar1 + [[0, daily_bounds1[0]], [daily_bounds1[1], 24*60]]
        all_busy2 = calendar2 + [[0, daily_bounds2[0]], [daily_bounds2[1], 24*60]]
        
        # Merge busy times
        busy1 = merge_intervals(all_busy1)
        busy2 = merge_intervals(all_busy2)
        
        # Find intersection of busy times
        all_busy = []
        i = j = 0
        
        while i < len(busy1) and j < len(busy2):
            start = max(busy1[i][0], busy2[j][0])
            end = min(busy1[i][1], busy2[j][1])
            
            if start < end:
                all_busy.append([start, end])
            
            if busy1[i][1] < busy2[j][1]:
                i += 1
            else:
                j += 1
        
        # Find free slots
        free_slots = []
        work_start = max(daily_bounds1[0], daily_bounds2[0])
        work_end = min(daily_bounds1[1], daily_bounds2[1])
        
        if not all_busy:
            if work_end - work_start >= meeting_duration:
                free_slots.append([work_start, work_end])
            return free_slots
        
        # Check gaps between busy periods
        prev_end = work_start
        for start, end in all_busy:
            if start > prev_end and start - prev_end >= meeting_duration:
                free_slots.append([prev_end, start])
            prev_end = max(prev_end, end)
        
        # Check after last busy period
        if work_end > prev_end and work_end - prev_end >= meeting_duration:
            free_slots.append([prev_end, work_end])
        
        return free_slots
    
    def resourceAllocation(requests, resources):
        """Optimal resource allocation using interval scheduling"""
        # Each request is [start_time, end_time, resource_type, priority]
        # Each resource has a capacity
        
        from collections import defaultdict
        import heapq
        
        # Group requests by resource type
        resource_requests = defaultdict(list)
        for start, end, res_type, priority in requests:
            resource_requests[res_type].append((start, end, priority))
        
        allocation = {}
        
        for res_type, type_requests in resource_requests.items():
            # Sort by end time for greedy selection
            type_requests.sort(key=lambda x: x[1])
            
            # Use priority queue for resource assignment
            available_resources = list(range(resources[res_type]))
            heapq.heapify(available_resources)
            
            allocated = []
            resource_end_times = {}
            
            for start, end, priority in type_requests:
                # Find available resource
                if available_resources:
                    resource_id = heapq.heappop(available_resources)
                    allocated.append((start, end, resource_id))
                    resource_end_times[resource_id] = end
                else:
                    # Check if any resource becomes available
                    earliest_free = min(resource_end_times.values())
                    if earliest_free <= start:
                        # Reuse earliest available resource
                        for rid, end_time in resource_end_times.items():
                            if end_time == earliest_free:
                                allocated.append((start, end, rid))
                                resource_end_times[rid] = end
                                break
            
            allocation[res_type] = allocated
        
        return allocation
```

### Questions from Top 150 (48–51)
- ✅ Summary Ranges - Convert sorted array to interval representation
- ✅ Merge Intervals - Core interval merging algorithm with overlap detection  
- ✅ Insert Interval - Efficient insertion into sorted interval list
- ✅ Non-overlapping Intervals - Activity selection problem using greedy algorithm

### Interval Problem Solving Framework
1. **Sort strategically**: By start time for merging, by end time for scheduling
2. **Identify the pattern**: Merging, scheduling, or geometric problem?
3. **Choose the algorithm**: Greedy for optimization, sweep line for events
4. **Handle edge cases**: Empty lists, single intervals, touching intervals
5. **Optimize for constraints**: In-place operations, early termination
6. **Verify correctness**: Prove greedy choice property when applicable

**Pattern Recognition Guide**:
- **"Merge overlapping"** → Sort by start, merge algorithm
- **"Minimum to remove/schedule"** → Sort by end, greedy selection
- **"Free time/gaps"** → Merge busy times, find intervals between
- **"Multiple resources"** → Heap-based scheduling (meeting rooms)
- **"Coverage problems"** → Set cover or sweep line algorithms

---

## 7. Stack

### Core Theory & Mathematical Foundation

A **Stack** is a Last-In-First-Out (LIFO) abstract data type that serves as a collection of elements with two principal operations: **push** (add element to top) and **pop** (remove element from top). Stacks are fundamental in computer science, serving as the backbone for function calls, expression evaluation, parsing, and numerous algorithmic techniques.

**Mathematical Properties**:
- **LIFO Invariant**: The last element pushed is the first element popped
- **Access Pattern**: Only the top element is accessible at any time
- **State Transition**: Stack state S after push(x) followed by pop() returns to original state S
- **Height Function**: h(S) represents number of elements; h(S ∪ {x}) = h(S) + 1

**Memory Model & Implementation**:
```python
class StackImplementations:
    """Multiple stack implementation approaches"""
    
    def arrayBasedStack(self):
        """Array-based implementation - most common"""
        class ArrayStack:
            def __init__(self, capacity=1000):
                self.items = [None] * capacity
                self.top = -1
                self.capacity = capacity
            
            def push(self, item):
                if self.top >= self.capacity - 1:
                    raise OverflowError("Stack overflow")
                self.top += 1
                self.items[self.top] = item
            
            def pop(self):
                if self.top < 0:
                    raise UnderflowError("Stack underflow")
                item = self.items[self.top]
                self.top -= 1
                return item
            
            def peek(self):
                if self.top < 0:
                    raise EmptyStackError("Stack is empty")
                return self.items[self.top]
            
            def is_empty(self):
                return self.top < 0
            
            def size(self):
                return self.top + 1
        
        return ArrayStack
    
    def linkedListStack(self):
        """Linked list implementation - dynamic size"""
        class Node:
            def __init__(self, data):
                self.data = data
                self.next = None
        
        class LinkedStack:
            def __init__(self):
                self.head = None
                self._size = 0
            
            def push(self, item):
                new_node = Node(item)
                new_node.next = self.head
                self.head = new_node
                self._size += 1
            
            def pop(self):
                if not self.head:
                    raise EmptyStackError("Stack is empty")
                item = self.head.data
                self.head = self.head.next
                self._size -= 1
                return item
            
            def peek(self):
                if not self.head:
                    raise EmptyStackError("Stack is empty")
                return self.head.data
            
            def is_empty(self):
                return self.head is None
            
            def size(self):
                return self._size
        
        return LinkedStack
```

### Core Stack Algorithms & Patterns

**1. Bracket/Parentheses Matching - The Foundation Pattern**

```python
def bracketMatchingAlgorithms():
    """Comprehensive bracket matching techniques"""
    
    def isValidParentheses(s):
        """Basic parentheses validation"""
        stack = []
        mapping = {')': '(', '}': '{', ']': '['}
        
        for char in s:
            if char in mapping:  # Closing bracket
                if not stack or stack.pop() != mapping[char]:
                    return False
            else:  # Opening bracket
                stack.append(char)
        
        return len(stack) == 0
    
    def longestValidParentheses(s):
        """Longest valid parentheses substring"""
        stack = [-1]  # Initialize with base index
        max_length = 0
        
        for i, char in enumerate(s):
            if char == '(':
                stack.append(i)
            else:  # char == ')'
                stack.pop()
                
                if not stack:
                    # No matching '(', use current index as new base
                    stack.append(i)
                else:
                    # Calculate length of valid substring
                    current_length = i - stack[-1]
                    max_length = max(max_length, current_length)
        
        return max_length
    
    def removeInvalidParentheses(s):
        """Remove minimum parentheses to make string valid"""
        def is_valid(string):
            count = 0
            for char in string:
                if char == '(':
                    count += 1
                elif char == ')':
                    count -= 1
                    if count < 0:
                        return False
            return count == 0
        
        # BFS to find minimum removals
        from collections import deque
        
        queue = deque([s])
        visited = set([s])
        found = False
        result = []
        
        while queue and not found:
            for _ in range(len(queue)):
                current = queue.popleft()
                
                if is_valid(current):
                    result.append(current)
                    found = True
                
                if not found:
                    # Generate all possible strings by removing one character
                    for i in range(len(current)):
                        if current[i] in '()':
                            next_string = current[:i] + current[i+1:]
                            if next_string not in visited:
                                visited.add(next_string)
                                queue.append(next_string)
        
        return result
    
    def scoreOfParentheses(s):
        """Calculate score of nested parentheses"""
        stack = [0]  # Stack to maintain scores
        
        for char in s:
            if char == '(':
                stack.append(0)  # New nesting level
            else:  # char == ')'
                last = stack.pop()
                # () gets score 1, (A) gets score 2 * score(A)
                stack[-1] += max(2 * last, 1)
        
        return stack[0]
```

**2. Monotonic Stack - Advanced Pattern for Optimization**

```python
def monotonicStackAlgorithms():
    """Monotonic stack for next/previous greater/smaller elements"""
    
    def nextGreaterElements(nums):
        """Next greater element for each position"""
        result = [-1] * len(nums)
        stack = []  # Stack stores indices
        
        for i in range(len(nums)):
            # Maintain decreasing stack (indices of elements in decreasing order)
            while stack and nums[i] > nums[stack[-1]]:
                idx = stack.pop()
                result[idx] = nums[i]
            
            stack.append(i)
        
        return result
    
    def nextGreaterElementsCircular(nums):
        """Next greater element in circular array"""
        n = len(nums)
        result = [-1] * n
        stack = []
        
        # Process array twice to simulate circular behavior
        for i in range(2 * n):
            while stack and nums[i % n] > nums[stack[-1]]:
                idx = stack.pop()
                result[idx] = nums[i % n]
            
            # Only add indices from first pass
            if i < n:
                stack.append(i)
        
        return result
    
    def dailyTemperatures(temperatures):
        """Days until warmer temperature"""
        result = [0] * len(temperatures)
        stack = []  # Stack of indices
        
        for i, temp in enumerate(temperatures):
            while stack and temp > temperatures[stack[-1]]:
                idx = stack.pop()
                result[idx] = i - idx  # Days difference
            
            stack.append(i)
        
        return result
    
    def largestRectangleHistogram(heights):
        """Largest rectangle in histogram - classic monotonic stack"""
        stack = []  # Stack of indices
        max_area = 0
        
        for i, height in enumerate(heights):
            # Maintain increasing stack
            while stack and heights[i] < heights[stack[-1]]:
                h = heights[stack.pop()]
                # Width is distance to current position minus distance to previous smaller element
                w = i if not stack else i - stack[-1] - 1
                max_area = max(max_area, h * w)
            
            stack.append(i)
        
        # Process remaining elements in stack
        while stack:
            h = heights[stack.pop()]
            w = len(heights) if not stack else len(heights) - stack[-1] - 1
            max_area = max(max_area, h * w)
        
        return max_area
    
    def maximalRectangle(matrix):
        """Maximum rectangle in binary matrix using histogram approach"""
        if not matrix or not matrix[0]:
            return 0
        
        rows, cols = len(matrix), len(matrix[0])
        heights = [0] * cols
        max_area = 0
        
        for row in matrix:
            # Update heights for current row
            for i in range(cols):
                heights[i] = heights[i] + 1 if row[i] == '1' else 0
            
            # Find maximum rectangle in current histogram
            max_area = max(max_area, largestRectangleHistogram(heights))
        
        return max_area
    
    def slidingWindowMaximum(nums, k):
        """Maximum in each sliding window using monotonic deque"""
        from collections import deque
        
        if not nums or k == 0:
            return []
        
        dq = deque()  # Store indices, maintain decreasing order of values
        result = []
        
        for i in range(len(nums)):
            # Remove indices outside current window
            while dq and dq[0] < i - k + 1:
                dq.popleft()
            
            # Maintain decreasing order
            while dq and nums[dq[-1]] <= nums[i]:
                dq.pop()
            
            dq.append(i)
            
            # Add maximum to result when window is complete
            if i >= k - 1:
                result.append(nums[dq[0]])
        
        return result
```

**3. Expression Evaluation & Parsing**

```python
def expressionEvaluationAlgorithms():
    """Advanced expression parsing and evaluation"""
    
    def evalRPN(tokens):
        """Evaluate Reverse Polish Notation"""
        stack = []
        operators = {'+', '-', '*', '/'}
        
        for token in tokens:
            if token in operators:
                b = stack.pop()
                a = stack.pop()
                
                if token == '+':
                    result = a + b
                elif token == '-':
                    result = a - b
                elif token == '*':
                    result = a * b
                elif token == '/':
                    # Handle division truncation toward zero
                    result = int(a / b) if a * b >= 0 else -(-a // b) if a < 0 else -(a // -b)
                
                stack.append(result)
            else:
                stack.append(int(token))
        
        return stack[0]
    
    def basicCalculator(s):
        """Evaluate expression with +, -, (, ) and spaces"""
        stack = []
        result = 0
        sign = 1
        num = 0
        
        for char in s:
            if char.isdigit():
                num = num * 10 + int(char)
            elif char in '+-':
                result += sign * num
                sign = 1 if char == '+' else -1
                num = 0
            elif char == '(':
                # Save current state
                stack.append(result)
                stack.append(sign)
                result = 0
                sign = 1
            elif char == ')':
                # Complete current calculation
                result += sign * num
                result *= stack.pop()  # Previous sign
                result += stack.pop()  # Previous result
                num = 0
        
        return result + sign * num
    
    def basicCalculatorII(s):
        """Evaluate expression with +, -, *, / and spaces"""
        stack = []
        num = 0
        op = '+'
        
        for i, char in enumerate(s):
            if char.isdigit():
                num = num * 10 + int(char)
            
            if char in '+-*/' or i == len(s) - 1:
                if op == '+':
                    stack.append(num)
                elif op == '-':
                    stack.append(-num)
                elif op == '*':
                    stack.append(stack.pop() * num)
                elif op == '/':
                    # Handle division truncation toward zero
                    stack.append(int(stack.pop() / num))
                
                op = char
                num = 0
        
        return sum(stack)
    
    def infixToPostfix(expression):
        """Convert infix to postfix notation"""
        precedence = {'+': 1, '-': 1, '*': 2, '/': 2, '^': 3}
        right_associative = {'^'}
        
        output = []
        operator_stack = []
        
        for token in expression:
            if token.isalnum():
                output.append(token)
            elif token == '(':
                operator_stack.append(token)
            elif token == ')':
                while operator_stack and operator_stack[-1] != '(':
                    output.append(operator_stack.pop())
                operator_stack.pop()  # Remove '('
            elif token in precedence:
                while (operator_stack and 
                       operator_stack[-1] != '(' and
                       operator_stack[-1] in precedence and
                       (precedence[operator_stack[-1]] > precedence[token] or
                        (precedence[operator_stack[-1]] == precedence[token] and
                         token not in right_associative))):
                    output.append(operator_stack.pop())
                operator_stack.append(token)
        
        while operator_stack:
            output.append(operator_stack.pop())
        
        return output
    
    def parseExpression(expression):
        """Parse and build expression tree"""
        class TreeNode:
            def __init__(self, val):
                self.val = val
                self.left = None
                self.right = None
        
        def build_tree(postfix):
            stack = []
            
            for token in postfix:
                node = TreeNode(token)
                
                if token in '+-*/^':
                    node.right = stack.pop()
                    node.left = stack.pop()
                
                stack.append(node)
            
            return stack[0] if stack else None
        
        postfix = infixToPostfix(expression)
        return build_tree(postfix)
```

**4. Advanced Stack Data Structures**

```python
def advancedStackStructures():
    """Specialized stack implementations for specific problems"""
    
    class MinStack:
        """Stack with O(1) minimum retrieval"""
        
        def __init__(self):
            self.stack = []
            self.min_stack = []
        
        def push(self, val):
            self.stack.append(val)
            # Push to min_stack if it's empty or val is <= current minimum
            if not self.min_stack or val <= self.min_stack[-1]:
                self.min_stack.append(val)
        
        def pop(self):
            if not self.stack:
                return None
            
            val = self.stack.pop()
            # Pop from min_stack if the popped value was the minimum
            if self.min_stack and val == self.min_stack[-1]:
                self.min_stack.pop()
            
            return val
        
        def top(self):
            return self.stack[-1] if self.stack else None
        
        def getMin(self):
            return self.min_stack[-1] if self.min_stack else None
    
    class MaxStack:
        """Stack with O(log n) maximum operations"""
        
        def __init__(self):
            import heapq
            self.stack = []
            self.max_heap = []  # Use negative values for max heap
            self.removed = set()
            self.id_counter = 0
        
        def push(self, val):
            self.id_counter += 1
            self.stack.append((val, self.id_counter))
            heapq.heappush(self.max_heap, (-val, -self.id_counter))
        
        def pop(self):
            while self.stack and self.stack[-1][1] in self.removed:
                self.stack.pop()
            
            if self.stack:
                val, id_val = self.stack.pop()
                self.removed.add(id_val)
                return val
            return None
        
        def top(self):
            while self.stack and self.stack[-1][1] in self.removed:
                self.stack.pop()
            
            return self.stack[-1][0] if self.stack else None
        
        def peekMax(self):
            while self.max_heap and -self.max_heap[0][1] in self.removed:
                heapq.heappop(self.max_heap)
            
            return -self.max_heap[0][0] if self.max_heap else None
        
        def popMax(self):
            while self.max_heap and -self.max_heap[0][1] in self.removed:
                heapq.heappop(self.max_heap)
            
            if self.max_heap:
                val, id_val = heapq.heappop(self.max_heap)
                self.removed.add(-id_val)
                return -val
            return None
    
    class FreqStack:
        """Stack that pops most frequent element"""
        
        def __init__(self):
            self.freq = {}  # Element frequency
            self.group = {}  # Frequency -> stack of elements
            self.max_freq = 0
        
        def push(self, val):
            f = self.freq.get(val, 0) + 1
            self.freq[val] = f
            
            if f > self.max_freq:
                self.max_freq = f
            
            if f not in self.group:
                self.group[f] = []
            self.group[f].append(val)
        
        def pop(self):
            if self.max_freq == 0:
                return None
            
            val = self.group[self.max_freq].pop()
            self.freq[val] -= 1
            
            if not self.group[self.max_freq]:
                self.max_freq -= 1
            
            return val
```

**5. Stack-Based Algorithms for Complex Problems**

```python
def stackBasedAlgorithms():
    """Using stacks to solve complex algorithmic problems"""
    
    def simplifyPath(path):
        """Simplify Unix file path"""
        stack = []
        
        for component in path.split('/'):
            if component == '..':
                if stack:
                    stack.pop()
            elif component and component != '.':
                stack.append(component)
        
        return '/' + '/'.join(stack)
    
    def decodeString(s):
        """Decode string with nested brackets: 3[a2[c]] -> accaccacc"""
        stack = []
        current_num = 0
        current_str = ""
        
        for char in s:
            if char.isdigit():
                current_num = current_num * 10 + int(char)
            elif char == '[':
                # Push current state to stack
                stack.append((current_str, current_num))
                current_str = ""
                current_num = 0
            elif char == ']':
                # Pop and construct string
                prev_str, num = stack.pop()
                current_str = prev_str + current_str * num
            else:
                current_str += char
        
        return current_str
    
    def validateStackSequences(pushed, popped):
        """Validate if popped sequence is valid for given pushed sequence"""
        stack = []
        pop_index = 0
        
        for push_val in pushed:
            stack.append(push_val)
            
            # Pop as many elements as possible
            while stack and pop_index < len(popped) and stack[-1] == popped[pop_index]:
                stack.pop()
                pop_index += 1
        
        return pop_index == len(popped)
    
    def exclusiveTime(n, logs):
        """Calculate exclusive execution time for functions"""
        stack = []
        times = [0] * n
        
        for log in logs:
            parts = log.split(':')
            func_id = int(parts[0])
            action = parts[1]
            timestamp = int(parts[2])
            
            if action == 'start':
                stack.append((func_id, timestamp))
            else:  # action == 'end'
                start_func, start_time = stack.pop()
                execution_time = timestamp - start_time + 1
                times[func_id] += execution_time
                
                # Subtract this time from parent function
                if stack:
                    parent_func = stack[-1][0]
                    times[parent_func] -= execution_time
        
        return times
    
    def astroidCollision(asteroids):
        """Simulate asteroid collisions"""
        stack = []
        
        for asteroid in asteroids:
            while (stack and asteroid < 0 < stack[-1]):
                # Collision between positive (moving right) and negative (moving left)
                if stack[-1] < -asteroid:
                    # Current asteroid destroys the one in stack
                    stack.pop()
                    continue
                elif stack[-1] == -asteroid:
                    # Both asteroids explode
                    stack.pop()
                # Current asteroid explodes (stack[-1] > -asteroid)
                break
            else:
                # No collision or current asteroid survived
                stack.append(asteroid)
        
        return stack
```

**6. Stack Applications in Parsing & Compilation**

```python
def parsingApplications():
    """Stack applications in parsing and language processing"""
    
    def balancedBrackets(s):
        """Check if brackets are balanced with error reporting"""
        stack = []
        matching = {'(': ')', '[': ']', '{': '}'}
        
        for i, char in enumerate(s):
            if char in matching:
                stack.append((char, i))
            elif char in matching.values():
                if not stack:
                    return False, f"Unexpected closing bracket '{char}' at position {i}"
                
                open_bracket, open_pos = stack.pop()
                if matching[open_bracket] != char:
                    return False, f"Mismatched brackets: '{open_bracket}' at {open_pos} and '{char}' at {i}"
        
        if stack:
            open_bracket, open_pos = stack[-1]
            return False, f"Unmatched opening bracket '{open_bracket}' at position {open_pos}"
        
        return True, "Balanced"
    
    def parseJSONSimple(json_str):
        """Simple JSON parser using stack"""
        stack = []
        i = 0
        
        def parse_value():
            nonlocal i
            while i < len(json_str) and json_str[i].isspace():
                i += 1
            
            if i >= len(json_str):
                raise ValueError("Unexpected end of input")
            
            char = json_str[i]
            
            if char == '{':
                return parse_object()
            elif char == '[':
                return parse_array()
            elif char == '"':
                return parse_string()
            elif char.isdigit() or char == '-':
                return parse_number()
            elif json_str[i:i+4] == 'true':
                i += 4
                return True
            elif json_str[i:i+5] == 'false':
                i += 5
                return False
            elif json_str[i:i+4] == 'null':
                i += 4
                return None
            else:
                raise ValueError(f"Unexpected character: {char}")
        
        def parse_object():
            nonlocal i
            obj = {}
            i += 1  # Skip '{'
            
            # Skip whitespace
            while i < len(json_str) and json_str[i].isspace():
                i += 1
            
            if i < len(json_str) and json_str[i] == '}':
                i += 1
                return obj
            
            while True:
                # Parse key
                key = parse_value()
                if not isinstance(key, str):
                    raise ValueError("Object keys must be strings")
                
                # Skip whitespace and colon
                while i < len(json_str) and json_str[i].isspace():
                    i += 1
                if i >= len(json_str) or json_str[i] != ':':
                    raise ValueError("Expected ':' after object key")
                i += 1
                
                # Parse value
                value = parse_value()
                obj[key] = value
                
                # Skip whitespace
                while i < len(json_str) and json_str[i].isspace():
                    i += 1
                
                if i >= len(json_str):
                    raise ValueError("Unexpected end of input in object")
                
                if json_str[i] == '}':
                    i += 1
                    break
                elif json_str[i] == ',':
                    i += 1
                else:
                    raise ValueError("Expected ',' or '}' in object")
            
            return obj
        
        # Implementation continues...
        return parse_value()
    
    def evaluateRPNWithVariables(tokens, variables):
        """Extended RPN evaluator with variable support"""
        stack = []
        
        for token in tokens:
            if token in '+-*/':
                if len(stack) < 2:
                    raise ValueError("Insufficient operands")
                b = stack.pop()
                a = stack.pop()
                
                if token == '+':
                    result = a + b
                elif token == '-':
                    result = a - b
                elif token == '*':
                    result = a * b
                elif token == '/':
                    if b == 0:
                        raise ValueError("Division by zero")
                    result = a / b
                
                stack.append(result)
            elif token in variables:
                stack.append(variables[token])
            else:
                try:
                    stack.append(float(token))
                except ValueError:
                    raise ValueError(f"Unknown token: {token}")
        
        if len(stack) != 1:
            raise ValueError("Invalid expression")
        
        return stack[0]
```

### Performance Analysis & Optimization

**Time Complexity Analysis**:
- **Basic Operations**: O(1) for push, pop, peek
- **Monotonic Stack**: O(n) total time for n elements (amortized O(1) per operation)
- **Expression Evaluation**: O(n) for parsing + evaluation
- **Bracket Matching**: O(n) single pass

**Space Complexity**:
- **Worst Case**: O(n) - all elements pushed before any pops
- **Average Case**: Depends on push/pop pattern
- **Optimized Structures**: Additional O(n) for auxiliary data (min stack)

**Memory Optimization Techniques**:
```python
def stackOptimizations():
    """Memory and performance optimizations for stacks"""
    
    def spaceOptimizedMinStack(self):
        """Min stack using single stack with encoding"""
        class OptimizedMinStack:
            def __init__(self):
                self.stack = []
                self.min_val = float('inf')
            
            def push(self, val):
                if not self.stack:
                    self.stack.append(0)
                    self.min_val = val
                else:
                    # Encode the difference
                    self.stack.append(val - self.min_val)
                    if val < self.min_val:
                        self.min_val = val
            
            def pop(self):
                if not self.stack:
                    return None
                
                diff = self.stack.pop()
                if diff < 0:
                    # The popped element was the minimum
                    old_min = self.min_val
                    self.min_val -= diff  # Restore previous minimum
                    return old_min
                else:
                    return self.min_val + diff
            
            def top(self):
                if not self.stack:
                    return None
                
                diff = self.stack[-1]
                return self.min_val if diff < 0 else self.min_val + diff
            
            def getMin(self):
                return self.min_val if self.stack else None
        
        return OptimizedMinStack
```

### Questions from Top 150 (52–56)
- ✅ Valid Parentheses - Fundamental bracket matching with stack
- ✅ Simplify Path - Unix path processing using stack for component tracking
- ✅ Min Stack - Auxiliary stack design for O(1) minimum retrieval
- ✅ Evaluate Reverse Polish Notation - Expression evaluation with operator precedence
- ✅ Basic Calculator - Infix expression parsing with parentheses handling

### Stack Problem Solving Framework
1. **Identify LIFO pattern**: Does the problem require processing in reverse order?
2. **Choose stack variant**: Basic, monotonic, or specialized (min/max stack)?
3. **Determine what to store**: Values, indices, or complex state tuples?
4. **Handle edge cases**: Empty stack, overflow/underflow conditions
5. **Optimize space**: Can auxiliary data structures be eliminated?
6. **Validate correctness**: Ensure stack invariants are maintained

**Pattern Recognition Guide**:
- **Matching/Parsing** → Use stack for nested structures
- **Next/Previous greater/smaller** → Monotonic stack
- **Expression evaluation** → Operator stack with precedence
- **Undo operations** → Stack for state history
- **Recursion to iteration** → Explicit stack for call frames

---

## 8. Linked List

### Core Theory & Memory Architecture

A **Linked List** is a linear data structure where elements (nodes) are stored in non-contiguous memory locations, connected through pointers. Unlike arrays, linked lists provide dynamic memory allocation and efficient insertion/deletion at known positions, at the cost of random access capability.

**Memory Model & Pointer Arithmetic**:
```
Array Memory:     [A][B][C][D]  ← Contiguous, cache-friendly
                   ↑
                   Base Address + index * element_size

Linked List:      [A]→[B]→[C]→[D]→NULL  ← Scattered memory locations
                   ↑   ↑   ↑   ↑
                 ptr1 ptr2 ptr3 ptr4  ← Pointers can be anywhere in memory
```

**Mathematical Properties**:
- **Sequential Access**: Access time to nth element is O(n)
- **Dynamic Size**: No fixed capacity constraint unlike arrays
- **Memory Overhead**: Each node requires additional pointer storage
- **Cache Performance**: Poor spatial locality due to pointer chasing

### Fundamental Node Structures & Implementations

```python
def linkedListFoundations():
    """Comprehensive linked list implementations and variations"""
    
    class ListNode:
        """Standard singly linked list node"""
        def __init__(self, val=0, next=None):
            self.val = val
            self.next = next
        
        def __str__(self):
            return str(self.val)
        
        def __repr__(self):
            return f"ListNode({self.val})"
    
    class DoublyListNode:
        """Doubly linked list node for bi-directional traversal"""
        def __init__(self, val=0, prev=None, next=None):
            self.val = val
            self.prev = prev
            self.next = next
    
    class RandomListNode:
        """Node with additional random pointer"""
        def __init__(self, val=0, next=None, random=None):
            self.val = val
            self.next = next
            self.random = random
    
    class LinkedList:
        """Complete linked list implementation with all operations"""
        
        def __init__(self):
            self.head = None
            self.tail = None
            self.size = 0
        
        def append(self, val):
            """Add element to end - O(1) with tail pointer"""
            new_node = ListNode(val)
            if not self.head:
                self.head = self.tail = new_node
            else:
                self.tail.next = new_node
                self.tail = new_node
            self.size += 1
        
        def prepend(self, val):
            """Add element to beginning - O(1)"""
            new_node = ListNode(val)
            new_node.next = self.head
            self.head = new_node
            if not self.tail:
                self.tail = new_node
            self.size += 1
        
        def insert_at(self, index, val):
            """Insert at specific index - O(n)"""
            if index < 0 or index > self.size:
                raise IndexError("Index out of bounds")
            
            if index == 0:
                self.prepend(val)
                return
            
            if index == self.size:
                self.append(val)
                return
            
            new_node = ListNode(val)
            current = self.head
            
            # Traverse to position index-1
            for _ in range(index - 1):
                current = current.next
            
            new_node.next = current.next
            current.next = new_node
            self.size += 1
        
        def delete_by_value(self, val):
            """Delete first occurrence of value - O(n)"""
            if not self.head:
                return False
            
            if self.head.val == val:
                self.head = self.head.next
                if not self.head:
                    self.tail = None
                self.size -= 1
                return True
            
            current = self.head
            while current.next and current.next.val != val:
                current = current.next
            
            if current.next:
                if current.next == self.tail:
                    self.tail = current
                current.next = current.next.next
                self.size -= 1
                return True
            
            return False
        
        def find(self, val):
            """Find index of value - O(n)"""
            current = self.head
            index = 0
            
            while current:
                if current.val == val:
                    return index
                current = current.next
                index += 1
            
            return -1
        
        def to_array(self):
            """Convert to array for debugging - O(n)"""
            result = []
            current = self.head
            while current:
                result.append(current.val)
                current = current.next
            return result
```

### Advanced Pointer Manipulation Techniques

**1. Two-Pointer Algorithms - The Swiss Army Knife**

```python
def twoPointerTechniques():
    """Master-level two-pointer techniques for linked lists"""
    
    def hasCycle(head):
        """Floyd's Cycle Detection - foundational algorithm"""
        if not head or not head.next:
            return False
        
        slow = fast = head
        
        # Phase 1: Detect if cycle exists
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            
            if slow == fast:
                return True
        
        return False
    
    def detectCycle(head):
        """Find the start of the cycle"""
        if not head or not head.next:
            return None
        
        # Phase 1: Detect cycle
        slow = fast = head
        
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            
            if slow == fast:
                break
        else:
            return None  # No cycle found
        
        # Phase 2: Find cycle start
        # Mathematical proof: distance from head to cycle start
        # equals distance from meeting point to cycle start
        finder = head
        while finder != slow:
            finder = finder.next
            slow = slow.next
        
        return slow
    
    def findCycleLength(head):
        """Find length of cycle if it exists"""
        if not head or not head.next:
            return 0
        
        slow = fast = head
        
        # Detect cycle
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
            
            if slow == fast:
                # Count cycle length
                length = 1
                current = slow.next
                while current != slow:
                    current = current.next
                    length += 1
                return length
        
        return 0
    
    def findMiddle(head):
        """Find middle node(s) with different strategies"""
        if not head:
            return None
        
        # Strategy 1: Standard middle (for odd length, exact middle; for even, second middle)
        slow = fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
        
        return slow
    
    def findMiddleAndPrevious(head):
        """Find middle and the node before it"""
        if not head or not head.next:
            return None, head
        
        prev_slow = None
        slow = fast = head
        
        while fast and fast.next:
            prev_slow = slow
            slow = slow.next
            fast = fast.next.next
        
        return prev_slow, slow
    
    def findKthFromEnd(head, k):
        """Find kth node from end using two pointers"""
        if not head or k <= 0:
            return None
        
        first = second = head
        
        # Move first pointer k steps ahead
        for _ in range(k):
            if not first:
                return None  # k is larger than list length
            first = first.next
        
        # Move both pointers until first reaches end
        while first:
            first = first.next
            second = second.next
        
        return second
    
    def removeNthFromEnd(head, n):
        """Remove nth node from end - one-pass algorithm"""
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
    
    def intersectionOfTwoLists(headA, headB):
        """Find intersection point of two linked lists"""
        if not headA or not headB:
            return None
        
        # Method 1: Two pointers with length equalization
        pointerA, pointerB = headA, headB
        
        # When one pointer reaches end, redirect to other list's head
        # This ensures both pointers travel same distance
        while pointerA != pointerB:
            pointerA = pointerA.next if pointerA else headB
            pointerB = pointerB.next if pointerB else headA
        
        return pointerA  # Will be None if no intersection
    
    def getIntersectionWithLengths(headA, headB):
        """Alternative approach calculating lengths first"""
        def get_length(head):
            length = 0
            while head:
                length += 1
                head = head.next
            return length
        
        lenA, lenB = get_length(headA), get_length(headB)
        
        # Align starting positions
        while lenA > lenB:
            headA = headA.next
            lenA -= 1
        
        while lenB > lenA:
            headB = headB.next
            lenB -= 1
        
        # Find intersection
        while headA and headB:
            if headA == headB:
                return headA
            headA = headA.next
            headB = headB.next
        
        return None
```

**2. List Reversal - Fundamental Building Block**

```python
def reversalAlgorithms():
    """Comprehensive reversal techniques and applications"""
    
    def reverseListIterative(head):
        """Iterative reversal - most memory efficient"""
        prev = None
        current = head
        
        while current:
            next_temp = current.next  # Store next node
            current.next = prev       # Reverse the link
            prev = current           # Move prev forward
            current = next_temp      # Move current forward
        
        return prev  # prev is now the new head
    
    def reverseListRecursive(head):
        """Recursive reversal - elegant but uses O(n) stack space"""
        # Base case
        if not head or not head.next:
            return head
        
        # Recursively reverse the rest
        new_head = reverseListRecursive(head.next)
        
        # Reverse current connection
        head.next.next = head
        head.next = None
        
        return new_head
    
    def reverseBetween(head, left, right):
        """Reverse portion of list between positions left and right"""
        if not head or left == right:
            return head
        
        # Create dummy node to handle edge case where left = 1
        dummy = ListNode(0)
        dummy.next = head
        prev = dummy
        
        # Move to position before left
        for _ in range(left - 1):
            prev = prev.next
        
        # Start reversal from left position
        current = prev.next
        
        # Reverse (right - left) connections
        for _ in range(right - left):
            next_node = current.next
            current.next = next_node.next
            next_node.next = prev.next
            prev.next = next_node
        
        return dummy.next
    
    def reverseKGroup(head, k):
        """Reverse nodes in groups of k"""
        if not head or k == 1:
            return head
        
        # Check if we have at least k nodes
        current = head
        for _ in range(k):
            if not current:
                return head
            current = current.next
        
        # Reverse first k nodes
        prev = None
        current = head
        for _ in range(k):
            next_temp = current.next
            current.next = prev
            prev = current
            current = next_temp
        
        # Recursively reverse remaining groups
        if current:
            head.next = reverseKGroup(current, k)
        
        return prev
    
    def swapPairs(head):
        """Swap every two adjacent nodes"""
        if not head or not head.next:
            return head
        
        dummy = ListNode(0)
        dummy.next = head
        prev = dummy
        
        while prev.next and prev.next.next:
            # Nodes to be swapped
            first = prev.next
            second = prev.next.next
            
            # Swapping
            prev.next = second
            first.next = second.next
            second.next = first
            
            # Move prev to end of swapped pair
            prev = first
        
        return dummy.next
    
    def oddEvenList(head):
        """Rearrange list to group odd and even positioned nodes"""
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

**3. Advanced List Operations**

```python
def advancedListOperations():
    """Complex linked list algorithms and data structures"""
    
    def mergeTwoSortedLists(list1, list2):
        """Merge two sorted lists - fundamental building block"""
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
        
        # Attach remaining nodes
        current.next = list1 if list1 else list2
        return dummy.next
    
    def mergeKSortedLists(lists):
        """Merge k sorted lists using divide and conquer"""
        if not lists:
            return None
        
        def merge_two_lists(l1, l2):
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
        
        # Divide and conquer approach
        while len(lists) > 1:
            merged_lists = []
            
            # Merge pairs of lists
            for i in range(0, len(lists), 2):
                l1 = lists[i]
                l2 = lists[i + 1] if i + 1 < len(lists) else None
                merged_lists.append(merge_two_lists(l1, l2))
            
            lists = merged_lists
        
        return lists[0]
    
    def mergeKSortedListsHeap(lists):
        """Alternative approach using min heap"""
        import heapq
        
        if not lists:
            return None
        
        heap = []
        
        # Initialize heap with first node of each list
        for i, head in enumerate(lists):
            if head:
                heapq.heappush(heap, (head.val, i, head))
        
        dummy = ListNode(0)
        current = dummy
        
        while heap:
            val, list_idx, node = heapq.heappop(heap)
            current.next = node
            current = current.next
            
            # Add next node from same list to heap
            if node.next:
                heapq.heappush(heap, (node.next.val, list_idx, node.next))
        
        return dummy.next
    
    def sortList(head):
        """Sort linked list using merge sort - O(n log n) time, O(log n) space"""
        if not head or not head.next:
            return head
        
        # Find middle and split
        def get_middle(node):
            slow = fast = node
            prev = None
            
            while fast and fast.next:
                prev = slow
                slow = slow.next
                fast = fast.next.next
            
            prev.next = None  # Split the list
            return slow
        
        # Recursive merge sort
        middle = get_middle(head)
        left = sortList(head)
        right = sortList(middle)
        
        return mergeTwoSortedLists(left, right)
    
    def partition(head, x):
        """Partition list around value x"""
        # Create two separate lists
        less_head = ListNode(0)
        greater_head = ListNode(0)
        less = less_head
        greater = greater_head
        
        current = head
        while current:
            if current.val < x:
                less.next = current
                less = less.next
            else:
                greater.next = current
                greater = greater.next
            current = current.next
        
        # Connect the two lists
        greater.next = None
        less.next = greater_head.next
        
        return less_head.next
    
    def rotateRight(head, k):
        """Rotate list to the right by k places"""
        if not head or not head.next or k == 0:
            return head
        
        # Find length and make it circular
        old_tail = head
        length = 1
        
        while old_tail.next:
            old_tail = old_tail.next
            length += 1
        
        old_tail.next = head  # Make it circular
        
        # Find new tail (length - k % length - 1) from head
        k = k % length
        steps_to_new_tail = length - k
        
        new_tail = head
        for _ in range(steps_to_new_tail - 1):
            new_tail = new_tail.next
        
        new_head = new_tail.next
        new_tail.next = None
        
        return new_head
```

**4. Complex Data Structures Using Linked Lists**

```python
def linkedListDataStructures():
    """Advanced data structures built on linked lists"""
    
    class LRUCache:
        """Least Recently Used Cache - combination of hash map and doubly linked list"""
        
        class DLinkedNode:
            def __init__(self, key=0, value=0):
                self.key = key
                self.value = value
                self.prev = None
                self.next = None
        
        def __init__(self, capacity):
            self.capacity = capacity
            self.cache = {}  # key -> node
            
            # Create dummy head and tail
            self.head = self.DLinkedNode()
            self.tail = self.DLinkedNode()
            self.head.next = self.tail
            self.tail.prev = self.head
        
        def _add_node(self, node):
            """Add node right after head"""
            node.prev = self.head
            node.next = self.head.next
            
            self.head.next.prev = node
            self.head.next = node
        
        def _remove_node(self, node):
            """Remove an existing node from linked list"""
            prev_node = node.prev
            new_node = node.next
            
            prev_node.next = new_node
            new_node.prev = prev_node
        
        def _move_to_head(self, node):
            """Move node to head (mark as recently used)"""
            self._remove_node(node)
            self._add_node(node)
        
        def _pop_tail(self):
            """Remove last node"""
            last_node = self.tail.prev
            self._remove_node(last_node)
            return last_node
        
        def get(self, key):
            node = self.cache.get(key)
            
            if node:
                # Move to head (recently used)
                self._move_to_head(node)
                return node.value
            
            return -1
        
        def put(self, key, value):
            node = self.cache.get(key)
            
            if node:
                # Update value and move to head
                node.value = value
                self._move_to_head(node)
            else:
                new_node = self.DLinkedNode(key, value)
                
                if len(self.cache) >= self.capacity:
                    # Remove least recently used
                    tail = self._pop_tail()
                    del self.cache[tail.key]
                
                self.cache[key] = new_node
                self._add_node(new_node)
    
    class LFUCache:
        """Least Frequently Used Cache"""
        
        def __init__(self, capacity):
            self.capacity = capacity
            self.min_freq = 0
            self.key_to_value = {}
            self.key_to_freq = {}
            self.freq_to_keys = {}  # frequency -> doubly linked list
        
        def _update_freq(self, key):
            freq = self.key_to_freq[key]
            
            # Remove from current frequency group
            self.freq_to_keys[freq].remove(key)
            
            # Update minimum frequency if necessary
            if not self.freq_to_keys[freq] and freq == self.min_freq:
                self.min_freq += 1
            
            # Add to new frequency group
            freq += 1
            self.key_to_freq[key] = freq
            if freq not in self.freq_to_keys:
                self.freq_to_keys[freq] = []
            self.freq_to_keys[freq].append(key)
        
        def get(self, key):
            if key not in self.key_to_value:
                return -1
            
            self._update_freq(key)
            return self.key_to_value[key]
        
        def put(self, key, value):
            if self.capacity <= 0:
                return
            
            if key in self.key_to_value:
                self.key_to_value[key] = value
                self._update_freq(key)
                return
            
            if len(self.key_to_value) >= self.capacity:
                # Remove least frequently used key
                key_to_remove = self.freq_to_keys[self.min_freq].pop(0)
                del self.key_to_value[key_to_remove]
                del self.key_to_freq[key_to_remove]
            
            # Add new key
            self.key_to_value[key] = value
            self.key_to_freq[key] = 1
            if 1 not in self.freq_to_keys:
                self.freq_to_keys[1] = []
            self.freq_to_keys[1].append(key)
            self.min_freq = 1
    
    def copyRandomList(head):
        """Deep copy list with random pointers - three-pass algorithm"""
        if not head:
            return None
        
        # Phase 1: Create new nodes and interweave with original
        current = head
        while current:
            new_node = RandomListNode(current.val)
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
        dummy = RandomListNode(0)
        current_new = dummy
        current = head
        
        while current:
            current_new.next = current.next
            current.next = current.next.next
            current_new = current_new.next
            current = current.next
        
        return dummy.next
    
    def copyRandomListHashMap(head):
        """Alternative approach using hash map"""
        if not head:
            return None
        
        # Create mapping from original to new nodes
        node_map = {}
        
        # First pass: create all nodes
        current = head
        while current:
            node_map[current] = RandomListNode(current.val)
            current = current.next
        
        # Second pass: set pointers
        current = head
        while current:
            if current.next:
                node_map[current].next = node_map[current.next]
            if current.random:
                node_map[current].random = node_map[current.random]
            current = current.next
        
        return node_map[head]
```

**5. Mathematical Analysis & Optimization**

```python
def linkedListAnalysis():
    """Performance analysis and optimization techniques"""
    
    def analyzeComplexity():
        """
        Linked List Complexity Analysis:
        
        Operations:
        - Access: O(n) - must traverse from head
        - Search: O(n) - sequential scan required
        - Insertion: O(1) if position known, O(n) if searching for position
        - Deletion: O(1) if node reference available, O(n) if searching
        
        Memory:
        - Each node: data + pointer(s) overhead
        - Singly linked: 8 bytes pointer overhead (64-bit systems)
        - Doubly linked: 16 bytes pointer overhead
        - Memory fragmentation: nodes scattered across heap
        
        Cache Performance:
        - Poor spatial locality: consecutive nodes may be far apart in memory
        - Pointer chasing: CPU must wait for each memory access
        - Prefetching ineffective: next address unknown until current node loaded
        """
        pass
    
    def memoryOptimizations():
        """Memory optimization techniques"""
        
        class CompactNode:
            """Memory-optimized node using slots"""
            __slots__ = ['val', 'next']  # Reduces memory overhead
            
            def __init__(self, val=0, next=None):
                self.val = val
                self.next = next
        
        def xorLinkedList():
            """XOR linked list - single pointer stores XOR of prev and next"""
            # Note: Not implementable in Python due to garbage collection
            # But conceptually: link = prev_addr XOR next_addr
            # Given prev_addr, can compute next_addr = prev_addr XOR link
            pass
        
        def arrayBasedList():
            """Array-based linked list for better cache performance"""
            class ArrayLinkedList:
                def __init__(self, capacity=1000):
                    self.values = [None] * capacity
                    self.next_indices = [-1] * capacity
                    self.free_list = list(range(capacity))
                    self.head = -1
                
                def allocate_node(self):
                    if not self.free_list:
                        raise MemoryError("No free nodes available")
                    return self.free_list.pop()
                
                def free_node(self, index):
                    self.free_list.append(index)
                
                def insert_at_head(self, val):
                    new_index = self.allocate_node()
                    self.values[new_index] = val
                    self.next_indices[new_index] = self.head
                    self.head = new_index
        
        return CompactNode, arrayBasedList
    
    def algorithmicOptimizations():
        """Algorithmic optimization techniques"""
        
        def skipList():
            """Skip list for O(log n) search in linked structure"""
            import random
            
            class SkipListNode:
                def __init__(self, val, level):
                    self.val = val
                    self.forward = [None] * (level + 1)
            
            class SkipList:
                def __init__(self, max_level=16):
                    self.max_level = max_level
                    self.header = SkipListNode(-1, max_level)
                    self.level = 0
                
                def random_level(self):
                    level = 0
                    while random.random() < 0.5 and level < self.max_level:
                        level += 1
                    return level
                
                def search(self, target):
                    current = self.header
                    
                    # Start from highest level
                    for i in range(self.level, -1, -1):
                        while (current.forward[i] and 
                               current.forward[i].val < target):
                            current = current.forward[i]
                    
                    current = current.forward[0]
                    return current and current.val == target
                
                def insert(self, val):
                    update = [None] * (self.max_level + 1)
                    current = self.header
                    
                    # Find insertion points at each level
                    for i in range(self.level, -1, -1):
                        while (current.forward[i] and 
                               current.forward[i].val < val):
                            current = current.forward[i]
                        update[i] = current
                    
                    current = current.forward[0]
                    
                    if not current or current.val != val:
                        new_level = self.random_level()
                        
                        if new_level > self.level:
                            for i in range(self.level + 1, new_level + 1):
                                update[i] = self.header
                            self.level = new_level
                        
                        new_node = SkipListNode(val, new_level)
                        
                        for i in range(new_level + 1):
                            new_node.forward[i] = update[i].forward[i]
                            update[i].forward[i] = new_node
            
            return SkipList
        
        def unrolledLinkedList():
            """Unrolled linked list - nodes contain arrays"""
            class UnrolledNode:
                def __init__(self, capacity=16):
                    self.capacity = capacity
                    self.elements = []
                    self.next = None
            
            class UnrolledLinkedList:
                def __init__(self, node_capacity=16):
                    self.node_capacity = node_capacity
                    self.head = UnrolledNode(node_capacity)
                
                def insert(self, index, val):
                    # Find the node and local index
                    current = self.head
                    local_index = index
                    
                    while current and local_index >= len(current.elements):
                        local_index -= len(current.elements)
                        current = current.next
                    
                    if not current:
                        # Create new node
                        current = UnrolledNode(self.node_capacity)
                        # Link appropriately...
                    
                    current.elements.insert(local_index, val)
                    
                    # Split node if it becomes too large
                    if len(current.elements) > self.node_capacity:
                        self._split_node(current)
                
                def _split_node(self, node):
                    mid = len(node.elements) // 2
                    new_node = UnrolledNode(self.node_capacity)
                    new_node.elements = node.elements[mid:]
                    node.elements = node.elements[:mid]
                    new_node.next = node.next
                    node.next = new_node
            
            return UnrolledLinkedList
        
        return skipList(), unrolledLinkedList()
```

**6. Advanced Applications & Problem Patterns**

```python
def advancedApplications():
    """Real-world applications and advanced problem patterns"""
    
    def addTwoNumbers(l1, l2):
        """Add two numbers represented as linked lists"""
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
            
            if l1:
                l1 = l1.next
            if l2:
                l2 = l2.next
        
        return dummy.next
    
    def addTwoNumbersII(l1, l2):
        """Add numbers when most significant digit is first"""
        # Reverse both lists
        def reverse_list(head):
            prev = None
            current = head
            while current:
                next_temp = current.next
                current.next = prev
                prev = current
                current = next_temp
            return prev
        
        rev_l1 = reverse_list(l1)
        rev_l2 = reverse_list(l2)
        
        # Add the reversed lists
        result = addTwoNumbers(rev_l1, rev_l2)
        
        # Reverse the result
        return reverse_list(result)
    
    def removeElements(head, val):
        """Remove all nodes with specific value"""
        dummy = ListNode(0)
        dummy.next = head
        prev = dummy
        current = head
        
        while current:
            if current.val == val:
                prev.next = current.next
            else:
                prev = current
            current = current.next
        
        return dummy.next
    
    def deleteDuplicates(head):
        """Remove duplicates from sorted list"""
        current = head
        
        while current and current.next:
            if current.val == current.next.val:
                current.next = current.next.next
            else:
                current = current.next
        
        return head
    
    def deleteDuplicatesII(head):
        """Remove all nodes with duplicate values"""
        dummy = ListNode(0)
        dummy.next = head
        prev = dummy
        current = head
        
        while current:
            # Check if current node has duplicates
            if current.next and current.val == current.next.val:
                # Skip all nodes with same value
                while current.next and current.val == current.next.val:
                    current = current.next
                prev.next = current.next
            else:
                prev = current
            
            current = current.next
        
        return dummy.next
    
    def isPalindrome(head):
        """Check if linked list is palindrome"""
        if not head or not head.next:
            return True
        
        # Find middle using two pointers
        slow = fast = head
        while fast.next and fast.next.next:
            slow = slow.next
            fast = fast.next.next
        
        # Reverse second half
        second_half = reverse_list(slow.next)
        
        # Compare first and second halves
        first_half = head
        result = True
        
        while second_half:
            if first_half.val != second_half.val:
                result = False
                break
            first_half = first_half.next
            second_half = second_half.next
        
        # Optional: restore original list
        # slow.next = reverse_list(second_half_start)
        
        return result
```

### Debugging & Common Pitfalls

**Common Mistakes & Solutions**:

```python
def debuggingTechniques():
    """Common pitfalls and debugging strategies"""
    
    def visualize_list(head, max_nodes=10):
        """Debug helper to visualize linked list"""
        if not head:
            return "NULL"
        
        result = []
        current = head
        count = 0
        visited = set()
        
        while current and count < max_nodes:
            if id(current) in visited:
                result.append(f"→ CYCLE DETECTED at {current.val}")
                break
            
            visited.add(id(current))
            result.append(str(current.val))
            current = current.next
            count += 1
        
        if current:
            result.append("...")
        
        return " → ".join(result) + " → NULL"
    
    def common_pitfalls():
        """
        Common Pitfalls in Linked List Programming:
        
        1. Null Pointer Exceptions:
           - Always check if node is None before accessing .next
           - Use dummy nodes to simplify edge cases
        
        2. Memory Leaks:
           - In languages with manual memory management
           - Always free nodes when deleting
        
        3. Losing References:
           - Save next pointer before modifying current.next
           - Use temporary variables for complex pointer manipulations
        
        4. Off-by-One Errors:
           - Be careful with loop conditions
           - Test with lists of length 0, 1, and 2
        
        5. Infinite Loops:
           - Can occur when creating cycles accidentally
           - Always ensure loop termination conditions
        
        6. Edge Cases:
           - Empty list (head = None)
           - Single node list
           - Operations on first/last nodes
        
        Debugging Strategies:
        - Draw diagrams of pointer movements
        - Use print statements to trace execution
        - Test with simple cases first
        - Verify invariants at each step
        """
        pass
    
    return visualize_list, common_pitfalls
```

### Questions from Top 150 (57–67)
- ✅ Linked List Cycle - Floyd's cycle detection algorithm with mathematical proof
- ✅ Add Two Numbers - Digit-by-digit addition with carry propagation
- ✅ Merge Two Sorted Lists - Fundamental merging technique using dummy head
- ✅ Copy List with Random Pointer - Deep copying with three-pass interweaving algorithm  
- ✅ Reverse Linked List II - Partial reversal with precise pointer manipulation
- ✅ Reverse Nodes in k-Group - Group reversal using recursion and iteration
- ✅ Remove Nth Node From End - Two-pointer technique with single pass
- ✅ Remove Duplicates from Sorted List II - Complete duplicate removal strategy
- ✅ Rotate List - Circular list transformation with optimal positioning
- ✅ Partition List - Two-list partitioning around pivot value
- ✅ LRU Cache - Advanced data structure combining hash map and doubly linked list

### Linked List Mastery Framework
1. **Master pointer manipulation**: Practice basic operations until intuitive
2. **Visualize before coding**: Draw diagrams for complex operations
3. **Use dummy nodes**: Simplify edge cases and code clarity
4. **Test edge cases**: Empty lists, single nodes, boundary conditions
5. **Understand trade-offs**: Time vs space, simplicity vs efficiency
6. **Pattern recognition**: Identify when to use specific techniques

**Pattern Recognition Guide**:
- **Cycle detection** → Floyd's algorithm (fast/slow pointers)
- **Find middle/kth from end** → Two-pointer technique
- **Reverse operations** → Iterative pointer reversal
- **Merge operations** → Dummy head with comparison logic
- **Complex state tracking** → Hash maps combined with lists
- **Cache implementations** → Doubly linked lists with hash maps

---

## 9. Binary Tree (General / DFS)

### Core Theory & Mathematical Foundation

A **Binary Tree** is a hierarchical data structure where each node has at most two children, traditionally called left and right subtrees. Trees are naturally recursive structures that model hierarchical relationships and enable efficient search, insertion, and traversal operations.

**Mathematical Properties**:
- **Height**: h = ⌊log₂(n)⌋ for balanced trees, h ∈ O(n) for skewed trees
- **Node Count**: For complete tree of height h: n = 2^(h+1) - 1
- **Leaf Nodes**: In a complete binary tree: ⌊(n+1)/2⌋ leaves
- **Internal Nodes**: n - leaf_count internal nodes
- **Path Length**: Sum of levels of all nodes, affects algorithm complexity

**Tree Terminology & Structure**:
```python
def treeFoundations():
    """Comprehensive binary tree foundations and properties"""
    
    class TreeNode:
        """Enhanced tree node with additional functionality"""
        def __init__(self, val=0, left=None, right=None):
            self.val = val
            self.left = left
            self.right = right
        
        def __str__(self):
            return str(self.val)
        
        def __repr__(self):
            return f"TreeNode({self.val})"
    
    class BinaryTree:
        """Complete binary tree implementation with analysis tools"""
        
        def __init__(self, root=None):
            self.root = root
        
        def height(self, node=None):
            """Calculate height of tree - O(n)"""
            if node is None:
                node = self.root
            
            if not node:
                return -1
            
            return 1 + max(self.height(node.left), self.height(node.right))
        
        def size(self, node=None):
            """Count total nodes - O(n)"""
            if node is None:
                node = self.root
            
            if not node:
                return 0
            
            return 1 + self.size(node.left) + self.size(node.right)
        
        def is_balanced(self, node=None):
            """Check if tree is height-balanced - O(n)"""
            if node is None:
                node = self.root
            
            def check_balance(node):
                if not node:
                    return True, -1
                
                left_balanced, left_height = check_balance(node.left)
                if not left_balanced:
                    return False, 0
                
                right_balanced, right_height = check_balance(node.right)
                if not right_balanced:
                    return False, 0
                
                balanced = abs(left_height - right_height) <= 1
                height = 1 + max(left_height, right_height)
                
                return balanced, height
            
            balanced, _ = check_balance(node)
            return balanced
        
        def diameter(self, node=None):
            """Calculate diameter (longest path between any two nodes) - O(n)"""
            if node is None:
                node = self.root
            
            max_diameter = 0
            
            def calculate_depth(node):
                nonlocal max_diameter
                if not node:
                    return 0
                
                left_depth = calculate_depth(node.left)
                right_depth = calculate_depth(node.right)
                
                # Diameter through current node
                current_diameter = left_depth + right_depth
                max_diameter = max(max_diameter, current_diameter)
                
                return 1 + max(left_depth, right_depth)
            
            calculate_depth(node)
            return max_diameter
        
        def is_complete(self):
            """Check if tree is complete - O(n)"""
            if not self.root:
                return True
            
            from collections import deque
            queue = deque([self.root])
            end_reached = False
            
            while queue:
                node = queue.popleft()
                
                if not node:
                    end_reached = True
                else:
                    if end_reached:
                        return False
                    queue.append(node.left)
                    queue.append(node.right)
            
            return True
        
        def is_perfect(self):
            """Check if tree is perfect (all levels completely filled) - O(n)"""
            def check_perfect(node, level, expected_level):
                if not node:
                    return level == expected_level
                
                if not node.left and not node.right:
                    return level == expected_level
                
                if not node.left or not node.right:
                    return False
                
                return (check_perfect(node.left, level + 1, expected_level) and
                       check_perfect(node.right, level + 1, expected_level))
            
            if not self.root:
                return True
            
            height = self.height()
            return check_perfect(self.root, 0, height)
```

### Tree Traversal Algorithms - Complete Mastery

**1. Depth-First Search (DFS) - Recursive & Iterative**

```python
def treeTraversalMastery():
    """Comprehensive tree traversal implementations and applications"""
    
    def preorderTraversals(root):
        """Preorder: Root → Left → Right"""
        
        # Recursive approach
        def preorder_recursive(node):
            if not node:
                return []
            return [node.val] + preorder_recursive(node.left) + preorder_recursive(node.right)
        
        # Iterative with explicit stack
        def preorder_iterative(node):
            if not node:
                return []
            
            result = []
            stack = [node]
            
            while stack:
                current = stack.pop()
                result.append(current.val)
                
                # Push right first, then left (stack is LIFO)
                if current.right:
                    stack.append(current.right)
                if current.left:
                    stack.append(current.left)
            
            return result
        
        # Morris traversal - O(1) space
        def preorder_morris(node):
            result = []
            current = node
            
            while current:
                if not current.left:
                    result.append(current.val)
                    current = current.right
                else:
                    # Find inorder predecessor
                    predecessor = current.left
                    while predecessor.right and predecessor.right != current:
                        predecessor = predecessor.right
                    
                    if not predecessor.right:
                        # Make connection and process current
                        result.append(current.val)
                        predecessor.right = current
                        current = current.left
                    else:
                        # Break connection and move right
                        predecessor.right = None
                        current = current.right
            
            return result
        
        return {
            'recursive': preorder_recursive(root),
            'iterative': preorder_iterative(root), 
            'morris': preorder_morris(root)
        }
    
    def inorderTraversals(root):
        """Inorder: Left → Root → Right (sorted order for BST)"""
        
        def inorder_recursive(node):
            if not node:
                return []
            return inorder_recursive(node.left) + [node.val] + inorder_recursive(node.right)
        
        def inorder_iterative(node):
            result = []
            stack = []
            current = node
            
            while stack or current:
                # Go to leftmost node
                while current:
                    stack.append(current)
                    current = current.left
                
                # Process current node
                current = stack.pop()
                result.append(current.val)
                
                # Move to right subtree
                current = current.right
            
            return result
        
        def inorder_morris(node):
            result = []
            current = node
            
            while current:
                if not current.left:
                    result.append(current.val)
                    current = current.right
                else:
                    predecessor = current.left
                    while predecessor.right and predecessor.right != current:
                        predecessor = predecessor.right
                    
                    if not predecessor.right:
                        predecessor.right = current
                        current = current.left
                    else:
                        predecessor.right = None
                        result.append(current.val)
                        current = current.right
            
            return result
        
        return {
            'recursive': inorder_recursive(root),
            'iterative': inorder_iterative(root),
            'morris': inorder_morris(root)
        }
    
    def postorderTraversals(root):
        """Postorder: Left → Right → Root (deletion-safe order)"""
        
        def postorder_recursive(node):
            if not node:
                return []
            return postorder_recursive(node.left) + postorder_recursive(node.right) + [node.val]
        
        def postorder_iterative(node):
            if not node:
                return []
            
            result = []
            stack = []
            last_visited = None
            current = node
            
            while stack or current:
                if current:
                    stack.append(current)
                    current = current.left
                else:
                    peek_node = stack[-1]
                    
                    # If right child exists and hasn't been processed yet
                    if peek_node.right and last_visited != peek_node.right:
                        current = peek_node.right
                    else:
                        result.append(peek_node.val)
                        last_visited = stack.pop()
            
            return result
        
        def postorder_two_stacks(node):
            """Alternative using two stacks"""
            if not node:
                return []
            
            stack1 = [node]
            stack2 = []
            
            while stack1:
                current = stack1.pop()
                stack2.append(current)
                
                if current.left:
                    stack1.append(current.left)
                if current.right:
                    stack1.append(current.right)
            
            result = []
            while stack2:
                result.append(stack2.pop().val)
            
            return result
        
        return {
            'recursive': postorder_recursive(root),
            'iterative': postorder_iterative(root),
            'two_stacks': postorder_two_stacks(root)
        }
```

**2. Tree Construction & Reconstruction**

```python
def treeConstructionAlgorithms():
    """Advanced tree construction from various inputs"""
    
    def buildTreeFromPreorderInorder(preorder, inorder):
        """Construct tree from preorder and inorder traversals"""
        if not preorder or not inorder:
            return None
        
        # Optimization: use hash map for O(1) index lookup
        inorder_map = {val: i for i, val in enumerate(inorder)}
        self.preorder_idx = 0
        
        def build_helper(left, right):
            if left > right:
                return None
            
            # Root is always first in preorder
            root_val = preorder[self.preorder_idx]
            root = TreeNode(root_val)
            self.preorder_idx += 1
            
            # Find root position in inorder
            root_idx = inorder_map[root_val]
            
            # Build left subtree first (preorder property)
            root.left = build_helper(left, root_idx - 1)
            root.right = build_helper(root_idx + 1, right)
            
            return root
        
        return build_helper(0, len(inorder) - 1)
    
    def buildTreeFromInorderPostorder(inorder, postorder):
        """Construct tree from inorder and postorder traversals"""
        if not inorder or not postorder:
            return None
        
        inorder_map = {val: i for i, val in enumerate(inorder)}
        self.postorder_idx = len(postorder) - 1
        
        def build_helper(left, right):
            if left > right:
                return None
            
            # Root is always last in postorder
            root_val = postorder[self.postorder_idx]
            root = TreeNode(root_val)
            self.postorder_idx -= 1
            
            root_idx = inorder_map[root_val]
            
            # Build right subtree first (postorder property)
            root.right = build_helper(root_idx + 1, right)
            root.left = build_helper(left, root_idx - 1)
            
            return root
        
        return build_helper(0, len(inorder) - 1)
    
    def buildTreeFromPreorderPostorder(preorder, postorder):
        """Construct tree from preorder and postorder (not unique)"""
        if not preorder:
            return None
        
        def build_helper(pre_start, pre_end, post_start, post_end):
            if pre_start > pre_end:
                return None
            
            root = TreeNode(preorder[pre_start])
            
            if pre_start == pre_end:
                return root
            
            # Find left child in postorder
            left_root_val = preorder[pre_start + 1]
            left_root_idx = postorder.index(left_root_val, post_start, post_end)
            left_subtree_size = left_root_idx - post_start + 1
            
            root.left = build_helper(
                pre_start + 1, 
                pre_start + left_subtree_size,
                post_start,
                left_root_idx
            )
            
            root.right = build_helper(
                pre_start + left_subtree_size + 1,
                pre_end,
                left_root_idx + 1,
                post_end - 1
            )
            
            return root
        
        return build_helper(0, len(preorder) - 1, 0, len(postorder) - 1)
    
    def buildTreeFromArray(arr):
        """Build tree from array representation (level order)"""
        if not arr or arr[0] is None:
            return None
        
        root = TreeNode(arr[0])
        from collections import deque
        queue = deque([root])
        i = 1
        
        while queue and i < len(arr):
            node = queue.popleft()
            
            # Add left child
            if i < len(arr) and arr[i] is not None:
                node.left = TreeNode(arr[i])
                queue.append(node.left)
            i += 1
            
            # Add right child
            if i < len(arr) and arr[i] is not None:
                node.right = TreeNode(arr[i])
                queue.append(node.right)
            i += 1
        
        return root
    
    def serializeTree(root):
        """Serialize tree to string representation"""
        def serialize_helper(node):
            if not node:
                return ["null"]
            
            return ([str(node.val)] + 
                   serialize_helper(node.left) + 
                   serialize_helper(node.right))
        
        return ",".join(serialize_helper(root))
    
    def deserializeTree(data):
        """Deserialize string back to tree"""
        def deserialize_helper():
            val = next(vals)
            if val == "null":
                return None
            
            node = TreeNode(int(val))
            node.left = deserialize_helper()
            node.right = deserialize_helper()
            return node
        
        vals = iter(data.split(","))
        return deserialize_helper()
```

**3. Advanced Tree Algorithms**

```python
def advancedTreeAlgorithms():
    """Complex tree algorithms and problem-solving patterns"""
    
    def lowestCommonAncestor(root, p, q):
        """Find LCA of two nodes - foundational algorithm"""
        if not root or root == p or root == q:
            return root
        
        left = lowestCommonAncestor(root.left, p, q)
        right = lowestCommonAncestor(root.right, p, q)
        
        if left and right:
            return root  # p and q in different subtrees
        
        return left if left else right
    
    def lcaWithParentPointers(p, q):
        """LCA when nodes have parent pointers"""
        # Find depths
        def get_depth(node):
            depth = 0
            while node.parent:
                depth += 1
                node = node.parent
            return depth
        
        depth_p, depth_q = get_depth(p), get_depth(q)
        
        # Bring to same level
        while depth_p > depth_q:
            p = p.parent
            depth_p -= 1
        
        while depth_q > depth_p:
            q = q.parent
            depth_q -= 1
        
        # Find common ancestor
        while p != q:
            p = p.parent
            q = q.parent
        
        return p
    
    def maxPathSum(root):
        """Maximum path sum between any two nodes"""
        max_sum = float('-inf')
        
        def max_gain(node):
            nonlocal max_sum
            if not node:
                return 0
            
            # Max gain from left and right subtrees (ignore negative)
            left_gain = max(max_gain(node.left), 0)
            right_gain = max(max_gain(node.right), 0)
            
            # Price of current path (through current node)
            price_newpath = node.val + left_gain + right_gain
            max_sum = max(max_sum, price_newpath)
            
            # Return max gain if we continue path through current node
            return node.val + max(left_gain, right_gain)
        
        max_gain(root)
        return max_sum
    
    def pathSum(root, targetSum):
        """Find all root-to-leaf paths with given sum"""
        result = []
        
        def dfs(node, remaining, path):
            if not node:
                return
            
            path.append(node.val)
            remaining -= node.val
            
            # Check if we've reached a leaf with target sum
            if not node.left and not node.right and remaining == 0:
                result.append(path[:])  # Make a copy
            
            # Continue exploring
            dfs(node.left, remaining, path)
            dfs(node.right, remaining, path)
            
            path.pop()  # Backtrack
        
        dfs(root, targetSum, [])
        return result
    
    def pathSumIII(root, targetSum):
        """Count paths with given sum (not necessarily root to leaf)"""
        def count_paths(node, target):
            if not node:
                return 0
            
            # Paths starting from current node
            paths_from_root = 1 if node.val == target else 0
            paths_from_root += count_paths(node.left, target - node.val)
            paths_from_root += count_paths(node.right, target - node.val)
            
            return paths_from_root
        
        if not root:
            return 0
        
        # Paths starting from root + paths in subtrees
        return (count_paths(root, targetSum) + 
               pathSumIII(root.left, targetSum) + 
               pathSumIII(root.right, targetSum))
    
    def pathSumIIIOptimized(root, targetSum):
        """Optimized version using prefix sums"""
        from collections import defaultdict
        
        def dfs(node, current_sum, prefix_sums):
            if not node:
                return 0
            
            current_sum += node.val
            
            # Number of paths ending at current node
            paths = prefix_sums[current_sum - targetSum]
            
            # Add current prefix sum
            prefix_sums[current_sum] += 1
            
            # Explore children
            paths += dfs(node.left, current_sum, prefix_sums)
            paths += dfs(node.right, current_sum, prefix_sums)
            
            # Backtrack
            prefix_sums[current_sum] -= 1
            
            return paths
        
        prefix_sums = defaultdict(int)
        prefix_sums[0] = 1  # Empty path
        return dfs(root, 0, prefix_sums)
    
    def sumRootToLeaf(root):
        """Sum of all root-to-leaf numbers"""
        def dfs(node, current_number):
            if not node:
                return 0
            
            current_number = current_number * 10 + node.val
            
            # Leaf node
            if not node.left and not node.right:
                return current_number
            
            return dfs(node.left, current_number) + dfs(node.right, current_number)
        
        return dfs(root, 0)
    
    def isValidSequence(root, arr):
        """Check if array represents root-to-leaf path"""
        def dfs(node, arr, index):
            if not node or index >= len(arr) or node.val != arr[index]:
                return False
            
            # Leaf node - check if we've consumed entire array
            if not node.left and not node.right:
                return index == len(arr) - 1
            
            return (dfs(node.left, arr, index + 1) or 
                   dfs(node.right, arr, index + 1))
        
        return dfs(root, arr, 0) if arr else not root
```

**4. Tree Transformation Algorithms**

```python
def treeTransformations():
    """Advanced tree transformation and modification algorithms"""
    
    def invertTree(root):
        """Mirror/invert binary tree"""
        if not root:
            return None
        
        # Swap children
        root.left, root.right = root.right, root.left
        
        # Recursively invert subtrees
        invertTree(root.left)
        invertTree(root.right)
        
        return root
    
    def flattenToLinkedList(root):
        """Flatten tree to linked list in preorder"""
        def flatten_helper(node):
            if not node:
                return None
            
            # Leaf node
            if not node.left and not node.right:
                return node
            
            # Get tail of left and right subtrees
            left_tail = flatten_helper(node.left)
            right_tail = flatten_helper(node.right)
            
            # If left subtree exists, rewire connections
            if left_tail:
                left_tail.right = node.right
                node.right = node.left
                node.left = None
            
            # Return the tail of the flattened tree
            return right_tail if right_tail else left_tail
        
        flatten_helper(root)
        return root
    
    def flattenIterative(root):
        """Iterative flattening using stack"""
        if not root:
            return
        
        stack = [root]
        
        while stack:
            current = stack.pop()
            
            if current.right:
                stack.append(current.right)
            if current.left:
                stack.append(current.left)
            
            # Connect to next node (if exists)
            if stack:
                current.right = stack[-1]
            current.left = None
    
    def connectNextPointers(root):
        """Connect nodes at same level using next pointers"""
        if not root:
            return root
        
        # Start with the root level
        leftmost = root
        
        while leftmost.left:  # Process until we reach leaf level
            head = leftmost
            
            while head:
                # Connect children of current node
                head.left.next = head.right
                
                # Connect to next node's left child
                if head.next:
                    head.right.next = head.next.left
                
                head = head.next
            
            # Move to next level
            leftmost = leftmost.left
        
        return root
    
    def connectNextPointersII(root):
        """Connect next pointers for any binary tree"""
        def get_next_right_node(node):
            """Find next right node at same level"""
            while node:
                if node.left:
                    return node.left
                if node.right:
                    return node.right
                node = node.next
            return None
        
        if not root:
            return root
        
        node = root
        
        while node:
            # Find leftmost node of next level
            next_level_start = None
            if node.left:
                next_level_start = node.left
            elif node.right:
                next_level_start = node.right
            else:
                temp = node.next
                while temp and not next_level_start:
                    if temp.left:
                        next_level_start = temp.left
                    elif temp.right:
                        next_level_start = temp.right
                    temp = temp.next
            
            # Connect nodes at current level
            while node:
                if node.left:
                    if node.right:
                        node.left.next = node.right
                    else:
                        node.left.next = get_next_right_node(node.next)
                
                if node.right:
                    node.right.next = get_next_right_node(node.next)
                
                node = node.next
            
            node = next_level_start
        
        return root
    
    def pruneTree(root):
        """Remove subtrees that don't contain 1"""
        if not root:
            return None
        
        root.left = pruneTree(root.left)
        root.right = pruneTree(root.right)
        
        # Remove current node if it's 0 and has no children
        if root.val == 0 and not root.left and not root.right:
            return None
        
        return root
```

**5. Tree Validation & Properties**

```python
def treeValidationAlgorithms():
    """Algorithms for validating tree properties and structure"""
    
    def isSameTree(p, q):
        """Check if two trees are identical"""
        if not p and not q:
            return True
        if not p or not q:
            return False
        
        return (p.val == q.val and 
               isSameTree(p.left, q.left) and 
               isSameTree(p.right, q.right))
    
    def isSymmetric(root):
        """Check if tree is symmetric (mirror of itself)"""
        def is_mirror(left, right):
            if not left and not right:
                return True
            if not left or not right:
                return False
            
            return (left.val == right.val and
                   is_mirror(left.left, right.right) and
                   is_mirror(left.right, right.left))
        
        return is_mirror(root.left, root.right) if root else True
    
    def isSubtree(root, subRoot):
        """Check if subRoot is subtree of root"""
        if not subRoot:
            return True
        if not root:
            return False
        
        return (isSameTree(root, subRoot) or 
               isSubtree(root.left, subRoot) or 
               isSubtree(root.right, subRoot))
    
    def isCompleteTree(root):
        """Check if tree is complete"""
        if not root:
            return True
        
        from collections import deque
        queue = deque([root])
        end = False
        
        while queue:
            node = queue.popleft()
            
            if node:
                if end:
                    return False
                queue.append(node.left)
                queue.append(node.right)
            else:
                end = True
        
        return True
    
    def isBalanced(root):
        """Check if tree is height-balanced"""
        def check_balance(node):
            if not node:
                return True, -1
            
            left_balanced, left_height = check_balance(node.left)
            if not left_balanced:
                return False, 0
            
            right_balanced, right_height = check_balance(node.right)
            if not right_balanced:
                return False, 0
            
            balanced = abs(left_height - right_height) <= 1
            height = 1 + max(left_height, right_height)
            
            return balanced, height
        
        balanced, _ = check_balance(root)
        return balanced
    
    def maxDepth(root):
        """Calculate maximum depth of tree"""
        if not root:
            return 0
        
        return 1 + max(maxDepth(root.left), maxDepth(root.right))
    
    def minDepth(root):
        """Calculate minimum depth to a leaf"""
        if not root:
            return 0
        
        # If only one child exists
        if not root.left:
            return 1 + minDepth(root.right)
        if not root.right:
            return 1 + minDepth(root.left)
        
        # Both children exist
        return 1 + min(minDepth(root.left), minDepth(root.right))
    
    def countNodes(root):
        """Count nodes in complete binary tree - O(log²n)"""
        if not root:
            return 0
        
        def get_height(node, go_left=True):
            height = 0
            while node:
                height += 1
                node = node.left if go_left else node.right
            return height
        
        left_height = get_height(root, True)
        right_height = get_height(root, False)
        
        if left_height == right_height:
            # Perfect binary tree
            return 2**left_height - 1
        
        return 1 + countNodes(root.left) + countNodes(root.right)
```

### Performance Analysis & Optimization

**Complexity Analysis**:
```python
def treeComplexityAnalysis():
    """
    Binary Tree Complexity Analysis:
    
    Time Complexities:
    - Traversal (DFS/BFS): O(n) - must visit all nodes
    - Search in balanced tree: O(log n)
    - Search in skewed tree: O(n)
    - Height calculation: O(n)
    - Path finding: O(n) worst case
    
    Space Complexities:
    - Recursive traversal: O(h) stack space, where h is height
    - Iterative traversal: O(h) for explicit stack
    - Morris traversal: O(1) space, O(n) time
    - Level-order traversal: O(w) where w is maximum width
    
    Tree Balance Impact:
    - Balanced tree: Height = O(log n), operations are O(log n)
    - Skewed tree: Height = O(n), operations degrade to O(n)
    
    Memory Layout:
    - Each node: data + 2 pointers (16-24 bytes on 64-bit systems)
    - Tree with n nodes: approximately n * 20 bytes
    - Poor cache locality due to random memory allocation
    """
    pass

def treeOptimizations():
    """Advanced optimization techniques for tree algorithms"""
    
    def iterativePostorder(root):
        """Most complex iterative traversal - multiple optimized versions"""
        
        # Version 1: Using last visited pointer
        def version1(root):
            if not root:
                return []
            
            result = []
            stack = []
            last_visited = None
            current = root
            
            while stack or current:
                if current:
                    stack.append(current)
                    current = current.left
                else:
                    peek_node = stack[-1]
                    if peek_node.right and last_visited != peek_node.right:
                        current = peek_node.right
                    else:
                        result.append(peek_node.val)
                        last_visited = stack.pop()
            
            return result
        
        # Version 2: Reverse preorder
        def version2(root):
            if not root:
                return []
            
            stack = [root]
            result = []
            
            while stack:
                node = stack.pop()
                result.append(node.val)
                
                if node.left:
                    stack.append(node.left)
                if node.right:
                    stack.append(node.right)
            
            return result[::-1]  # Reverse to get postorder
        
        return version1(root), version2(root)
```

### Questions from Top 150 (68–81)
- ✅ Maximum Depth of Binary Tree - Height calculation using recursion
- ✅ Same Tree - Structural and value comparison using DFS
- ✅ Invert Binary Tree - Tree mirroring with recursive swapping
- ✅ Symmetric Tree - Mirror validation using dual traversal
- ✅ Construct Binary Tree from Preorder and Inorder Traversal - Advanced reconstruction
- ✅ Construct Binary Tree from Inorder and Postorder Traversal - Alternative reconstruction
- ✅ Populating Next Right Pointers in Each Node II - Level connection for arbitrary trees
- ✅ Flatten Binary Tree to Linked List - Tree linearization using preorder
- ✅ Path Sum - Root-to-leaf path validation with target sum
- ✅ Sum Root to Leaf Numbers - Numeric path calculation and summation
- ✅ Binary Tree Maximum Path Sum - Global maximum path with node-to-node optimization
- ✅ Binary Search Tree Iterator - In-order iteration with O(1) amortized access
- ✅ Count Complete Tree Nodes - Optimized counting using complete tree properties
- ✅ Lowest Common Ancestor of a Binary Tree - Fundamental ancestor finding algorithm

### Binary Tree Mastery Framework
1. **Master traversals**: Recursive and iterative implementations of all three DFS orders
2. **Understand recursion**: Most tree problems have elegant recursive solutions
3. **Practice tree construction**: Building trees from different traversal combinations
4. **Path algorithms**: Master various path-finding and path-sum patterns
5. **Tree validation**: Learn to verify structural and property constraints
6. **Optimization techniques**: Morris traversal, iterative approaches, space optimization

**Pattern Recognition Guide**:
- **Tree traversal needed** → Choose appropriate DFS/BFS order
- **Path-related problems** → Use DFS with backtracking
- **Level-by-level processing** → Use BFS/level-order traversal
- **Tree construction** → Use preorder/inorder or inorder/postorder combinations
- **Tree validation** → Use property-checking algorithms
- **Ancestor problems** → Use LCA or parent pointer techniques

---

## 10. Binary Tree BFS

### Core Theory & Queue-Based Algorithms

**Breadth-First Search (BFS)** on trees explores nodes level by level from left to right, using a **queue** data structure to maintain the frontier of exploration. BFS guarantees that nodes are processed in order of their distance from the root, making it ideal for level-based problems and shortest path scenarios in unweighted structures.

**Mathematical Properties of BFS**:
- **Time Complexity**: O(n) - each node visited exactly once
- **Space Complexity**: O(w) where w is maximum width of tree
- **Queue Size**: Varies from 1 (root level) to maximum width (typically around n/2 for complete trees)
- **Level Processing**: Guarantees complete level processing before moving to next level

**Memory Layout & Cache Behavior**:
```
BFS Queue Evolution (Tree: 1->2,3->4,5,6,7):
Level 0: [1]           Process: 1        Queue after: [2,3]
Level 1: [2,3]         Process: 2,3      Queue after: [4,5,6,7]  
Level 2: [4,5,6,7]     Process: 4,5,6,7  Queue after: []

Cache Efficiency: Good locality within levels, poor between levels
```

### Fundamental BFS Patterns & Templates

**1. Core BFS Templates**

```python
def bfsFoundations():
    """Comprehensive BFS template patterns for trees"""
    
    def levelOrderTraversal(root):
        """Basic level-order traversal - foundation template"""
        if not root:
            return []
        
        from collections import deque
        result = []
        queue = deque([root])
        
        while queue:
            level_size = len(queue)  # Current level size
            current_level = []
            
            # Process all nodes at current level
            for _ in range(level_size):
                node = queue.popleft()
                current_level.append(node.val)
                
                # Add children to queue for next level
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            
            result.append(current_level)
        
        return result
    
    def levelOrderFlat(root):
        """Flattened level-order traversal"""
        if not root:
            return []
        
        from collections import deque
        result = []
        queue = deque([root])
        
        while queue:
            node = queue.popleft()
            result.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        return result
    
    def levelOrderWithDepth(root):
        """BFS with depth tracking"""
        if not root:
            return []
        
        from collections import deque
        result = []
        queue = deque([(root, 0)])  # (node, depth)
        
        while queue:
            node, depth = queue.popleft()
            
            # Extend result if needed
            if depth >= len(result):
                result.append([])
            
            result[depth].append(node.val)
            
            if node.left:
                queue.append((node.left, depth + 1))
            if node.right:
                queue.append((node.right, depth + 1))
        
        return result
    
    def levelOrderBottomUp(root):
        """Level order from bottom to top"""
        if not root:
            return []
        
        from collections import deque
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
        
        return result[::-1]  # Reverse for bottom-up
```

**2. Advanced Level Processing Algorithms**

```python
def advancedLevelProcessing():
    """Sophisticated level-by-level processing techniques"""
    
    def rightSideView(root):
        """Right side view - multiple approaches"""
        
        # Approach 1: BFS with level tracking
        def bfs_approach(root):
            if not root:
                return []
            
            from collections import deque
            result = []
            queue = deque([root])
            
            while queue:
                level_size = len(queue)
                
                for i in range(level_size):
                    node = queue.popleft()
                    
                    # Only add the last node of each level
                    if i == level_size - 1:
                        result.append(node.val)
                    
                    if node.left:
                        queue.append(node.left)
                    if node.right:
                        queue.append(node.right)
            
            return result
        
        # Approach 2: DFS with depth tracking
        def dfs_approach(root):
            result = []
            
            def dfs(node, depth):
                if not node:
                    return
                
                # First node we see at this depth is the rightmost
                if depth == len(result):
                    result.append(node.val)
                
                # Process right first, then left
                dfs(node.right, depth + 1)
                dfs(node.left, depth + 1)
            
            dfs(root, 0)
            return result
        
        return bfs_approach(root), dfs_approach(root)
    
    def leftSideView(root):
        """Left side view using BFS"""
        if not root:
            return []
        
        from collections import deque
        result = []
        queue = deque([root])
        
        while queue:
            level_size = len(queue)
            
            for i in range(level_size):
                node = queue.popleft()
                
                # Only add the first node of each level
                if i == 0:
                    result.append(node.val)
                
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        
        return result
    
    def verticalOrderTraversal(root):
        """Vertical order traversal - nodes at same column"""
        if not root:
            return []
        
        from collections import deque, defaultdict
        
        # BFS with column tracking
        column_map = defaultdict(list)
        queue = deque([(root, 0)])  # (node, column)
        
        while queue:
            node, col = queue.popleft()
            column_map[col].append(node.val)
            
            if node.left:
                queue.append((node.left, col - 1))
            if node.right:
                queue.append((node.right, col + 1))
        
        # Sort by column index and return values
        result = []
        for col in sorted(column_map.keys()):
            result.append(column_map[col])
        
        return result
    
    def verticalOrderTraversalII(root):
        """Enhanced vertical order with row-column sorting"""
        if not root:
            return []
        
        from collections import deque, defaultdict
        
        # Track (row, column, value) for proper sorting
        node_list = []
        queue = deque([(root, 0, 0)])  # (node, row, col)
        
        while queue:
            node, row, col = queue.popleft()
            node_list.append((col, row, node.val))
            
            if node.left:
                queue.append((node.left, row + 1, col - 1))
            if node.right:
                queue.append((node.right, row + 1, col + 1))
        
        # Sort by column, then row, then value
        node_list.sort(key=lambda x: (x[0], x[1], x[2]))
        
        # Group by column
        result = []
        column_map = defaultdict(list)
        
        for col, row, val in node_list:
            column_map[col].append(val)
        
        for col in sorted(column_map.keys()):
            result.append(column_map[col])
        
        return result
    
    def zigzagLevelOrder(root):
        """Zigzag level order traversal - alternating directions"""
        if not root:
            return []
        
        from collections import deque
        result = []
        queue = deque([root])
        left_to_right = True
        
        while queue:
            level_size = len(queue)
            current_level = deque()
            
            for _ in range(level_size):
                node = queue.popleft()
                
                # Add to appropriate end based on direction
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
    
    def averageOfLevels(root):
        """Calculate average value at each level"""
        if not root:
            return []
        
        from collections import deque
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
    
    def largestValueInEachRow(root):
        """Find largest value in each tree row"""
        if not root:
            return []
        
        from collections import deque
        result = []
        queue = deque([root])
        
        while queue:
            level_size = len(queue)
            max_val = float('-inf')
            
            for _ in range(level_size):
                node = queue.popleft()
                max_val = max(max_val, node.val)
                
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            
            result.append(max_val)
        
        return result
```

**3. Distance & Path-Based BFS Algorithms**

```python
def distancePathBFS():
    """BFS algorithms for distance and path finding"""
    
    def findBottomLeftValue(root):
        """Find leftmost value in the last row"""
        from collections import deque
        
        queue = deque([root])
        leftmost = root.val
        
        while queue:
            level_size = len(queue)
            
            for i in range(level_size):
                node = queue.popleft()
                
                # Update leftmost for each level
                if i == 0:
                    leftmost = node.val
                
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        
        return leftmost
    
    def distanceK(root, target, k):
        """Find all nodes at distance k from target node"""
        if k == 0:
            return [target.val]
        
        from collections import deque, defaultdict
        
        # Build parent mapping using BFS
        parent = {}
        queue = deque([root])
        
        while queue:
            node = queue.popleft()
            
            if node.left:
                parent[node.left] = node
                queue.append(node.left)
            if node.right:
                parent[node.right] = node
                queue.append(node.right)
        
        # BFS from target node
        visited = set([target])
        queue = deque([target])
        distance = 0
        
        while queue and distance < k:
            level_size = len(queue)
            distance += 1
            
            for _ in range(level_size):
                node = queue.popleft()
                
                # Explore all adjacent nodes (left, right, parent)
                for adjacent in [node.left, node.right, parent.get(node)]:
                    if adjacent and adjacent not in visited:
                        visited.add(adjacent)
                        queue.append(adjacent)
        
        # Return nodes at exact distance k
        return [node.val for node in queue]
    
    def widthOfBinaryTree(root):
        """Find maximum width of binary tree"""
        if not root:
            return 0
        
        from collections import deque
        
        max_width = 0
        queue = deque([(root, 0)])  # (node, position)
        
        while queue:
            level_size = len(queue)
            level_start = queue[0][1]  # Position of first node in level
            
            for _ in range(level_size):
                node, pos = queue.popleft()
                
                if node.left:
                    queue.append((node.left, 2 * pos))
                if node.right:
                    queue.append((node.right, 2 * pos + 1))
            
            # Width of current level
            level_end = pos  # Position of last processed node
            current_width = level_end - level_start + 1
            max_width = max(max_width, current_width)
        
        return max_width
    
    def minDepth(root):
        """Find minimum depth using BFS (optimal for this problem)"""
        if not root:
            return 0
        
        from collections import deque
        
        queue = deque([(root, 1)])  # (node, depth)
        
        while queue:
            node, depth = queue.popleft()
            
            # First leaf node we encounter has minimum depth
            if not node.left and not node.right:
                return depth
            
            if node.left:
                queue.append((node.left, depth + 1))
            if node.right:
                queue.append((node.right, depth + 1))
        
        return 0
    
    def isCousins(root, x, y):
        """Check if two nodes are cousins (same depth, different parents)"""
        from collections import deque
        
        if not root:
            return False
        
        queue = deque([root])
        
        while queue:
            level_size = len(queue)
            found_x = found_y = False
            
            for _ in range(level_size):
                node = queue.popleft()
                
                # Check if current node is parent of both x and y
                if ((node.left and node.left.val == x and 
                     node.right and node.right.val == y) or
                    (node.left and node.left.val == y and 
                     node.right and node.right.val == x)):
                    return False  # Same parent
                
                if node.left:
                    if node.left.val == x:
                        found_x = True
                    elif node.left.val == y:
                        found_y = True
                    queue.append(node.left)
                
                if node.right:
                    if node.right.val == x:
                        found_x = True
                    elif node.right.val == y:
                        found_y = True
                    queue.append(node.right)
            
            # If both found at same level with different parents
            if found_x and found_y:
                return True
            # If only one found, they're not at same level
            if found_x or found_y:
                return False
        
        return False
```

**4. Tree Reconstruction Using BFS**

```python
def bfsTreeConstruction():
    """Tree construction and modification using BFS principles"""
    
    def connectNextPointers(root):
        """Connect next right pointers using BFS"""
        if not root:
            return root
        
        from collections import deque
        
        queue = deque([root])
        
        while queue:
            level_size = len(queue)
            
            for i in range(level_size):
                node = queue.popleft()
                
                # Connect to next node in same level
                if i < level_size - 1:
                    node.next = queue[0]  # Peek at next node
                
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
        
        return root
    
    def connectNextPointersConstantSpace(root):
        """Connect next pointers using O(1) extra space"""
        if not root:
            return root
        
        # Start from root level
        leftmost = root
        
        while leftmost:
            # Iterate through current level using next pointers
            head = leftmost
            leftmost = None  # Will be set to first node of next level
            
            while head:
                # Process left child
                if head.left:
                    if leftmost:
                        prev.next = head.left
                    else:
                        leftmost = head.left
                    prev = head.left
                
                # Process right child
                if head.right:
                    if leftmost:
                        prev.next = head.right
                    else:
                        leftmost = head.right
                    prev = head.right
                
                # Move to next node in current level
                head = head.next
        
        return root
    
    def buildTreeFromLevelOrder(arr):
        """Build tree from level-order array representation"""
        if not arr or arr[0] is None:
            return None
        
        from collections import deque
        
        root = TreeNode(arr[0])
        queue = deque([root])
        i = 1
        
        while queue and i < len(arr):
            node = queue.popleft()
            
            # Add left child
            if i < len(arr) and arr[i] is not None:
                node.left = TreeNode(arr[i])
                queue.append(node.left)
            i += 1
            
            # Add right child
            if i < len(arr) and arr[i] is not None:
                node.right = TreeNode(arr[i])
                queue.append(node.right)
            i += 1
        
        return root
    
    def treeToArray(root):
        """Convert tree to level-order array representation"""
        if not root:
            return []
        
        from collections import deque
        
        result = []
        queue = deque([root])
        
        while queue:
            node = queue.popleft()
            
            if node:
                result.append(node.val)
                queue.append(node.left)
                queue.append(node.right)
            else:
                result.append(None)
        
        # Remove trailing nulls
        while result and result[-1] is None:
            result.pop()
        
        return result
```

**5. Advanced BFS Applications**

```python
def advancedBFSApplications():
    """Complex BFS applications and optimizations"""
    
    def sumOfLeftLeaves(root):
        """Sum of all left leaves using BFS"""
        if not root:
            return 0
        
        from collections import deque
        
        total = 0
        queue = deque([(root, False)])  # (node, is_left_child)
        
        while queue:
            node, is_left = queue.popleft()
            
            # Check if it's a left leaf
            if is_left and not node.left and not node.right:
                total += node.val
            
            if node.left:
                queue.append((node.left, True))
            if node.right:
                queue.append((node.right, False))
        
        return total
    
    def findLeaves(root):
        """Find leaves and remove them iteratively"""
        if not root:
            return []
        
        result = []
        
        while root:
            leaves = []
            root = self.removeLeaves(root, leaves)
            result.append(leaves)
        
        return result
    
    def removeLeaves(self, node, leaves):
        """Helper to remove leaves and collect their values"""
        if not node:
            return None
        
        if not node.left and not node.right:
            leaves.append(node.val)
            return None
        
        node.left = self.removeLeaves(node.left, leaves)
        node.right = self.removeLeaves(node.right, leaves)
        
        return node
    
    def printTreeLevelByLevel(root):
        """Pretty print tree level by level"""
        if not root:
            return
        
        from collections import deque
        
        queue = deque([root])
        level = 0
        
        while queue:
            level_size = len(queue)
            level_nodes = []
            
            print(f"Level {level}: ", end="")
            
            for i in range(level_size):
                node = queue.popleft()
                level_nodes.append(str(node.val))
                
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            
            print(" -> ".join(level_nodes))
            level += 1
    
    def bfsWithMultipleQueues(root):
        """BFS using multiple queues for complex processing"""
        if not root:
            return []
        
        from collections import deque
        
        current_level = deque([root])
        next_level = deque()
        result = []
        
        while current_level:
            level_values = []
            
            while current_level:
                node = current_level.popleft()
                level_values.append(node.val)
                
                if node.left:
                    next_level.append(node.left)
                if node.right:
                    next_level.append(node.right)
            
            result.append(level_values)
            current_level, next_level = next_level, deque()
        
        return result
```

### BFS vs DFS Comparison & When to Use

**Performance Analysis**:

| Aspect | BFS | DFS |
|--------|-----|-----|
| **Space Complexity** | O(w) - width | O(h) - height |
| **Best for** | Level processing | Path finding |
| **Memory Pattern** | Breadth expansion | Depth expansion |
| **Implementation** | Queue (iterative) | Stack/Recursion |

**BFS Advantages**:
- Guarantees shortest path in unweighted graphs
- Processes nodes by distance from root
- Optimal for level-based problems
- Better for wide, shallow trees

**DFS Advantages**:
- More memory efficient for deep trees
- Natural recursion fits tree structure
- Better for path-based problems
- Easier to implement for most tree problems

### Advanced BFS Optimizations

```python
def bfsOptimizations():
    """Advanced optimization techniques for BFS"""
    
    def memoryOptimizedBFS(root):
        """Reduce memory usage in BFS"""
        if not root:
            return []
        
        # Use indices instead of storing actual nodes
        from collections import deque
        
        # Convert tree to array first
        node_array = []
        queue = deque([root])
        
        while queue:
            node = queue.popleft()
            if node:
                node_array.append(node.val)
                queue.append(node.left)
                queue.append(node.right)
            else:
                node_array.append(None)
        
        # Process using indices
        result = []
        level_start = 0
        level_size = 1
        
        while level_start < len(node_array):
            level = []
            for i in range(level_start, min(level_start + level_size, len(node_array))):
                if node_array[i] is not None:
                    level.append(node_array[i])
            
            if level:
                result.append(level)
            
            level_start += level_size
            level_size *= 2
        
        return result
    
    def bidirectionalBFS(root, target):
        """Bidirectional BFS for finding shortest path"""
        if not root:
            return -1
        
        from collections import deque
        
        # BFS from root
        queue1 = deque([(root, 0)])
        visited1 = {root: 0}
        
        # BFS from target (if we can identify target's position)
        # This is more applicable to graph problems
        # For tree problems, regular BFS is usually sufficient
        
        while queue1:
            node, dist = queue1.popleft()
            
            if node.val == target:
                return dist
            
            for child in [node.left, node.right]:
                if child and child not in visited1:
                    visited1[child] = dist + 1
                    queue1.append((child, dist + 1))
        
        return -1
```

### Questions from Top 150 (82–85)
- ✅ Binary Tree Right Side View - Level-based rightmost node extraction
- ✅ Average of Levels in Binary Tree - Statistical analysis using level-order traversal  
- ✅ Binary Tree Level Order Traversal - Fundamental BFS template application
- ✅ Binary Tree Zigzag Level Order Traversal - Alternating direction processing with deque

### BFS Mastery Framework
1. **Master the queue pattern**: Level-by-level processing using queue size tracking
2. **Understand space complexity**: Queue grows to maximum tree width  
3. **Level-based thinking**: Identify when problems require complete level processing
4. **Direction variations**: Handle forward, reverse, and alternating traversals
5. **Distance problems**: Use BFS for shortest path in unweighted scenarios
6. **Optimization techniques**: Memory reduction and bidirectional approaches

**Pattern Recognition Guide**:
- **"Level order" or "by levels"** → Standard BFS template
- **"Distance k" or "shortest path"** → BFS with distance tracking
- **"Right/left side view"** → Process last/first node of each level
- **"Width" or "diameter"** → Level-wise measurement problems
- **"Average/sum by level"** → Statistical processing during traversal
- **"Connect nodes"** → Level-based linking using next pointers

---

## 11. Binary Search Tree

### Core Theory & Mathematical Foundation

A **Binary Search Tree (BST)** is a specialized binary tree that maintains the **BST invariant**: for every node, all values in the left subtree are less than the node's value, and all values in the right subtree are greater than the node's value. This property enables efficient search, insertion, and deletion operations.

**Mathematical Properties**:
- **Search/Insert/Delete Time**: O(h) where h is tree height
- **Best Case Height**: h = ⌊log₂(n)⌋ for balanced BST (O(log n) operations)
- **Worst Case Height**: h = n-1 for completely skewed BST (O(n) operations)
- **Average Case**: O(log n) for random insertion sequences
- **Inorder Traversal**: Always produces sorted sequence

**BST Invariant Verification**:
```
Valid BST:        Invalid BST:
    5                 5
   / \               / \
  3   8             3   8
 / \ / \           / \ / \
2  4 6  9         2  6 4  9
                     ↑ Violates BST property (6 > 5)
```

**Memory & Performance Characteristics**:
- **Space**: O(n) for n nodes, same as regular binary tree
- **Cache Performance**: Poor for random access, good for sequential (inorder)
- **Balance Factor**: Critical for performance - unbalanced BSTs degrade to O(n)

### Fundamental BST Operations

**1. Core CRUD Operations**

```python
def bstFundamentals():
    """Complete BST implementation with all fundamental operations"""
    
    class TreeNode:
        def __init__(self, val=0, left=None, right=None):
            self.val = val
            self.left = left
            self.right = right
    
    class BST:
        def __init__(self):
            self.root = None
            self.size = 0
        
        def search(self, val):
            """Search for value in BST - O(h)"""
            def search_recursive(node, val):
                if not node or node.val == val:
                    return node
                
                if val < node.val:
                    return search_recursive(node.left, val)
                else:
                    return search_recursive(node.right, val)
            
            return search_recursive(self.root, val)
        
        def search_iterative(self, val):
            """Iterative search - more memory efficient"""
            current = self.root
            
            while current:
                if val == current.val:
                    return current
                elif val < current.val:
                    current = current.left
                else:
                    current = current.right
            
            return None
        
        def insert(self, val):
            """Insert value into BST - O(h)"""
            def insert_recursive(node, val):
                if not node:
                    self.size += 1
                    return TreeNode(val)
                
                if val < node.val:
                    node.left = insert_recursive(node.left, val)
                elif val > node.val:
                    node.right = insert_recursive(node.right, val)
                # If val == node.val, we don't insert duplicates
                
                return node
            
            self.root = insert_recursive(self.root, val)
        
        def insert_iterative(self, val):
            """Iterative insertion - avoids recursion stack"""
            if not self.root:
                self.root = TreeNode(val)
                self.size += 1
                return
            
            current = self.root
            parent = None
            
            # Find insertion point
            while current:
                parent = current
                if val < current.val:
                    current = current.left
                elif val > current.val:
                    current = current.right
                else:
                    return  # Duplicate value, don't insert
            
            # Insert new node
            new_node = TreeNode(val)
            if val < parent.val:
                parent.left = new_node
            else:
                parent.right = new_node
            
            self.size += 1
        
        def delete(self, val):
            """Delete value from BST - O(h)"""
            def find_min(node):
                """Find minimum value in subtree"""
                while node.left:
                    node = node.left
                return node
            
            def delete_recursive(node, val):
                if not node:
                    return node
                
                if val < node.val:
                    node.left = delete_recursive(node.left, val)
                elif val > node.val:
                    node.right = delete_recursive(node.right, val)
                else:
                    # Node to be deleted found
                    self.size -= 1
                    
                    # Case 1: No children (leaf node)
                    if not node.left and not node.right:
                        return None
                    
                    # Case 2: One child
                    elif not node.left:
                        return node.right
                    elif not node.right:
                        return node.left
                    
                    # Case 3: Two children
                    else:
                        # Find inorder successor (smallest in right subtree)
                        successor = find_min(node.right)
                        node.val = successor.val
                        node.right = delete_recursive(node.right, successor.val)
                
                return node
            
            self.root = delete_recursive(self.root, val)
        
        def find_min(self):
            """Find minimum value in BST - O(h)"""
            if not self.root:
                return None
            
            current = self.root
            while current.left:
                current = current.left
            
            return current.val
        
        def find_max(self):
            """Find maximum value in BST - O(h)"""
            if not self.root:
                return None
            
            current = self.root
            while current.right:
                current = current.right
            
            return current.val
        
        def inorder_traversal(self):
            """Get sorted values via inorder traversal - O(n)"""
            result = []
            
            def inorder(node):
                if node:
                    inorder(node.left)
                    result.append(node.val)
                    inorder(node.right)
            
            inorder(self.root)
            return result
```

**2. Advanced BST Queries**

```python
def advancedBSTQueries():
    """Advanced BST query operations and algorithms"""
    
    def kthSmallest(root, k):
        """Find kth smallest element - multiple approaches"""
        
        # Approach 1: Inorder traversal with early termination
        def kth_inorder(root, k):
            def inorder(node):
                nonlocal count, result
                if not node or result is not None:
                    return
                
                inorder(node.left)
                
                count += 1
                if count == k:
                    result = node.val
                    return
                
                inorder(node.right)
            
            count = 0
            result = None
            inorder(root)
            return result
        
        # Approach 2: Iterative inorder with stack
        def kth_iterative(root, k):
            stack = []
            current = root
            
            while current or stack:
                # Go to leftmost node
                while current:
                    stack.append(current)
                    current = current.left
                
                # Process current node
                current = stack.pop()
                k -= 1
                if k == 0:
                    return current.val
                
                # Move to right subtree
                current = current.right
            
            return None
        
        # Approach 3: Morris traversal - O(1) space
        def kth_morris(root, k):
            current = root
            count = 0
            
            while current:
                if not current.left:
                    count += 1
                    if count == k:
                        return current.val
                    current = current.right
                else:
                    # Find inorder predecessor
                    pred = current.left
                    while pred.right and pred.right != current:
                        pred = pred.right
                    
                    if not pred.right:
                        pred.right = current
                        current = current.left
                    else:
                        pred.right = None
                        count += 1
                        if count == k:
                            return current.val
                        current = current.right
            
            return None
        
        return kth_iterative(root, k)
    
    def kthLargest(root, k):
        """Find kth largest element (reverse inorder)"""
        def reverse_inorder(node):
            nonlocal count, result
            if not node or result is not None:
                return
            
            reverse_inorder(node.right)  # Visit right first
            
            count += 1
            if count == k:
                result = node.val
                return
            
            reverse_inorder(node.left)   # Then left
        
        count = 0
        result = None
        reverse_inorder(root)
        return result
    
    def findRange(root, low, high):
        """Find all values in range [low, high]"""
        result = []
        
        def range_search(node):
            if not node:
                return
            
            # If node's value is greater than low, explore left subtree
            if node.val > low:
                range_search(node.left)
            
            # If node's value is in range, include it
            if low <= node.val <= high:
                result.append(node.val)
            
            # If node's value is less than high, explore right subtree
            if node.val < high:
                range_search(node.right)
        
        range_search(root)
        return result
    
    def rangeSumBST(root, low, high):
        """Sum of all values in range [low, high]"""
        def range_sum(node):
            if not node:
                return 0
            
            total = 0
            
            # Include current node if in range
            if low <= node.val <= high:
                total += node.val
            
            # Explore left subtree if current value > low
            if node.val > low:
                total += range_sum(node.left)
            
            # Explore right subtree if current value < high
            if node.val < high:
                total += range_sum(node.right)
            
            return total
        
        return range_sum(root)
    
    def closestValue(root, target):
        """Find value closest to target in BST"""
        closest = root.val
        
        while root:
            # Update closest if current is closer
            if abs(root.val - target) < abs(closest - target):
                closest = root.val
            
            # Move to more promising subtree
            if target < root.val:
                root = root.left
            else:
                root = root.right
        
        return closest
    
    def closestKValues(root, target, k):
        """Find k values closest to target"""
        import heapq
        
        def inorder(node):
            if not node:
                return
            
            inorder(node.left)
            
            # Use max heap to maintain k closest values
            if len(heap) < k:
                heapq.heappush(heap, (-abs(node.val - target), node.val))
            else:
                # If current is closer than farthest in heap
                if abs(node.val - target) < -heap[0][0]:
                    heapq.heapreplace(heap, (-abs(node.val - target), node.val))
            
            inorder(node.right)
        
        heap = []
        inorder(root)
        
        return [val for _, val in heap]
```

**3. BST Validation & Properties**

```python
def bstValidation():
    """BST validation and property checking algorithms"""
    
    def isValidBST(root):
        """Validate BST property - multiple approaches"""
        
        # Approach 1: Recursive with bounds
        def validate_bounds(node, min_val, max_val):
            if not node:
                return True
            
            if node.val <= min_val or node.val >= max_val:
                return False
            
            return (validate_bounds(node.left, min_val, node.val) and
                   validate_bounds(node.right, node.val, max_val))
        
        # Approach 2: Inorder traversal check
        def validate_inorder(node):
            def inorder(node):
                if not node:
                    return True
                
                if not inorder(node.left):
                    return False
                
                if self.prev is not None and self.prev >= node.val:
                    return False
                self.prev = node.val
                
                return inorder(node.right)
            
            self.prev = None
            return inorder(node)
        
        # Approach 3: Iterative inorder
        def validate_iterative(node):
            stack = []
            prev = float('-inf')
            
            while stack or node:
                while node:
                    stack.append(node)
                    node = node.left
                
                node = stack.pop()
                if node.val <= prev:
                    return False
                prev = node.val
                
                node = node.right
            
            return True
        
        return validate_bounds(root, float('-inf'), float('inf'))
    
    def findMode(root):
        """Find mode(s) in BST using inorder property"""
        def inorder(node):
            nonlocal prev, count, max_count, result
            
            if not node:
                return
            
            inorder(node.left)
            
            # Process current node
            if prev is not None:
                if node.val == prev:
                    count += 1
                else:
                    count = 1
            else:
                count = 1
            
            if count > max_count:
                max_count = count
                result = [node.val]
            elif count == max_count:
                result.append(node.val)
            
            prev = node.val
            inorder(node.right)
        
        prev = None
        count = 0
        max_count = 0
        result = []
        
        inorder(root)
        return result
    
    def minDiffInBST(root):
        """Find minimum absolute difference between any two nodes"""
        def inorder(node):
            nonlocal prev, min_diff
            
            if not node:
                return
            
            inorder(node.left)
            
            if prev is not None:
                min_diff = min(min_diff, node.val - prev)
            prev = node.val
            
            inorder(node.right)
        
        prev = None
        min_diff = float('inf')
        inorder(root)
        return min_diff
    
    def isCompleteTreeBST(root):
        """Check if BST is also a complete binary tree"""
        if not root:
            return True
        
        from collections import deque
        
        queue = deque([root])
        end_reached = False
        
        while queue:
            node = queue.popleft()
            
            if not node:
                end_reached = True
            else:
                if end_reached:
                    return False
                
                queue.append(node.left)
                queue.append(node.right)
        
        # Also verify BST property
        return isValidBST(root)
```

**4. BST Construction & Conversion**

```python
def bstConstructionAlgorithms():
    """BST construction and conversion algorithms"""
    
    def sortedArrayToBST(nums):
        """Convert sorted array to height-balanced BST"""
        def build_tree(left, right):
            if left > right:
                return None
            
            # Choose middle element as root for balance
            mid = left + (right - left) // 2
            root = TreeNode(nums[mid])
            
            # Recursively build left and right subtrees
            root.left = build_tree(left, mid - 1)
            root.right = build_tree(mid + 1, right)
            
            return root
        
        return build_tree(0, len(nums) - 1)
    
    def sortedListToBST(head):
        """Convert sorted linked list to height-balanced BST"""
        def get_size(node):
            count = 0
            while node:
                count += 1
                node = node.next
            return count
        
        def build_tree(size):
            nonlocal head
            if size <= 0:
                return None
            
            # Build left subtree
            left = build_tree(size // 2)
            
            # Create root with current head value
            root = TreeNode(head.val)
            root.left = left
            head = head.next
            
            # Build right subtree
            root.right = build_tree(size - size // 2 - 1)
            
            return root
        
        size = get_size(head)
        return build_tree(size)
    
    def bstToGST(root):
        """Convert BST to Greater Sum Tree"""
        def reverse_inorder(node):
            nonlocal total
            if not node:
                return
            
            # Process right subtree first
            reverse_inorder(node.right)
            
            # Update current node
            total += node.val
            node.val = total
            
            # Process left subtree
            reverse_inorder(node.left)
        
        total = 0
        reverse_inorder(root)
        return root
    
    def balanceBST(root):
        """Balance an unbalanced BST"""
        def inorder(node):
            if not node:
                return []
            return inorder(node.left) + [node.val] + inorder(node.right)
        
        def build_balanced(values):
            if not values:
                return None
            
            mid = len(values) // 2
            root = TreeNode(values[mid])
            root.left = build_balanced(values[:mid])
            root.right = build_balanced(values[mid + 1:])
            return root
        
        sorted_values = inorder(root)
        return build_balanced(sorted_values)
    
    def mergeTwoBSTs(root1, root2):
        """Merge two BSTs into a balanced BST"""
        def inorder(node):
            if not node:
                return []
            return inorder(node.left) + [node.val] + inorder(node.right)
        
        def merge_sorted_arrays(arr1, arr2):
            merged = []
            i = j = 0
            
            while i < len(arr1) and j < len(arr2):
                if arr1[i] <= arr2[j]:
                    merged.append(arr1[i])
                    i += 1
                else:
                    merged.append(arr2[j])
                    j += 1
            
            merged.extend(arr1[i:])
            merged.extend(arr2[j:])
            return merged
        
        values1 = inorder(root1)
        values2 = inorder(root2)
        merged_values = merge_sorted_arrays(values1, values2)
        
        return sortedArrayToBST(merged_values)
```

**5. Advanced BST Data Structures**

```python
def advancedBSTStructures():
    """Advanced BST-based data structures and algorithms"""
    
    class BSTIterator:
        """Implement iterator over BST in sorted order"""
        
        def __init__(self, root):
            self.stack = []
            self._push_left_path(root)
        
        def _push_left_path(self, node):
            """Push all left nodes to stack"""
            while node:
                self.stack.append(node)
                node = node.left
        
        def next(self):
            """Return next smallest element - O(1) amortized"""
            node = self.stack.pop()
            
            # If right subtree exists, push its left path
            if node.right:
                self._push_left_path(node.right)
            
            return node.val
        
        def hasNext(self):
            """Check if more elements exist - O(1)"""
            return len(self.stack) > 0
    
    class BSTSet:
        """Set implementation using BST"""
        
        def __init__(self):
            self.root = None
            self._size = 0
        
        def add(self, val):
            """Add element to set"""
            def insert(node, val):
                if not node:
                    self._size += 1
                    return TreeNode(val)
                
                if val < node.val:
                    node.left = insert(node.left, val)
                elif val > node.val:
                    node.right = insert(node.right, val)
                
                return node
            
            self.root = insert(self.root, val)
        
        def remove(self, val):
            """Remove element from set"""
            def delete(node, val):
                if not node:
                    return node
                
                if val < node.val:
                    node.left = delete(node.left, val)
                elif val > node.val:
                    node.right = delete(node.right, val)
                else:
                    self._size -= 1
                    
                    if not node.left:
                        return node.right
                    elif not node.right:
                        return node.left
                    
                    # Find successor
                    successor = node.right
                    while successor.left:
                        successor = successor.left
                    
                    node.val = successor.val
                    node.right = delete(node.right, successor.val)
                
                return node
            
            self.root = delete(self.root, val)
        
        def contains(self, val):
            """Check if element exists"""
            current = self.root
            while current:
                if val == current.val:
                    return True
                elif val < current.val:
                    current = current.left
                else:
                    current = current.right
            return False
        
        def size(self):
            return self._size
        
        def to_sorted_list(self):
            """Convert to sorted list"""
            result = []
            def inorder(node):
                if node:
                    inorder(node.left)
                    result.append(node.val)
                    inorder(node.right)
            
            inorder(self.root)
            return result
    
    def findPredecessor(root, val):
        """Find predecessor of given value in BST"""
        predecessor = None
        
        while root:
            if val > root.val:
                predecessor = root
                root = root.right
            else:
                root = root.left
        
        return predecessor
    
    def findSuccessor(root, val):
        """Find successor of given value in BST"""
        successor = None
        
        while root:
            if val < root.val:
                successor = root
                root = root.left
            else:
                root = root.right
        
        return successor
    
    def findLCA(root, p, q):
        """Find Lowest Common Ancestor in BST"""
        while root:
            if p.val < root.val and q.val < root.val:
                root = root.left
            elif p.val > root.val and q.val > root.val:
                root = root.right
            else:
                return root
        
        return None
```

### BST vs Other Data Structures

**Comparison Table**:

| Operation | BST (Balanced) | Hash Table | Array (Sorted) |
|-----------|----------------|------------|-----------------|
| **Search** | O(log n) | O(1) avg | O(log n) |
| **Insert** | O(log n) | O(1) avg | O(n) |
| **Delete** | O(log n) | O(1) avg | O(n) |
| **Min/Max** | O(log n) | O(n) | O(1) |
| **Range Query** | O(log n + k) | O(n) | O(log n + k) |
| **Sorted Iteration** | O(n) | O(n log n) | O(n) |
| **Memory** | O(n) | O(n) | O(n) |

**When to Use BST**:
✅ Need sorted access to data
✅ Frequent range queries
✅ Need predecessor/successor operations  
✅ Memory constraints (vs hash table overhead)
✅ Deterministic performance requirements

**When NOT to Use BST**:
❌ Only need fast lookup (use hash table)
❌ Frequent random access by index (use array)
❌ Data doesn't have natural ordering
❌ Cannot guarantee balanced tree maintenance

### Advanced Applications

```python
def realWorldBSTApplications():
    """Real-world applications and advanced use cases"""
    
    def countSmaller(nums):
        """Count smaller elements to the right using BST"""
        class BSTNode:
            def __init__(self, val):
                self.val = val
                self.left = None
                self.right = None
                self.count = 1  # Count of nodes in subtree
                self.left_size = 0  # Size of left subtree
        
        def insert(root, val):
            if not root:
                return BSTNode(val)
            
            if val <= root.val:
                root.left = insert(root.left, val)
                root.left_size += 1
            else:
                root.right = insert(root.right, val)
            
            return root
        
        def search(root, val):
            count = 0
            while root:
                if val <= root.val:
                    root = root.left
                else:
                    count += root.left_size + 1
                    root = root.right
            return count
        
        result = []
        root = None
        
        # Process from right to left
        for i in range(len(nums) - 1, -1, -1):
            if root:
                count = search(root, nums[i])
                result.append(count)
            else:
                result.append(0)
            
            root = insert(root, nums[i])
        
        return result[::-1]
    
    def containsNearbyAlmostDuplicate(nums, k, t):
        """Check if array contains nearby almost duplicates"""
        from sortedcontainers import SortedList
        
        if k == 0 or t < 0:
            return False
        
        sorted_window = SortedList()
        
        for i, num in enumerate(nums):
            # Remove element outside window
            if i > k:
                sorted_window.remove(nums[i - k - 1])
            
            # Check for nearby elements within distance t
            pos = sorted_window.bisect_left(num)
            
            # Check left neighbor
            if pos > 0 and num - sorted_window[pos - 1] <= t:
                return True
            
            # Check right neighbor
            if pos < len(sorted_window) and sorted_window[pos] - num <= t:
                return True
            
            sorted_window.add(num)
        
        return False
```

### Questions from Top 150 (86–88)
- ✅ Minimum Absolute Difference in BST - Inorder traversal with adjacent difference tracking
- ✅ Kth Smallest Element in a BST - Optimized inorder traversal with early termination
- ✅ Validate Binary Search Tree - Bounds-checking recursive validation

### BST Mastery Framework
1. **Master the invariant**: Always maintain BST property during modifications
2. **Leverage inorder traversal**: Use sorted property for efficient algorithms
3. **Understand balance**: Know when trees become inefficient and how to rebalance
4. **Range operations**: Exploit BST structure for efficient range queries
5. **Iterator patterns**: Implement efficient in-order iteration using stacks
6. **Conversion algorithms**: Master array ↔ BST and balancing techniques

**Pattern Recognition Guide**:
- **"Sorted order" or "kth smallest/largest"** → Use inorder traversal properties
- **"Range query" or "between values"** → Leverage BST search pruning
- **"Validate BST"** → Check bounds or inorder sequence  
- **"Convert sorted array/list"** → Recursive middle-element selection
- **"Balance" or "height-balanced"** → Reconstruction from inorder traversal
- **"Predecessor/successor"** → BST-specific navigation algorithms

---

## 12. Graph (General / DFS & Union-Find)

### Core Theory & Mathematical Foundation

A **Graph** G = (V, E) is a mathematical structure consisting of vertices (nodes) V and edges E that connect pairs of vertices. Graphs model relationships, networks, dependencies, and countless real-world scenarios from social networks to computer networks to molecular structures.

**Graph Classifications**:
- **Directed vs Undirected**: Edges have direction or are bidirectional
- **Weighted vs Unweighted**: Edges have associated costs/weights or not
- **Dense vs Sparse**: |E| approaches |V|² vs |E| ≈ |V|
- **Connected vs Disconnected**: Path exists between all vertex pairs or not
- **Cyclic vs Acyclic**: Contains cycles or is a forest/tree
- **Simple vs Multigraph**: At most one edge between vertices or multiple allowed

**Mathematical Properties**:
- **Vertex Degree**: Number of incident edges
- **Handshaking Lemma**: Sum of all degrees = 2|E|
- **Path Length**: Number of edges in a path
- **Distance**: Length of shortest path between vertices
- **Diameter**: Maximum distance between any pair of vertices

### Graph Representations & Trade-offs

**Memory & Performance Analysis**:

| Representation | Space | Add Vertex | Add Edge | Remove Vertex | Remove Edge | Check Adjacent |
|----------------|-------|------------|----------|---------------|-------------|----------------|
| **Adjacency List** | O(V+E) | O(1) | O(1) | O(V+E) | O(degree) | O(degree) |
| **Adjacency Matrix** | O(V²) | O(V²) | O(1) | O(V²) | O(1) | O(1) |
| **Edge List** | O(E) | O(1) | O(1) | O(E) | O(E) | O(E) |

```python
def graphRepresentations():
    """Comprehensive graph representation implementations"""
    
    class GraphAdjacencyList:
        """Most commonly used representation - efficient for sparse graphs"""
        
        def __init__(self, directed=False):
            self.graph = {}
            self.directed = directed
            self.vertex_count = 0
            self.edge_count = 0
        
        def add_vertex(self, vertex):
            """Add vertex - O(1)"""
            if vertex not in self.graph:
                self.graph[vertex] = []
                self.vertex_count += 1
        
        def add_edge(self, u, v, weight=None):
            """Add edge - O(1)"""
            self.add_vertex(u)
            self.add_vertex(v)
            
            if weight is None:
                self.graph[u].append(v)
                if not self.directed:
                    self.graph[v].append(u)
            else:
                self.graph[u].append((v, weight))
                if not self.directed:
                    self.graph[v].append((u, weight))
            
            self.edge_count += 1
        
        def remove_edge(self, u, v):
            """Remove edge - O(degree)"""
            if u in self.graph and v in self.graph[u]:
                self.graph[u].remove(v)
                if not self.directed:
                    self.graph[v].remove(u)
                self.edge_count -= 1
                return True
            return False
        
        def get_neighbors(self, vertex):
            """Get all neighbors - O(1)"""
            return self.graph.get(vertex, [])
        
        def get_degree(self, vertex):
            """Get vertex degree - O(1)"""
            return len(self.graph.get(vertex, []))
        
        def has_edge(self, u, v):
            """Check if edge exists - O(degree)"""
            return v in self.graph.get(u, [])
        
        def to_adjacency_matrix(self):
            """Convert to matrix representation"""
            vertices = list(self.graph.keys())
            n = len(vertices)
            matrix = [[0] * n for _ in range(n)]
            vertex_to_index = {v: i for i, v in enumerate(vertices)}
            
            for u in self.graph:
                for v in self.graph[u]:
                    if isinstance(v, tuple):  # Weighted
                        matrix[vertex_to_index[u]][vertex_to_index[v[0]]] = v[1]
                    else:  # Unweighted
                        matrix[vertex_to_index[u]][vertex_to_index[v]] = 1
            
            return matrix, vertices
    
    class GraphAdjacencyMatrix:
        """Matrix representation - efficient for dense graphs"""
        
        def __init__(self, size, directed=False):
            self.matrix = [[0] * size for _ in range(size)]
            self.size = size
            self.directed = directed
            self.vertices = list(range(size))  # Can map to actual vertex names
        
        def add_edge(self, u, v, weight=1):
            """Add edge - O(1)"""
            self.matrix[u][v] = weight
            if not self.directed:
                self.matrix[v][u] = weight
        
        def remove_edge(self, u, v):
            """Remove edge - O(1)"""
            self.matrix[u][v] = 0
            if not self.directed:
                self.matrix[v][u] = 0
        
        def has_edge(self, u, v):
            """Check if edge exists - O(1)"""
            return self.matrix[u][v] != 0
        
        def get_neighbors(self, u):
            """Get neighbors - O(V)"""
            neighbors = []
            for v in range(self.size):
                if self.matrix[u][v] != 0:
                    neighbors.append(v)
            return neighbors
        
        def get_degree(self, u):
            """Get degree - O(V)"""
            return sum(1 for v in range(self.size) if self.matrix[u][v] != 0)
    
    class GraphEdgeList:
        """Edge list representation - simple but limited"""
        
        def __init__(self, directed=False):
            self.edges = []
            self.vertices = set()
            self.directed = directed
        
        def add_edge(self, u, v, weight=None):
            """Add edge - O(1)"""
            self.vertices.add(u)
            self.vertices.add(v)
            
            if weight is None:
                self.edges.append((u, v))
                if not self.directed:
                    self.edges.append((v, u))
            else:
                self.edges.append((u, v, weight))
                if not self.directed:
                    self.edges.append((v, u, weight))
        
        def has_edge(self, u, v):
            """Check edge existence - O(E)"""
            return (u, v) in [(e[0], e[1]) for e in self.edges]
        
        def get_neighbors(self, u):
            """Get neighbors - O(E)"""
            neighbors = []
            for edge in self.edges:
                if edge[0] == u:
                    neighbors.append(edge[1])
            return neighbors
        
        def to_adjacency_list(self):
            """Convert to adjacency list for better performance"""
            graph = GraphAdjacencyList(self.directed)
            for edge in self.edges:
                if len(edge) == 2:
                    graph.add_edge(edge[0], edge[1])
                else:
                    graph.add_edge(edge[0], edge[1], edge[2])
            return graph
    
    return GraphAdjacencyList, GraphAdjacencyMatrix, GraphEdgeList
```

### Depth-First Search (DFS) - Complete Mastery

**DFS Algorithm Analysis**:
- **Time Complexity**: O(V + E) - visits each vertex and edge once
- **Space Complexity**: O(V) - recursion stack or explicit stack
- **Properties**: Explores as deep as possible before backtracking
- **Applications**: Connectivity, cycle detection, topological sorting, pathfinding

```python
def depthFirstSearchAlgorithms():
    """Comprehensive DFS implementations and applications"""
    
    def dfs_recursive(graph, start, visited=None, path=None):
        """Classic recursive DFS with path tracking"""
        if visited is None:
            visited = set()
        if path is None:
            path = []
        
        visited.add(start)
        path.append(start)
        
        for neighbor in graph.get(start, []):
            if neighbor not in visited:
                dfs_recursive(graph, neighbor, visited, path)
        
        return path, visited
    
    def dfs_iterative(graph, start):
        """Iterative DFS using explicit stack"""
        visited = set()
        stack = [start]
        path = []
        
        while stack:
            vertex = stack.pop()
            
            if vertex not in visited:
                visited.add(vertex)
                path.append(vertex)
                
                # Add neighbors to stack (reverse order for consistent traversal)
                for neighbor in reversed(graph.get(vertex, [])):
                    if neighbor not in visited:
                        stack.append(neighbor)
        
        return path, visited
    
    def dfs_with_timestamps(graph, start):
        """DFS with discovery and finish times - useful for classification"""
        time = [0]  # Use list to make it mutable in nested function
        discovery = {}
        finish = {}
        visited = set()
        
        def dfs_visit(vertex):
            nonlocal time
            time[0] += 1
            discovery[vertex] = time[0]
            visited.add(vertex)
            
            for neighbor in graph.get(vertex, []):
                if neighbor not in visited:
                    dfs_visit(neighbor)
            
            time[0] += 1
            finish[vertex] = time[0]
        
        dfs_visit(start)
        return discovery, finish
    
    def dfs_all_paths(graph, start, end, path=None):
        """Find all paths between start and end vertices"""
        if path is None:
            path = []
        
        path = path + [start]
        
        if start == end:
            return [path]
        
        paths = []
        for neighbor in graph.get(start, []):
            if neighbor not in path:  # Avoid cycles
                new_paths = dfs_all_paths(graph, neighbor, end, path)
                paths.extend(new_paths)
        
        return paths
    
    def dfs_longest_path(graph, start, visited=None, path_length=0):
        """Find longest path in DAG starting from vertex"""
        if visited is None:
            visited = set()
        
        visited.add(start)
        max_length = path_length
        
        for neighbor in graph.get(start, []):
            if neighbor not in visited:
                length = dfs_longest_path(graph, neighbor, visited.copy(), path_length + 1)
                max_length = max(max_length, length)
        
        return max_length
    
    def dfs_cycle_detection_undirected(graph):
        """Detect cycle in undirected graph using DFS"""
        visited = set()
        
        def has_cycle_util(vertex, parent):
            visited.add(vertex)
            
            for neighbor in graph.get(vertex, []):
                if neighbor not in visited:
                    if has_cycle_util(neighbor, vertex):
                        return True
                elif neighbor != parent:
                    return True  # Back edge found
            
            return False
        
        # Check all components
        for vertex in graph:
            if vertex not in visited:
                if has_cycle_util(vertex, None):
                    return True
        
        return False
    
    def dfs_cycle_detection_directed(graph):
        """Detect cycle in directed graph using DFS with colors"""
        WHITE, GRAY, BLACK = 0, 1, 2
        color = {vertex: WHITE for vertex in graph}
        
        def has_cycle_util(vertex):
            if color[vertex] == GRAY:
                return True  # Back edge - cycle found
            
            if color[vertex] == BLACK:
                return False  # Already processed
            
            color[vertex] = GRAY
            
            for neighbor in graph.get(vertex, []):
                if has_cycle_util(neighbor):
                    return True
            
            color[vertex] = BLACK
            return False
        
        for vertex in graph:
            if color[vertex] == WHITE:
                if has_cycle_util(vertex):
                    return True
        
        return False
    
    return (dfs_recursive, dfs_iterative, dfs_with_timestamps,
           dfs_all_paths, dfs_longest_path, 
           dfs_cycle_detection_undirected, dfs_cycle_detection_directed)
```

### Connected Components & Graph Connectivity

```python
def graphConnectivityAlgorithms():
    """Advanced connectivity algorithms and component analysis"""
    
    def connected_components_undirected(graph):
        """Find all connected components in undirected graph"""
        visited = set()
        components = []
        
        def dfs_component(vertex, component):
            visited.add(vertex)
            component.append(vertex)
            
            for neighbor in graph.get(vertex, []):
                if neighbor not in visited:
                    dfs_component(neighbor, component)
        
        for vertex in graph:
            if vertex not in visited:
                component = []
                dfs_component(vertex, component)
                components.append(component)
        
        return components
    
    def strongly_connected_components(graph):
        """Find strongly connected components using Kosaraju's algorithm"""
        
        def dfs1(vertex, visited, stack):
            """First DFS to get finish order"""
            visited.add(vertex)
            for neighbor in graph.get(vertex, []):
                if neighbor not in visited:
                    dfs1(neighbor, visited, stack)
            stack.append(vertex)
        
        def dfs2(vertex, visited, component, transpose_graph):
            """Second DFS on transpose graph"""
            visited.add(vertex)
            component.append(vertex)
            for neighbor in transpose_graph.get(vertex, []):
                if neighbor not in visited:
                    dfs2(neighbor, visited, component, transpose_graph)
        
        def get_transpose(graph):
            """Get transpose of directed graph"""
            transpose = {}
            for vertex in graph:
                transpose[vertex] = []
            
            for vertex in graph:
                for neighbor in graph[vertex]:
                    transpose[neighbor].append(vertex)
            
            return transpose
        
        # Step 1: Fill vertices in stack according to their finishing times
        visited = set()
        stack = []
        for vertex in graph:
            if vertex not in visited:
                dfs1(vertex, visited, stack)
        
        # Step 2: Create transpose graph
        transpose_graph = get_transpose(graph)
        
        # Step 3: Process all vertices in order defined by stack
        visited = set()
        scc_list = []
        
        while stack:
            vertex = stack.pop()
            if vertex not in visited:
                component = []
                dfs2(vertex, visited, component, transpose_graph)
                scc_list.append(component)
        
        return scc_list
    
    def tarjan_scc(graph):
        """Tarjan's algorithm for strongly connected components"""
        index_counter = [0]
        stack = []
        lowlinks = {}
        index = {}
        on_stack = {}
        scc_list = []
        
        def strongconnect(vertex):
            index[vertex] = index_counter[0]
            lowlinks[vertex] = index_counter[0]
            index_counter[0] += 1
            stack.append(vertex)
            on_stack[vertex] = True
            
            for neighbor in graph.get(vertex, []):
                if neighbor not in index:
                    strongconnect(neighbor)
                    lowlinks[vertex] = min(lowlinks[vertex], lowlinks[neighbor])
                elif on_stack.get(neighbor, False):
                    lowlinks[vertex] = min(lowlinks[vertex], index[neighbor])
            
            # If vertex is root node, pop stack and create SCC
            if lowlinks[vertex] == index[vertex]:
                component = []
                while True:
                    w = stack.pop()
                    on_stack[w] = False
                    component.append(w)
                    if w == vertex:
                        break
                scc_list.append(component)
        
        for vertex in graph:
            if vertex not in index:
                strongconnect(vertex)
        
        return scc_list
    
    def bridge_finding(graph):
        """Find bridges (critical edges) in undirected graph"""
        visited = set()
        disc = {}
        low = {}
        parent = {}
        bridges = []
        time = [0]
        
        def bridge_util(u):
            visited.add(u)
            disc[u] = low[u] = time[0]
            time[0] += 1
            
            for v in graph.get(u, []):
                if v not in visited:
                    parent[v] = u
                    bridge_util(v)
                    
                    low[u] = min(low[u], low[v])
                    
                    # Bridge condition
                    if low[v] > disc[u]:
                        bridges.append((u, v))
                
                elif v != parent.get(u):
                    low[u] = min(low[u], disc[v])
        
        for vertex in graph:
            if vertex not in visited:
                bridge_util(vertex)
        
        return bridges
    
    def articulation_points(graph):
        """Find articulation points (cut vertices) in undirected graph"""
        visited = set()
        disc = {}
        low = {}
        parent = {}
        ap = set()
        time = [0]
        
        def ap_util(u):
            children = 0
            visited.add(u)
            disc[u] = low[u] = time[0]
            time[0] += 1
            
            for v in graph.get(u, []):
                if v not in visited:
                    children += 1
                    parent[v] = u
                    ap_util(v)
                    
                    low[u] = min(low[u], low[v])
                    
                    # Root with more than one child
                    if parent.get(u) is None and children > 1:
                        ap.add(u)
                    
                    # Non-root articulation point
                    if parent.get(u) is not None and low[v] >= disc[u]:
                        ap.add(u)
                
                elif v != parent.get(u):
                    low[u] = min(low[u], disc[v])
        
        for vertex in graph:
            if vertex not in visited:
                ap_util(vertex)
        
        return list(ap)
    
    return (connected_components_undirected, strongly_connected_components,
           tarjan_scc, bridge_finding, articulation_points)
```

### Union-Find (Disjoint Set Union) - Advanced Implementation

```python
def unionFindDataStructures():
    """Complete Union-Find implementations with optimizations"""
    
    class UnionFind:
        """Optimized Union-Find with path compression and union by rank"""
        
        def __init__(self, n):
            self.parent = list(range(n))
            self.rank = [0] * n
            self.size = [1] * n  # Size of each component
            self.components = n
        
        def find(self, x):
            """Find with path compression - O(α(n)) amortized"""
            if self.parent[x] != x:
                self.parent[x] = self.find(self.parent[x])  # Path compression
            return self.parent[x]
        
        def union(self, x, y):
            """Union by rank - O(α(n)) amortized"""
            root_x, root_y = self.find(x), self.find(y)
            
            if root_x == root_y:
                return False  # Already in same component
            
            # Union by rank for better balance
            if self.rank[root_x] < self.rank[root_y]:
                self.parent[root_x] = root_y
                self.size[root_y] += self.size[root_x]
            elif self.rank[root_x] > self.rank[root_y]:
                self.parent[root_y] = root_x
                self.size[root_x] += self.size[root_y]
            else:
                self.parent[root_y] = root_x
                self.size[root_x] += self.size[root_y]
                self.rank[root_x] += 1
            
            self.components -= 1
            return True
        
        def connected(self, x, y):
            """Check if x and y are in same component - O(α(n))"""
            return self.find(x) == self.find(y)
        
        def component_size(self, x):
            """Get size of component containing x"""
            return self.size[self.find(x)]
        
        def get_components(self):
            """Get all components as lists"""
            component_map = {}
            for i in range(len(self.parent)):
                root = self.find(i)
                if root not in component_map:
                    component_map[root] = []
                component_map[root].append(i)
            return list(component_map.values())
    
    class WeightedUnionFind:
        """Union-Find with edge weights for more complex relationships"""
        
        def __init__(self, n):
            self.parent = list(range(n))
            self.weight = [0] * n  # Weight from node to its parent
            self.components = n
        
        def find(self, x):
            """Find with path compression and weight adjustment"""
            if self.parent[x] != x:
                original_parent = self.parent[x]
                self.parent[x] = self.find(original_parent)
                self.weight[x] += self.weight[original_parent]
            return self.parent[x]
        
        def union(self, x, y, w):
            """Union with weight constraint: weight[y] - weight[x] = w"""
            root_x, root_y = self.find(x), self.find(y)
            
            if root_x == root_y:
                return self.weight[y] - self.weight[x] == w
            
            # Make root_y the parent of root_x
            self.parent[root_x] = root_y
            self.weight[root_x] = self.weight[y] - self.weight[x] - w
            self.components -= 1
            return True
        
        def difference(self, x, y):
            """Get weight[y] - weight[x] if connected"""
            if not self.connected(x, y):
                return None
            return self.weight[y] - self.weight[x]
        
        def connected(self, x, y):
            return self.find(x) == self.find(y)
    
    def union_find_applications():
        """Common Union-Find application patterns"""
        
        def number_of_islands_uf(grid):
            """Solve number of islands using Union-Find"""
            if not grid or not grid[0]:
                return 0
            
            rows, cols = len(grid), len(grid[0])
            
            # Count initial land cells
            land_count = 0
            for r in range(rows):
                for c in range(cols):
                    if grid[r][c] == '1':
                        land_count += 1
            
            uf = UnionFind(rows * cols)
            uf.components = land_count  # Start with land cells as components
            
            # Connect adjacent land cells
            directions = [(0, 1), (1, 0), (0, -1), (-1, 0)]
            
            for r in range(rows):
                for c in range(cols):
                    if grid[r][c] == '1':
                        for dr, dc in directions:
                            nr, nc = r + dr, c + dc
                            if (0 <= nr < rows and 0 <= nc < cols and 
                                grid[nr][nc] == '1'):
                                uf.union(r * cols + c, nr * cols + nc)
            
            # Count components that contain land
            components = set()
            for r in range(rows):
                for c in range(cols):
                    if grid[r][c] == '1':
                        components.add(uf.find(r * cols + c))
            
            return len(components)
        
        def accounts_merge(accounts):
            """Merge accounts with common emails using Union-Find"""
            from collections import defaultdict
            
            email_to_name = {}
            email_to_id = {}
            email_id = 0
            
            # Assign unique IDs to emails
            for account in accounts:
                name = account[0]
                for email in account[1:]:
                    if email not in email_to_id:
                        email_to_id[email] = email_id
                        email_to_name[email] = name
                        email_id += 1
            
            uf = UnionFind(email_id)
            
            # Union emails from same account
            for account in accounts:
                if len(account) > 2:  # Has at least 2 emails
                    first_email_id = email_to_id[account[1]]
                    for email in account[2:]:
                        uf.union(first_email_id, email_to_id[email])
            
            # Group emails by component
            component_to_emails = defaultdict(list)
            for email, email_id in email_to_id.items():
                root = uf.find(email_id)
                component_to_emails[root].append(email)
            
            # Build result
            result = []
            for emails in component_to_emails.values():
                emails.sort()
                name = email_to_name[emails[0]]
                result.append([name] + emails)
            
            return result
        
        def redundant_connection(edges):
            """Find redundant edge that creates cycle"""
            uf = UnionFind(len(edges) + 1)
            
            for u, v in edges:
                if not uf.union(u, v):
                    return [u, v]  # This edge creates a cycle
            
            return None
        
        return number_of_islands_uf, accounts_merge, redundant_connection
    
    return UnionFind, WeightedUnionFind, union_find_applications
```

### Topological Sorting & DAG Algorithms

```python
def topologicalSortingAlgorithms():
    """Complete topological sorting implementations and applications"""
    
    def kahns_algorithm(graph, n):
        """Kahn's algorithm using BFS - O(V + E)"""
        from collections import deque, defaultdict
        
        # Build indegree array
        indegree = [0] * n
        for node in graph:
            for neighbor in graph[node]:
                indegree[neighbor] += 1
        
        # Initialize queue with nodes having 0 indegree
        queue = deque([i for i in range(n) if indegree[i] == 0])
        topo_order = []
        
        while queue:
            node = queue.popleft()
            topo_order.append(node)
            
            # Reduce indegree of neighbors
            for neighbor in graph.get(node, []):
                indegree[neighbor] -= 1
                if indegree[neighbor] == 0:
                    queue.append(neighbor)
        
        # Check for cycle
        if len(topo_order) == n:
            return topo_order
        else:
            return []  # Cycle detected
    
    def dfs_topological_sort(graph, n):
        """DFS-based topological sort - O(V + E)"""
        WHITE, GRAY, BLACK = 0, 1, 2
        color = [WHITE] * n
        topo_order = []
        has_cycle = False
        
        def dfs(node):
            nonlocal has_cycle
            
            if color[node] == GRAY:  # Back edge - cycle detected
                has_cycle = True
                return
            
            if color[node] == BLACK:  # Already processed
                return
            
            color[node] = GRAY
            
            for neighbor in graph.get(node, []):
                dfs(neighbor)
                if has_cycle:
                    return
            
            color[node] = BLACK
            topo_order.append(node)
        
        # Process all nodes
        for i in range(n):
            if color[i] == WHITE:
                dfs(i)
                if has_cycle:
                    return []
        
        return topo_order[::-1]  # Reverse for correct order
    
    def course_schedule(numCourses, prerequisites):
        """Course Schedule I - detect if all courses can be taken"""
        graph = {i: [] for i in range(numCourses)}
        
        # Build graph
        for course, prereq in prerequisites:
            graph[prereq].append(course)
        
        # Use topological sort to detect cycles
        topo_order = kahns_algorithm(graph, numCourses)
        return len(topo_order) == numCourses
    
    def course_schedule_ii(numCourses, prerequisites):
        """Course Schedule II - return valid ordering"""
        graph = {i: [] for i in range(numCourses)}
        
        for course, prereq in prerequisites:
            graph[prereq].append(course)
        
        return kahns_algorithm(graph, numCourses)
    
    def alien_dictionary(words):
        """Determine order of characters in alien language"""
        from collections import defaultdict, deque
        
        # Build graph of character dependencies
        graph = defaultdict(set)
        indegree = defaultdict(int)
        chars = set()
        
        # Initialize all characters
        for word in words:
            for char in word:
                chars.add(char)
                if char not in indegree:
                    indegree[char] = 0
        
        # Build edges from adjacent words
        for i in range(len(words) - 1):
            word1, word2 = words[i], words[i + 1]
            min_len = min(len(word1), len(word2))
            
            # Check for invalid ordering
            if (len(word1) > len(word2) and 
                word1[:min_len] == word2[:min_len]):
                return ""
            
            # Find first different character
            for j in range(min_len):
                if word1[j] != word2[j]:
                    if word2[j] not in graph[word1[j]]:
                        graph[word1[j]].add(word2[j])
                        indegree[word2[j]] += 1
                    break
        
        # Topological sort using Kahn's algorithm
        queue = deque([char for char in chars if indegree[char] == 0])
        result = []
        
        while queue:
            char = queue.popleft()
            result.append(char)
            
            for neighbor in graph[char]:
                indegree[neighbor] -= 1
                if indegree[neighbor] == 0:
                    queue.append(neighbor)
        
        # Check if all characters are processed
        return ''.join(result) if len(result) == len(chars) else ""
    
    def longest_path_dag(graph, start):
        """Find longest path in DAG starting from given node"""
        # Topologically sort the graph
        topo_order = dfs_topological_sort(graph, len(graph))
        
        if not topo_order:
            return -1  # Cycle detected
        
        # Initialize distances
        dist = [-float('inf')] * len(graph)
        dist[start] = 0
        
        # Process vertices in topological order
        for u in topo_order:
            if dist[u] != -float('inf'):
                for v in graph.get(u, []):
                    # Assuming unit weights, modify for weighted graphs
                    dist[v] = max(dist[v], dist[u] + 1)
        
        return max(dist)
    
    return (kahns_algorithm, dfs_topological_sort, course_schedule,
           course_schedule_ii, alien_dictionary, longest_path_dag)
```

### Advanced Graph Applications

```python
def advancedGraphApplications():
    """Complex graph problems and real-world applications"""
    
    def evaluate_division(equations, values, queries):
        """Solve equation evaluation using weighted Union-Find or DFS"""
        from collections import defaultdict
        
        # Build graph with weights
        graph = defaultdict(dict)
        
        for (a, b), value in zip(equations, values):
            graph[a][b] = value
            graph[b][a] = 1.0 / value
        
        def dfs(start, end, visited):
            if start == end:
                return 1.0
            
            visited.add(start)
            
            for neighbor, weight in graph[start].items():
                if neighbor not in visited:
                    result = dfs(neighbor, end, visited)
                    if result != -1.0:
                        return weight * result
            
            return -1.0
        
        results = []
        for a, b in queries:
            if a not in graph or b not in graph:
                results.append(-1.0)
            else:
                result = dfs(a, b, set())
                results.append(result)
        
        return results
    
    def surrounded_regions(board):
        """Replace surrounded regions using DFS/Union-Find"""
        if not board or not board[0]:
            return
        
        rows, cols = len(board), len(board[0])
        
        def dfs(r, c):
            if (r < 0 or r >= rows or c < 0 or c >= cols or 
                board[r][c] != 'O'):
                return
            
            board[r][c] = 'S'  # Mark as safe
            
            # Explore all 4 directions
            dfs(r + 1, c)
            dfs(r - 1, c)
            dfs(r, c + 1)
            dfs(r, c - 1)
        
        # Mark boundary-connected 'O's as safe
        for r in range(rows):
            dfs(r, 0)      # Left border
            dfs(r, cols - 1)  # Right border
        
        for c in range(cols):
            dfs(0, c)      # Top border
            dfs(rows - 1, c)  # Bottom border
        
        # Convert surrounded 'O's to 'X' and safe 'S's back to 'O'
        for r in range(rows):
            for c in range(cols):
                if board[r][c] == 'O':
                    board[r][c] = 'X'
                elif board[r][c] == 'S':
                    board[r][c] = 'O'
    
    def clone_graph(node):
        """Clone undirected graph using DFS"""
        if not node:
            return None
        
        visited = {}
        
        def dfs_clone(original):
            if original in visited:
                return visited[original]
            
            # Create clone
            clone = Node(original.val)
            visited[original] = clone
            
            # Clone all neighbors
            for neighbor in original.neighbors:
                clone.neighbors.append(dfs_clone(neighbor))
            
            return clone
        
        return dfs_clone(node)
    
    def pacific_atlantic_water_flow(heights):
        """Find cells that can reach both Pacific and Atlantic"""
        if not heights or not heights[0]:
            return []
        
        rows, cols = len(heights), len(heights[0])
        
        def dfs(r, c, reachable, prev_height):
            if (r < 0 or r >= rows or c < 0 or c >= cols or
                (r, c) in reachable or heights[r][c] < prev_height):
                return
            
            reachable.add((r, c))
            
            # Explore 4 directions
            for dr, dc in [(0, 1), (1, 0), (0, -1), (-1, 0)]:
                dfs(r + dr, c + dc, reachable, heights[r][c])
        
        pacific = set()
        atlantic = set()
        
        # Start DFS from Pacific borders (top and left)
        for r in range(rows):
            dfs(r, 0, pacific, heights[r][0])
            dfs(r, cols - 1, atlantic, heights[r][cols - 1])
        
        for c in range(cols):
            dfs(0, c, pacific, heights[0][c])
            dfs(rows - 1, c, atlantic, heights[rows - 1][c])
        
        # Find intersection
        result = []
        for r, c in pacific:
            if (r, c) in atlantic:
                result.append([r, c])
        
        return result
    
    def word_ladder_graph(beginWord, endWord, wordList):
        """Model word ladder as graph problem"""
        if endWord not in wordList:
            return 0
        
        from collections import deque, defaultdict
        
        # Build graph of word transformations
        graph = defaultdict(list)
        wordList.append(beginWord)
        
        # Create intermediate states
        for word in wordList:
            for i in range(len(word)):
                pattern = word[:i] + '*' + word[i+1:]
                graph[pattern].append(word)
        
        # BFS to find shortest transformation
        visited = set([beginWord])
        queue = deque([(beginWord, 1)])
        
        while queue:
            word, length = queue.popleft()
            
            for i in range(len(word)):
                pattern = word[:i] + '*' + word[i+1:]
                
                for next_word in graph[pattern]:
                    if next_word == endWord:
                        return length + 1
                    
                    if next_word not in visited:
                        visited.add(next_word)
                        queue.append((next_word, length + 1))
        
        return 0
    
    return (evaluate_division, surrounded_regions, clone_graph,
           pacific_atlantic_water_flow, word_ladder_graph)
```

### Questions from Top 150 (89–94)
- ✅ Number of Islands - Connected components using DFS with grid traversal
- ✅ Surrounded Regions - DFS from boundaries to identify safe regions  
- ✅ Clone Graph - Deep copying using DFS with visited tracking
- ✅ Evaluate Division - Weighted graph problem using DFS path finding
- ✅ Course Schedule - Cycle detection in DAG using topological sorting
- ✅ Course Schedule II - Topological ordering using Kahn's algorithm

### Graph Algorithm Mastery Framework
1. **Master representation trade-offs**: Choose adjacency list vs matrix based on density
2. **DFS pattern recognition**: Connectivity, cycles, paths, topological sorting
3. **Union-Find applications**: Dynamic connectivity, MST, social networks
4. **Component analysis**: Strongly connected, bridges, articulation points
5. **DAG algorithms**: Topological sorting, longest paths, dependency resolution
6. **Real-world modeling**: Transform problems into graph representations

**Pattern Recognition Guide**:
- **"Connected" or "reachable"** → DFS/BFS traversal or Union-Find
- **"Islands" or "regions"** → Connected components using DFS
- **"Dependencies" or "prerequisites"** → Topological sorting on DAG
- **"Shortest path"** (unweighted) → BFS traversal
- **"Cycle detection"** → DFS with colors or Union-Find for undirected
- **"Dynamic connectivity"** → Union-Find data structure
- **"Clone" or "copy"** → DFS/BFS with visited mapping
- **"Minimum spanning"** → Union-Find for Kruskal's algorithm

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
