# [Blind 75](https://www.teamblind.com/post/New-Year-Gift---Curated-List-of-Top-75-LeetCode-Questions-to-Save-Your-Time-OaM1orEU)

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
