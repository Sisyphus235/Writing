Question 16.
Given an array `nums` of n integers and an integer `target`, find three integers in nums such that the sum is closest to `target`. Return the sum of the three integers. You may assume that each input would have exactly one solution.

**Example**:
```
Given array nums = [-1, 2, 1, -4], and target = 1.

The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

# 题目分析
## tag
类别是 arrays and strings，特点是连续存储空间中 members 的调度。
## 题目
本题要在连续存储空间中寻找 3 个元素，要求 3 个元素的和与目标值最接近。
## 思路
示例：
nums = [-1, 2, 1, -4]
target = 1
1. 延续 No.15 的解法，当时已经扩展到三数之和等于目标值的解法，思路是先排序，然后通过最小指针和最大指针寻找符合条件的三个数的组合。本题在此基础之上，只要变换符合的条件即可，从等于确定值，变为计算和确定值之间的差值，始终记录差值最小的组合。
首先排序，nums = [-4, -1, 1, 2]；
取第一个元素 -4，最小指针指向 -1，最大指针指向 2，和是 -4 + (-1) + 2 = -3，差值是 |1 - (-3)| = 4，记录组合是 (-4, -1, 2)，和 -3 < 目标值 1，所以最小指针右移。最小指针指向 1，和是 -4 + 1 + 2 = -1，差值是 |1 - (-1)| = 2，差值更小，记录组合是 (-4, 1, 2)，和 -1 < 目标值 1，最小指针右移，最小指针和最大指针相遇，本次结束；
取第二个元素 -1，最小指针指向 1， 最大指针指向 2，和是 -1 + 1 + 2 = 2，差值是 |1 - 2| = 1，差值更小，记录组合是 (-1, 1, 2)，和 2 > 目标值 1，所以最大指针左移，最大指针和最小指针相遇，本次结束；
第三个元素后面只剩余一个元素，终止。
所以最小的差值是 1，目标组合是 (-1, 1, 2)，目标组合的加和是 2，算法复杂度和 No.15 一样，O(n^2)。



# Python
```python
import sys
from typing import List

def three_sum_closest(nums: List[int], target: int) -> int:
    length = len(nums)
    # 初始化记录，差值设置为最大整数，三个数和为 0
    record = [sys.maxsize, 0]
    # array 排序  
    nums.sort()
    # 三个数之和，所以遍历到倒数第三个数
    for i in range(length)[:-2]:
        # 当前值下一个是最小指针, array 最后一个是最大指针
        start, end = i + 1, length - 1
        # 最小最大指针相交前不断循环
        while start < end:
            # 当前三个数的和
            current_sum = nums[i] + nums[start] + nums[end]
            # 和与目标值的差值
            diff = abs(target - current_sum)
            # 如果差值小于记录的差值，则更新记录
            if diff < record[0]:
                record = [diff, current_sum]
            # 如果和小于目标值，则最小指针右移，反之最大指针左移
            if current_sum < target:
                start += 1
            else:
                end -= 1
    return record[1]
```

# Java
```java
import java.util.Arrays;

public int solution(int[] nums, int target) {
    // array 排序
    Arrays.sort(nums);
    // 初始化记录，diff 设置为最大 int 值，三个数和为 0
    int[] record = new int[]{Integer.MAX_VALUE, 0};
    // 三个数之和，所以遍历到倒数第三个数
    for (int i = 0; i < nums.length - 2; i++) {
        // 当前值下一个是最小指针
        int start = i + 1;
        // array 最后一个是最大指针
        int end = nums.length - 1;
        // 最小最大指针相交前不断循环
        while (start < end) {
            // 当前三个数之和
            int currentSum = nums[i] + nums[start] + nums[end];
            // 当前和与目标值的差值
            int diff = Math.abs(currentSum - target);
            // 如果差值小于记录的差值，则更新记录
            if (diff < record[0]) {
                record = new int[]{diff, currentSum};
            }
            // 如果和小于目标值，则最小指针右移，反之最大指针左移
            if (currentSum < target) {
                start++;
            } else {
                end--;
            }
        }
    }
    return record[1];
}

```

# 相关题目
[LeetCode 1. Two Sum](https://www.jianshu.com/p/b95a3886a026)
[LeetCode 167. Two Sum II](https://www.jianshu.com/p/283fea3b05de)
[LeetCode 170. Two Sum III](https://www.jianshu.com/p/0dcc73159e59)
[LeetCode 15. Three Sum](https://www.jianshu.com/p/96028e4dd8b6)