# 二分搜索

给一个**有序数组**和目标值，找第一次/最后一次/任何一次出现的索引，时间复杂度 O(logN)。

## 模板

常用的二分搜索模板有如下三种形式：

![binary_search_template](https://img.fuiboom.com/img/binary_search_template.png)

其中，模板 1 和 3 是最常用的，几乎所有二分查找问题都可以用其中之一轻松实现。模板 2 更高级一些，用于解决某些类型的问题。详细的对比可以参考 Leetcode 上的文章：[二分搜索模板](https://leetcode-cn.com/explore/learn/card/binary-search/212/template-analysis/847/)。
区分语法
模板1：
初始条件：left = 0, right = length-1
终止：left > right             while left <= right:
向左查找：right = mid-1
向右查找：left = mid+1


模板2：
关键属性

一种实现二分查找的高级方法。
查找条件需要访问元素的直接右邻居。
使用元素的右邻居来确定是否满足条件，并决定是向左还是向右。
保证查找空间在每一步中至少有 2 个元素。
需要进行后处理。 当你剩下 1 个元素时，循环 / 递归结束。 需要评估剩余元素是否符合条件

区分语法:
初始条件：left = 0, right = length
终止：left == right     while left<right:
向左查找：right = mid
向右查找：left = mid+1


模板3：模板 #3 是二分查找的另一种独特形式。 它用于搜索需要访问当前索引及其在数组中的直接左右邻居索引的元素或条件
关键属性

 

实现二分查找的另一种方法。
搜索条件需要访问元素的直接左右邻居。
使用元素的邻居来确定它是向右还是向左。
保证查找空间在每个步骤中至少有 3 个元素。
需要进行后处理。 当剩下 2 个元素时，循环 / 递归结束。 需要评估其余元素是否符合条件。

区分语法:
初始条件：left = 0, right = length-1
终止：left + 1 == right      while left + 1 < right:
向左查找：right = mid
向右查找：left = mid


### [binary-search](https://leetcode-cn.com/problems/binary-search/)

> 给定一个  n  个元素有序的（升序）整型数组  nums 和一个目标值  target  ，写一个函数搜索  nums  中的 target，如果目标值存在返回下标，否则返回 -1。

- 模板 3 的实现

```Python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        
        l, r = 0, len(nums) - 1
        
        while l + 1 < r:
            mid = l + (r - l) // 2
            if nums[mid] < target:
                l = mid
            else:
                r = mid

        if nums[l] == target:
            return l
        elif nums[r] == target:
            return r
        else:
            return -1
```

- 如果是最简单的二分搜索，不需要找第一个、最后一个位置，或者是没有重复元素，可以使用模板 1，代码更简洁。同时，如果搜索失败，left 是第一个大于 target 的索引，right 是最后一个小于 target 的索引。

```Python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        
        l, r = 0, len(nums) - 1
        
        while l <= r:
            mid = l + (r - l) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] > target:
                r = mid - 1
            else:
                l = mid + 1
        
        return -1
```

- 模板 2 的实现

```Python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        
        l, r = 0, len(nums) - 1
        
        while l < r:
            mid = l + (r - l) // 2
            if nums[mid] < target:
                l = mid + 1
            else:
                r = mid

        if nums[l] == target:
            return l
        
        return -1
```

## 常见题目

### [find-first-and-last-position-of-element-in-sorted-array](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

> 给定一个包含 n 个整数的排序数组，找出给定目标值 target 的起始和结束位置。如果目标值不在数组中，则返回`[-1, -1]`

- 思路：核心点就是找第一个 target 的索引，和最后一个 target 的索引，所以用两次二分搜索分别找第一次和最后一次的位置，下面是使用模板 3 的解法
模板1：
```Python
class Solution(object):
    def searchRange(self,nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        def left_func(nums,target):
            n = len(nums)-1
            left = 0
            right = n
            while(left<=right):
                mid = (left+right)//2
                if nums[mid] >= target:
                    right = mid-1
                if nums[mid] < target:
                    left = mid+1
            return left
        a =  left_func(nums,target)
        b = left_func(nums,target+1)
        if  a == len(nums) or nums[a] != target:
            return [-1,-1]
        else:
            return [a,b-1]
```
模板3：
```Python
class Solution:
    def searchRange(self, nums, target):
        Range = [-1, -1]
        if len(nums) == 0:
            return Range

        l, r = 0, len(nums) - 1
        while l + 1 < r:
            mid = l + (r - l) // 2
            if nums[mid] < target:
                l = mid
            else:
                r = mid
        
        if nums[l] == target:
            Range[0] = l
        elif nums[r] == target:
            Range[0] = r
        else:
            return Range
        
        l, r = 0, len(nums) - 1
        while l + 1 < r:
            mid = l + (r - l) // 2
            if nums[mid] <= target:
                l = mid
            else:
                r = mid
        
        if nums[r] == target:
            Range[1] = r
        else:
            Range[1] = l
        
        return Range
```

- 使用模板 2 的解法

```Python
class Solution:
    def searchRange(self, nums, target):
        Range = [-1, -1]
        if len(nums) == 0:
            return Range
        
        l, r = 0, len(nums) - 1
        while l < r:
            mid = l + (r - l) // 2
            if nums[mid] < target:
                l = mid + 1
            else:
                r = mid

        if nums[l] == target:
            Range[0] = l
        else:
            return Range 
        
        l, r = 0, len(nums) - 1
        while l < r:
            mid = l + (r - l + 1) // 2
            if nums[mid] > target:
                r = mid - 1
            else:
                l = mid
            
        Range[1] = l
        return Range
```

### [search-insert-position](https://leetcode-cn.com/problems/search-insert-position/)

> 给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。

- 使用模板 1，若不存在，左边界为第一个大于目标值的索引（插入位置），右边界为最后一个小于目标值的索引

```Python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        
        l, r = 0, len(nums) - 1
        
        while l <= r:
            mid = l + (r - l) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] > target:
                r = mid - 1
            else:
                l = mid + 1
        
        return l
```

### [search-a-2d-matrix](https://leetcode-cn.com/problems/search-a-2d-matrix/)

> 编写一个高效的算法来判断  m x n  矩阵中，是否存在一个目标值。该矩阵具有如下特性：
>
> 1. 每行中的整数从左到右按升序排列。
>
> 2. 每行的第一个整数大于前一行的最后一个整数。

- 两次二分，首先定位行数，接着定位列数

```Python
class Solution:
    def searchMatrix(self, matrix: List[List[int]], target: int) -> bool:
        
        if len(matrix) == 0 or len(matrix[0]) == 0:
            return False
        
        l, r = 0, len(matrix) - 1
        
        while l <= r:
            mid = l + (r - l) // 2
            if matrix[mid][0] == target:
                return True
            elif matrix[mid][0] < target:
                l = mid + 1
            else:
                r = mid - 1
        
        row = r
        l, r = 0, len(matrix[0]) - 1
        while l <= r:
            mid = l + (r - l) // 2
            if matrix[row][mid] == target:
                return True
            elif matrix[row][mid] < target:
                l = mid + 1
            else:
                r = mid - 1
        
        return False
```

### [find-minimum-in-rotated-sorted-array](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转，例如，数组 [0, 1, 2, 4, 5, 6, 7] 可能变为 [4, 5, 6, 7, 0, 1, 2]。请找出其中最小的元素。假设数组中无重复元素。

- 使用二分搜索，当中间元素大于右侧元素时意味着拐点即最小元素在右侧，否则在左侧

```Python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        
        l , r = 0, len(nums) - 1
        
        while l < r:
            mid = l + (r - l) // 2
            if nums[mid] > nums[r]: # 数组有重复时，若 nums[l] == nums[mid] == nums[r]，无法判断移动方向
                l = mid + 1
            else:
                r = mid
        
        return nums[l]
```

### [find-minimum-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转，例如，数组 [0, 1, 2, 4, 5, 6, 7] 可能变为 [4, 5, 6, 7, 0, 1, 2]。请找出其中最小的元素。数组中可能包含重复元素。

```Python
class Solution:
    def findMin(self, nums: List[int]) -> int:
        
        l , r = 0, len(nums) - 1
        
        while l < r:
            mid = l + (r - l) // 2
            if nums[mid] > nums[r]:
                l = mid + 1
            elif nums[mid] < nums[r] or nums[mid] != nums[l]:
                r = mid
            else: # nums[l] == nums[mid] == nums[r]
                l += 1
        
        return nums[l]
```

### [search-in-rotated-sorted-array](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转，例如，数组 [0, 1, 2, 4, 5, 6, 7] 可能变为 [4, 5, 6, 7, 0, 1, 2]。搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回  -1。假设数组中不存在重复的元素。

```Python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        
        l , r = 0, len(nums) - 1
        
        while l <= r:
            mid = l + (r - l) // 2
            if nums[mid] == target:
                return mid
            elif nums[mid] > target:
                if nums[l] > target and nums[mid] > nums[r]:
                    l = mid + 1
                else:
                    r = mid - 1
            else:
                if nums[r] < target and nums[mid] < nums[l]:
                    r = mid - 1
                else:
                    l = mid + 1
        return -1
```

### [search-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)

> 假设按照升序排序的数组在预先未知的某个点上进行了旋转，例如，数组 [0, 0, 1, 2, 2, 5, 6] 可能变为 [2, 5, 6, 0, 0, 1, 2]。编写一个函数来判断给定的目标值是否存在于数组中，若存在返回  true，否则返回  false。数组中可能包含重复元素。

```Python
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        
        l , r = 0, len(nums) - 1
        
        while l <= r:
            if nums[l] == nums[r] and nums[l] != target:
                l += 1
                r -= 1
                continue
            mid = l + (r - l) // 2
            if nums[mid] == target:
                return True
            elif nums[mid] > target:
                if nums[l] > target and nums[mid] > nums[r]:
                    l = mid + 1
                else:
                    r = mid - 1
            else:
                if nums[r] < target and nums[mid] < nums[l]:
                    r = mid - 1
                else:
                    l = mid + 1
        return False
```

## 隐含的二分搜索

有时用到二分搜索的题目并不会直接给你一个有序数组，它隐含在题目中，需要你去发现或者构造。一类常见的隐含的二分搜索的问题是求某个有界数据的最值，以最小值为例，当数据比最小值大时都符合条件，比最小值小时都不符合条件，那么符合/不符合条件就构成了一种有序关系，再加上数据有界，我们就可以使用二分搜索来找数据的最小值。注意，数据的界一般也不会在题目中明确提示你，需要你自己去发现。

### [koko-eating-bananas](https://leetcode-cn.com/problems/koko-eating-bananas/)

```Python
class Solution:
    def minEatingSpeed(self, piles: List[int], H: int) -> int:
        
        l, r = 1, max(piles)
        
        while l < r:
            mid = l + (r - l) // 2
            if sum([-pile // mid for pile in piles]) < -H:
                l = mid + 1
            else:
                r = mid
        
        return l
```

## 总结

二分搜索核心四点要素（必背&理解）

- 1、初始化：start=0、end=len-1
- 2、循环退出条件：start + 1 < end
- 3、比较中点和目标值：A[mid] ==、 <、> target
- 4、判断最后两个元素是否符合：A[start]、A[end] ? target

## 练习题

- [ ] [search-for-range](https://www.lintcode.com/problem/search-for-a-range/description)
- [ ] [search-insert-position](https://leetcode-cn.com/problems/search-insert-position/)
- [ ] [search-a-2d-matrix](https://leetcode-cn.com/problems/search-a-2d-matrix/)
- [ ] [first-bad-version](https://leetcode-cn.com/problems/first-bad-version/)
- [ ] [find-minimum-in-rotated-sorted-array](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array/)
- [ ] [find-minimum-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/find-minimum-in-rotated-sorted-array-ii/)
- [ ] [search-in-rotated-sorted-array](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)
- [ ] [search-in-rotated-sorted-array-ii](https://leetcode-cn.com/problems/search-in-rotated-sorted-array-ii/)
