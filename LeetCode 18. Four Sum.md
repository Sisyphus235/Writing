Question 18.
Given an array nums of n integers and an integer target, are there elements a, b, c, and d in nums such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target.

Note:
The solution set must not contain duplicate quadruplets.

Example:
```bash
Given array nums = [1, 0, -1, 0, -2, 2], and target = 0.

A solution set is:
[
  [-1,  0, 0, 1],
  [-2, -1, 1, 2],
  [-2,  0, 0, 2]
]
```

# 题目分析
## tag
类别是 arrays and strings，特点是连续存储空间中 members 的调度。
## 题目
本题要在连续存储空间中寻找 4 个元素，要求 4 个元素的和等于目标值，元素可能重复。
## 思路
示例：
nums = [1, 0, -1, 0, -2, 2]
target = 0
1. 从 2Sum 到 3Sum，已经完成了一次数量增加的分析，4Sum 思路可以一致，这样在 3Sum 的基础上要做多做一次循环，所以算法复杂度从 O(n^2) 上升到了 O(n^3)。
先排序 nums = [-2, -1, 0, 0, 1, 2]
* 第一个数的遍历从 -2 开始：
    * 第二个数从 -1 开始。最小指针指向第一个 0，最大指针指向 2，当前和是 (-2) + (-1) + 0 + 2 = -1，小于目标值，所以最小指针右移，跳过重复指向 1。当前和是 (-2) + (-1) + 1 + 2 = 0，等于目标值，记录当前组合。最大最小指针同时移动，发现最小指针大于最大指针，结束当前遍历；
    * 第二个数指向 0，最小指针指向第二个 0，最大指针指向 2，当前和是 (-2) + 0 + 0 + 2 = 0，等于目标值，记录当前组合。最大最小指针同时移动，发现最小指针等于最大指针，结束当前遍历；
    * 第二个数跳过重复指向 1，剩余数字不足 2 个，结束当前遍历；
* 第一个数指向 -1：
    * 第二个数指向 0。最小指针指向第二个 0，最大指针指向 2，当前和是 (-1) + 0 + 0 + 2 = 1，大于最大值，最大指针左移指向 1。当前和是 (-1) + 0 + 0 + 1 = 0，等于目标值，记录当前组合，最大最小指针同时移动，最小指针大于最大指针，结束当前遍历；
    * 第二个数跳过重复指向 1，剩余数字不足 2 个，结束当前遍历；
* 第一个数指向 0：
    * 第二个数指向第二个 0，最小指针指向 1，最大指针指向 2，当前和是 0 + 0 + 1 + 2 = 3，大于最大值，最大指针左移，最小指针等于最大指针，结束当前遍历；
* 第一个数跳过重复指向 1，剩余数字不足 3 个，结束当前遍历；
* 所有记录组合是 [-1,  0, 0, 1], [-2, -1, 1, 2], [-2,  0, 0, 2]，没有重复，直接返回。

```python
import sys
from typing import List
from collection import defaultdict

def three_sum_with_multiplicity(nums: List[int], target: int) -> int:
    def _skip_left(left: int, right: int) -> int:
        """
        略过重复的最小指针
        """
        while left < right and nums[left] == nums[left + 1]:
            left += 1
        return left

    def _skip_right(left: int, right: int) -> int:
        """
        略过重复的最大指针
        """
        while left < right and nums[right] == nums[right - 1]:
            right -= 1
        return right

    # 初始化符合条件记录个数为 0
    count = 0
    # 根据题中条件返回 bound 的余数
    bound = 10 ** 9 + 7
    # 记录符合目标值的三个数的组合情况
    record = []
    # 输入 array 排序
    nums.sort()
    length = len(nums)
    # 三个数之和，所以遍历到倒数第三个数
    for i in range(length)[:-2]:
        # 如果不是第一个数且等于 array 前一个数，不会产生新的组合，跳过
        if i == 0 or nums[i] != nums[i - 1]:
            # 如果第一个数的三倍已经大于目标值，则跳出循环，因为后面的数至少都等于第一个数
            if nums[i] * 3 > target:
                break
            # 当前值下一个是最小指针, array 最后一个是最大指针
            left, right = i + 1, length - 1
            # 最小最大指针相交前不断循环
            while left < right:
                # 当前三个数的和
                current_sum = nums[i] + nums[left] + nums[right]
                # 当前和等于目标值，则记录当前组合，移动最小/大指针，跳过重复
                if current_sum == target:
                    record.append((nums[i], nums[left], nums[right]))
                    left = _skip_left(left, right)
                    right = _skip_right(left, right)
                    left += 1
                    right -= 1
                # 如果当前和小于目标值，则最小指针右移，跳过重复
                elif current_sum < target:
                    left = _skip_left(left, right)
                    left += 1
                # 如果当前和大于等于目标值，则最大值指针左移
                else:
                    right = _skip_right(left, right)
                    right -= 1
    # 统计元素个数
    element = defaultdict(int)
    for e in nums:
        element[e] += 1
    # 计算符合条件组合的个数
    for a, b, c in record:
        if a == b and b == c:
            # 如果三个数相等，则可能性是组合的计算 n * (n-1) * (n-2) / (3 * 2)
            count = (count + element[a] * (element[a] - 1) * (element[a] - 2) // 6) % bound
        elif a == b:
            # 如果两个数相等，则相等两个数的可能性是组合的计算 n * (n - 1) / 2
            count = (count + element[a] * (element[a] - 1) // 2 * element[c]) % bound
        elif b == c:
            count = (count + element[c] * (element[c] - 1) // 2 * element[a]) % bound
        else:
            # 如果都不相等，连乘即可
            count = (count + element[a] * element[b] * element[c]) % bound

    return count
```
2. 时间复杂度过高，如果希望降低时间复杂度，一定是通过提升可用空间来换取时间的。 

# Python
```python
def three_sum_with_multiplicity_optimize(A: List[int], target: int) -> int:
    from collections import Counter
    bound = 10 ** 9 + 7
    element = Counter(A)
    A = sorted(element.items(), key=lambda x: x[0])
    res = 0
    for i in range(len(A)):
        j = i
        k = len(A) - 1
        new_target = target - A[i][0]
        while j <= k:
            if A[j][0] + A[k][0] < new_target:
                j += 1
            elif A[j][0] + A[k][0] > new_target:
                k -= 1
            else:
                if A[i][0] == A[k][0]:
                    res = (res + A[i][1] * (A[i][1] - 1) * (A[i][1] - 2) // 6) % bound
                elif A[i][0] == A[j][0]:
                    res = (res + A[k][1] * A[i][1] * (A[i][1] - 1) // 2) % bound
                elif A[j][0] == A[k][0]:
                    res = (res + A[i][1] * A[j][1] * (A[j][1] - 1) // 2) % bound
                else:
                    res = (res + A[i][1] * A[j][1] * A[k][1]) % bound
                j += 1
                k -= 1
    return res
```

# Java
```java
// TODO
```

# 相关题目
[LeetCode 1. Two Sum](https://www.jianshu.com/p/b95a3886a026)
[LeetCode 167. Two Sum II](https://www.jianshu.com/p/283fea3b05de)
[LeetCode 170. Two Sum III](https://www.jianshu.com/p/0dcc73159e59)
[LeetCode 15. Three Sum](https://www.jianshu.com/p/96028e4dd8b6)
[LeetCode 16. Three Sum Closest](https://www.jianshu.com/p/fa288d632ca4)
[LeetCode 259. Three Sum Smaller](https://www.jianshu.com/p/9296ce65b9cb)