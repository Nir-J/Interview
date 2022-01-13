# [Blind 75](https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU)
## Array

### [152. Maximum Product Subarray](https://leetcode.com/problems/maximum-product-subarray/)
```python
class Solution:
    def maxProducthelp(self, nums: List[int]) -> int:
        N = len(nums)
        res = max(nums)
        pre = [1]*N
        suf = [1]*N
        pre[0] = nums[0]
        suf[-1] = nums[-1]
        for i in range(1, N):
            pre[i] = pre[i-1] * nums[i]
            suf[N-i-1] = suf[N-i]*nums[N-i-1]
        total = suf[0]
        res = max(max(pre), max(suf), res)
        for i in range(N//2):
            j = N-i-1
            if i < j-1:
                temp = total
                if pre[i] != 0:
                    temp = temp//pre[i]
                if suf[j] != 0:
                    temp = temp//suf[j]
                res = max(res, temp)
        return res
    
    def maxProduct(self, nums):
        start = 0
        res = max(nums)
        for i in range(len(nums)):
            if nums[i] == 0:
                if start < i-1:
                    res = max(res, self.maxProducthelp(nums[start:i]))
                start=i+1
        if start < len(nums)-1:
            res = max(res, self.maxProducthelp(nums[start:]))
        return res

```
> - Time Complexity: O(N), N = Length of nums
> - Space Complexity: O(N) (Prefix suffix arrays) 

Voila moments:
- Last constraint mentions Prefix/Suffix. Useful when calculating range products.
- Presence of zero messes up the whole thing
- Zero resets progress. So can split the array on where zeros are present. Didn't figure this out till lots of failure, hence changed main function to helper


## Binary
## Dynamic Programming
## Graph

### [128. Longest Consecutive Sequence](https://leetcode.com/problems/longest-consecutive-sequence/)

```python
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
        
        s = set(nums)
        done = {}
        
        def find(n):
            if n not in s:
                return 0
            if n in done:
                return done[n]
            done[n] = 1 + find(n+1)
            return done[n]
            
        maxx = 0
        for n in nums:
            maxx = max(maxx, find(n))
        return maxx
```
> - Time complexity: O(N)
> - Space complexity: O(N) (set, dict)

First thoughts:
- Questions required O(N) so can't sort. Tried to use dict by storing whatever I see, and updating entries as I saw neighbors. This won't be optimal as it would keep on updating multiple entries.

Voila moments:
- Question says nothing about space complexity. Create a set of whole list. Now we know if something is present or not in O(1)
- Have a done dict (memory) which is longest sequence possible from current number. Recursively fill it. Keep track of max.
- Alt: No need of dict. Do not search if N-1 is present in the list. Only start sequence from lowest number. That way we only count once.

## Interval

### [56. Merge Intervals](https://leetcode.com/problems/merge-intervals/)

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals = sorted(intervals, key=lambda x: x[0])
        last_end = intervals[0][1]
        last_start = intervals[0][0]
        res = []
        res.append(intervals[0])
        for start, end in intervals[1:]:
            last_start = start
            if end <= last_end:
                pass
            elif start <= last_end:
                res[-1][1] = end
                last_end = end
            else:
                res.append([start,end])
                last_end = end
        return res

```
> - Time Complexity: O(N), N = Number of intervals
> - Space Complexity: O(N) (New intervals list)


Voila moments:
- Need to sort intervals by start time. Then start comparing adjacent intervals. Need a new list
- Update the previous end if the current start lies within it.
- Edge case: Current end is smaller than previous end, in that case don't update.
- If current start is more than previous end, we insert a new interval

### [435. Non-overlapping Intervals](https://leetcode.com/problems/non-overlapping-intervals/)

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: List[List[int]]) -> int:
        intervals.sort(key=lambda x: x[0])
        
        last = 0
        cur = 1
        cnt = 0
        while cur < len(intervals):
            a = intervals[last]
            b = intervals[cur]
            if a[1] > b[0]:
                cnt += 1
                if a[1] > b[1]:
                    last = cur
                    cur += 1
                else:
                    cur += 1
            else:
                last = cur
                cur += 1
        return cnt
        
```
> - Time complexity: O(N), N = Number of intervals
> - Space complexity: O(1)

What didn't work: 
- Tried recursive way to try out all possible exclusions.
- Sort intervals. When two intervals overlap, we have to decide which to exclude. So two possible recursive branches.
- Add memoization to this, just keeping track of current valid and last valid interval. (55/58 test cases)
- Tried to add binary search to find next best interval we can pick. This adds complexity in code to calculate how many intervals we have to exclude in between.
- Worked for some, failed majority.

Voila moments:
- When two intervals overlap, we can choose which to exclude based on greedy method.
- Sort intervals based on start time. When two overlap, check which has the larger end interval and exclude that. As this interval will obviously overlap with more intervals.
- If intervals don't overlap, we just move on assigning last valid to current, and current to current + 1.



## Linked List

### [143. Reorder List](https://leetcode.com/problems/reorder-list/)
```python
class Solution:
    def reorderList(self, head: Optional[ListNode]) -> None:
        """
        Do not return anything, modify head in-place instead.
        """
        
        stack = []
        cur = head
        length = 0
        while cur:
            stack.append(cur)
            cur = cur.next
            length += 1
        cur = head
        i = 1
        while i+1 < length:
            i += 1
            length -= 1
            after = cur.next
            new = stack[-1]
            stack.pop()
            new.next = after
            cur.next = new
            cur = after
            stack[-1].next = None
        return head
            
```
> - Time complexity: O(N), N = length of list
> - Space complexity: O(N), (Stack)

Voila moments:
- Deciding when to stop is important.
- If the node we need to get from end to insert is same as our current end node, we need to stop.
- Change all next pointers appropriately. Changing end node's next to None is easy due to stack.

Optimization:
- Can optimize space
- Find out midpoint of list by slow and fast pointers.
- Reverse second half of list. Merge two lists.

```python
class Solution:
    def reorderList(self, head: Optional[ListNode]) -> None:
        """
        Do not return anything, modify head in-place instead.
        """
        def reverse(node):
            prev = None
            cur = node
            while cur:
                after = cur.next
                cur.next = prev
                prev = cur
                cur = after
            return prev
        
        slow = head
        fast = head
        while fast and fast.next:
            slow = slow.next
            fast = fast.next.next
        
        h2 = reverse(slow.next)
        slow.next = None
        h1 = head
        
        while h1 and h2:
            after1 = h1.next
            after2 = h2.next
            h1.next = h2
            h2.next = after1
            h1 = after1
            h2 = after2
        return head
```


## Matrix
## String

### [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)
```python
class Solution:
    def isAnagram(self, s: str, t: str) -> bool:
        
        def count(word):
            ctr = [0]*26
            for w in word:
                ctr[ord(w)-ord('a')] += 1
            return "".join([str(x) for x in ctr])
        
        return count(s) == count(t)
```
> - Time Complexity: O(N), N = Max len of string
> - Space Complexity: O(1) (Counter array is fixed)


Voila moments:
- Anagrams will have the same counter array


### [49. Group Anagrams](https://leetcode.com/problems/group-anagrams/)

```python
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        
        def get_cnt_str(word):
            cnt = [0]*26
            for w in word:
                asci = ord(w)-ord('a')
                cnt[asci] += 1
            return "|".join([str(x) for x in cnt])
        
        groups = {}
        for word in strs:
            key = get_cnt_str(word)
            if key not in groups:
                groups[key] = []
            groups[key].append(word)
        
        res = []
        for _, group in groups.items():
            res.append(group)
        return res

```
> - Time Complexity: O(N*M), N = Number of strings, M = Max len of string
> - Space Complexity: O(N).. or maybe both


Voila moments:
- Anagrams will have the same counter array
- Just group based on counter arrays.


## Tree

### [104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)

```python
class Solution:
    def maxDepth(self, root: Optional[TreeNode]) -> int:
        
        if root == None:
            return 0
        return 1 + max(self.maxDepth(root.left),self.maxDepth(root.right))
```
> - Time complexity: O(N), N = number of nodes
> - Space complexity: O(N), Max depth of stack (Not logN as its not a balanced tree)

Voila moments:
- Simple dfs


### [230. Kth Smallest Element in a BST](https://leetcode.com/problems/kth-smallest-element-in-a-bst/)

```python
class Solution:
    def kthSmallest(self, root: Optional[TreeNode], k: int) -> int:
        
        kth = None
        ctr = 1
        def inorder(node):
            nonlocal kth, ctr
            if not node:
                return
            inorder(node.left)
            if ctr == k:
                kth = node.val
            ctr += 1
            inorder(node.right)
        inorder(root)
        return kth
```
> - Time complexity: O(N), N = number of elements
> - Space Complexity: O(N) (Recursion Stack)

Voila Moments
- It's a BST! (Already sorted)
- Inorder traversal gives order.
- Just need to keep track of how many numbers we have already come across. Used global variables for this.



### [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        
        lowest = None
        def find(node):
            nonlocal lowest
            if not node:
                return 0
            res = 0
            if node.val == p.val or node.val == q.val:
                res = 1
            res += (find(node.left) + find(node.right))
            if not lowest and res == 2:
                lowest = node
            return res
        find(root)
        return lowest
```
> - Time complexity: O(N), Searching all nodes
> - Space Complexity: O(N), tree might be just one branch

Initial thoughts:
- Tried to make the function return bool if one of the values is found. Becomes hard to manage if both nodes are present in the same branch.

Voila moments:
- Returned 1 if p or q is found. If current value is 2, both are found and this is the lowest.

What I missed:
- Its a BST. So can focus search instead of searching the whole tree. 
- Keep going till p and q are in different branches, or we reach either p or q, thats our lowest.

```python
class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', p: 'TreeNode', q: 'TreeNode') -> 'TreeNode':
        if p.val < root.val and q.val < root.val:
            return self.lowestCommonAncestor(root.left, p, q)
        elif p.val > root.val and q.val > root.val:
            return self.lowestCommonAncestor(root.right, p, q)
        else:
            return root
```
> - Time complexity: O(N)
> - Space complexity: O(N). O(1) if done iteratively.



## Heap
