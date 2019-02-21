Question 259.
Given an array of n integers nums and a target, find the number of index triplets i, j, k with 0 <= i < j < k < n that satisfy the condition nums[i] + nums[j] + nums[k] < target.

For example, given nums = [-2, 0, 1, 3], and target = 2.
Return 2. Because there are two triplets which sums are less than 2:

[-2, 0, 1]
[-2, 0, 3]
Follow up:
Could you solve it in O(n2) runtime?

# 题目分析
## tag
类别是 arrays and strings，特点是连续存储空间中 members 的调度。
## 题目
本题要在连续存储空间中寻找 3 个元素，要求 3 个元素的和小于目标值的个数。
## 思路
示例：
nums = [-2, 0, 1, 3]
target = 2
1. 延续 No.16 的解法，当时思路是先排序，然后通过最小指针和最大指针寻找符合条件的三个数的组合。本题在此基础之上，只要变换符合的条件即可，从接近目标值，变为判断是否小于目标值，并记录符合条件的组合个数。
首先排序，nums = [-2, 0, 1, 3]；
取第一个元素 -2，最小指针指向 0，最大指针指向 3，和是 -2 + 0 + 3 = 1，小于目标值 2，最大指针向左移动的所有可能都符合条件，所以不用每次再做判断，这时计算最大指针到最小指针的距离即可，最大指针 index 是 3，最小指针 index 是 1，3 - 1 = 2，记录符合条件组合的个数 2；
再取第二个元素 0，最小指针指向 1，最大指针指向 3，和是 0 + 1 + 3 = 4，大于目标值 2，最大指针向左移动，与最小指针相遇，本次结束；
第三个元素后面只剩余一个元素，终止。
所以符合要求的组合个数是 2，算法复杂度和 No.16 一样，O(n^2)，达到了题目的最严格要求。



# Python
```python
import sys
from typing import List

def three_sum_smaller(nums: List[int], target: int) -> int:
    # array 排序  
    nums.sort()
    # 初始化符合条件的个数为 0
    count = 0
    length = len(nums)
    # 三个数之和，所以遍历到倒数第三个数
    for i in range(length)[:-2]:
        # 如果第一个数已经大于目标值，则跳出循环
        if nums[i] >= target:
            break
        # 当前值下一个是最小指针, array 最后一个是最大指针
        left, right = i + 1, length - 1
        # 最小最大指针相交前不断循环
        while left < right:
            # 当前三个数的和
            current_sum = nums[i] + nums[left] + nums[right]
            # 如果当前和大于等于目标值，则最大值指针左移
            if current_sum >= target:
                right -= 1
            else:
                # 小于目标值，则计算最大最小值距离，加到符合个数的统计中
                count += right - left
                break
    return count
```

# Java
```java
import java.util.Arrays;

public int solution(int[] nums, int target) {
    // array 排序
    Arrays.sort(nums);
    # 初始化符合条件的个数为 0
    int count = 0;
    # 三个数之和，所以遍历到倒数第三个数
    for (int i = 0; i < nums.length - 2; i++) {
        # 如果第一个数已经大于目标值，则跳出循环
        if (nums[i] >= target) {
            break;
        }
        # 当前值下一个是最小指针, array 最后一个是最大指针
        int left = i + 1;
        int right = nums.length - 1;
        # 最小最大指针相交前不断循环
        while (left < right) {
            # 当前三个数的和
            int currentSum = nums[i] + nums[left] + nums[right];
            # 如果当前和大于等于目标值，则最大值指针左移
            if (currentSum >= target) {
                right -= 1;
            } else {
                # 小于目标值，则计算最大最小值距离，加到符合个数的统计中
                count += right - left;
                break;
            }
        }
    }
    return count;
}
```

# 相关题目
[LeetCode 1. Two Sum](https://www.jianshu.com/p/b95a3886a026)
[LeetCode 167. Two Sum II](https://www.jianshu.com/p/283fea3b05de)
[LeetCode 170. Two Sum III](https://www.jianshu.com/p/0dcc73159e59)
[LeetCode 15. Three Sum](https://www.jianshu.com/p/96028e4dd8b6)
[LeetCode 16. Three Sum Closest](https://www.jianshu.com/p/fa288d632ca4)