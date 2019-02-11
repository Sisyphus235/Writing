Question 15.
Given an array `nums` of n integers, are there elements a, b, c in `nums` such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero.

**Note**:

The solution set must not contain duplicate triplets.

**Example**:
```
Given array nums = [-1, 0, 1, 2, -1, -4],

A solution set is:
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

# 题目分析
## tag
类别是 arrays and strings，特点是连续存储空间中 members 的调度。
## 题目
本题要在连续存储空间中寻找 3 个元素，要求 3 个元素的和为 0。扩展一点，可以要求 3 个元素值为输入值，这样和 Two Sum 的题更相似。
## 思路
示例：[-1, 0, 1, 2, -1, -4]
1. 化繁为简，在已知解法上衍生是算法很重要的理念。这道题可以和 Two Sum 建立连接。可以遍历一次 array，取 0 减去当前值为目标值，寻找后续存储空间有没有能满足这个目标值的 Two Sum。
先取第一个值 -1，目标值是 0 - (-1) = 1，在 [0, 1, 2, -1, -4] 中基于 Two Sum 寻找加和是 1 的组合，有 (0, 1), (2, -1)，所以有两个方案(-1, 0, 1), (-1, 2, -1)；
再取第二个值 0，目标值是 0 - 0 = 0，在 [1, 2, -1, -4] 中基于 Two Sum 寻找加和是 0 的组合，有 (1, -1)，所以有一个方案 (0, 1, -1)；
接着取第三个值 1，目标值是 0 - 1 = -1，在 [2, -1, -4] 中基于 Two Sum 寻找加和是 -1 的组合，无；
然后取第四个值 2，目标值是 0 - 2 = -2，在 [-1, -4] 中基于 Two Sum 寻找加和是 -2 的组合，无；
剩余元素只有 2 个，停止遍历；
最后分析所有组合去重，共 3 个方案(-1, 0, 1), (-1, 2, -1), (0, 1, -1)，第一个和第三个重复，所以返回[[-1, 0, 1], [-1, -1, 2]]。
找出解决方案的算法复杂度是 O(n^2)，去重要比较所有可能解。

2. 分析前面的解法，找出解决方案的效率是比较令人满意的，基本上达到了复杂度的极限，但是在去重方面做得不够好。
有没有办法在生成解决方案的时候就解决重复的问题？这要分析重复的产生原因，是因为 array 无序排列导致重复，所以可以尝试先对 array 进行排序。
首先排序得到 [-4, -1, -1, 0, 1, 2]；
先取第一个值 -4，目标值是 0 - (-4) = 4，在 [-1, -1, 0, 1, 2] 中基于 Two Sum 寻找加和是 4 的组合，无；
再取第二个值 -1，目标值是 0 - (-1) = 1，在 [-1, 0, 1, 2] 中基于 Two Sum 寻找加和是 1 的组合，有 (-1, 2), (0, 1)，所以有两个方案(-1, -1, 2), (-1, 0, 1)；
第三个值 -1 和上一个相等，跳过；
然后取第四个值 0，目标值是 0 - (0) = 0，在 [1, 2] 中基于 Two Sum 寻找加和是 0 的组合，无；
剩余元素只有 2 个，停止遍历；
直接返回解决方案 [[-1, 0, 1], [-1, -1, 2]]。
排序的算法复杂度可以达到 O(nlogn)，寻找解决方案的算法复杂度是 O(n^2)，最终复杂度是 O(n^2)。

3. 第二种思路已经可以较好的完成任务，继续思考可以优化的部分，因为 array 已经排序，所以找目标值的时候不必须完成后续所有的组合，而是可以停止在第一个值大于目标值的一半时候，因为如果当前值大于目标值的一半，后续值一定大于当前值，总和必然大于目标值。


# Python
```python
from typing import List

def three_sum(nums: List[int]) -> List[List[int]]:
    length = len(nums)
    # array 排序   
    nums.sort()
    # 初始化返回 list
    response = []
    # 三个数之和，所以遍历到倒数第三个数
    for i in range(length)[:-2]:
        # 如果是第一个数则进入，不是第一个数则需要和前一个数不相同才能进入
        if i == 0 or nums[i] != nums[i - 1]:
            # 获取目标值
            target = 0 - nums[i]
            # 当前值下一个是最小指针, array 最后一个是最大指针
            start, end = i + 1, length -1
            # 最小最大指针相交前不断循环
            while start < end:
                # 如果最小指针大于目标值的一半，或者最大指针小于目标值的一半，结束当前循环
                if nums[start] > target // 2 or nums[end] < target // 2:
                    break
                # 获得当前两个指针的元素和
                current_sum = nums[start] + nums[end]
                # 元素和符合目标值，则将三个数字加入返回 list
                if current_sum == target:
                    response.append([nums[i], nums[start], nums[end]])
                    # 如果最小值指针下一个和当前相等，则需要跳过
                    while start < end and nums[start] == nums[start + 1]:
                        start += 1
                    # 如果最大值指针前一个和当前相等，则需要跳过
                    while start < end and nums[end] == nums[end - 1]:
                        end -= 1
                    # 最小指针前进一个，最大指针后退一个，寻找后续解决方案
                    start += 1
                    end -= 1
                elif current_sum < target:
                    start += 1
                else:
                    end -= 1

    return response

```

# Java
```java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.lang.Math;

public List<List<Integer>> solution(int[] nums) {
    // array 排序   
    Arrays.sort(nums);
    // 初始化返回 list
    List<List<Integer>> response = new ArrayList<List<Integer>>();
    // 三个数之和，所以遍历到倒数第三个数
    for (int i = 0; i < nums.length - 2; i++) {
        // 如果是第一个数则进入，不是第一个数则需要和前一个数不相同才能进入
        if (i == 0 || nums[i] != nums[i - 1]) {
            // 获取目标值
            int target = 0 - nums[i];
            // 当前值下一个是最小指针
            int start = i + 1;
            // array 最后一个是最大指针
            int end = nums.length - 1;
            // 最小最大指针相交前不断循环
            while (start < end) {
                // 如果最小指针大于目标值的一半，或者最大指针小于目标值的一半，结束当前循环
                if (nums[start] > Math.floor(target / 2) || nums[end] < Math.floor(target / 2)) {
                    break;
                }
                // 获得当前两个指针的元素和
                int currentSum = nums[start] + nums[end];
                if (currentSum == target) {
                    // 元素和符合目标值，则将三个数字加入返回 list
                    response.add(Arrays.asList(nums[i], nums[start], nums[end]));
                    // 如果最小值指针下一个和当前相等，则需要跳过
                    while (start < end && nums[start + 1] == nums[start]) {
                        start++;
                    }
                    // 如果最大值指针前一个和当前相等，则需要跳过
                    while (start < end && nums[end] == nums[end - 1]) {
                        end--;
                    }
                    // 最小指针前进一个，最大指针后退一个，寻找后续解决方案
                    start++;
                    end--;
                } else if (currentSum < target) {
                    start++;
                } else {
                    end--;
                }
            }
        }
    }
    return response;
}

```

# 相关题目
[LeetCode 1. Two Sum](https://www.jianshu.com/p/b95a3886a026)
[LeetCode 167. Two Sum II](https://www.jianshu.com/p/283fea3b05de)
[LeetCode 170. Two Sum III](https://www.jianshu.com/p/0dcc73159e59)