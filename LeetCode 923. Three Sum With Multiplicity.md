Question 923.
Given an integer array A, and an integer target, return the number of tuples i, j, k  such that i < j < k and A[i] + A[j] + A[k] == target.

As the answer can be very large, return it modulo 10^9 + 7.

Example 1:
```
Input: A = [1,1,2,2,3,3,4,4,5,5], target = 8
Output: 20
Explanation: 
Enumerating by the values (A[i], A[j], A[k]):
(1, 2, 5) occurs 8 times;
(1, 3, 4) occurs 8 times;
(2, 2, 4) occurs 2 times;
(2, 3, 3) occurs 2 times.
```
Example 2:
```
Input: A = [1,1,2,2,2,2], target = 5
Output: 12
Explanation: 
A[i] = 1, A[j] = A[k] = 2 occurs 12 times:
We choose one 1 from [1,1] in 2 ways,
and two 2s from [2,2,2,2] in 6 ways.
```

Note:
1. 3 <= A.length <= 3000
2. 0 <= A[i] <= 100
3. 0 <= target <= 300

# 题目分析
## tag
类别是 arrays and strings，特点是连续存储空间中 members 的调度。
## 题目
本题要在连续存储空间中寻找 3 个元素，要求 3 个元素的和等于目标值的个数，元素可能重复。
## 思路
示例：
A = [1,1,2,2,3,3,4,4,5,5]
target = 8
1. 相比于之前的三数和不同条件的题目，本题叠加了相同元素组合数量的求解。总体思路是先排序，找到目标组合，获得组合中元素的数量计算组合由不同元素组成的数量，再加总所有可能性。
首先排序，A = [1,1,2,2,3,3,4,4,5,5]；
接着取第一个元素 1，1 * 3 = 3，小于目标值 8。最小指针指向 1，最大指针指向 5，当前和 1 + 1 + 5 = 7，小于目标值 8，最小指针右移指向 2。当前和 1 + 2 + 5 = 8，等于目标值 8，可能组合记录 (1, 2, 5)。接着最大指针和最小指针分别向左、右移动跳过重复的元素，最小指针指向 3， 最大指针指向 4，当前和 1 + 3 + 4 = 8， 等于目标值 8，可能组合记录 (1, 3, 4)。接着最大指针和最小指针分别向左、右移动跳过重复的元素，最小指针指向 4， 最大指针指向 3，最小指针大于了最大指针，当前结束；
然后取第二个元素 1，等于前面的元素，跳过；
继续取第三个元素 2，2 * 3 = 6，小于目标值 8。最小指针指向 2，最大指针指向 5，当前和 2 + 2 + 5 = 9，大于目标值 8，最大指针左移跳过重复元素指向 4。当前和 2 + 2 + 4 = 8，等于目标值 8，可能组合记录 (2, 2, 4)。接着最大指针和最小指针分别向左、右移动跳过重复的元素，最小指针指向第一个 3， 最大指针指向第二个 3，当前和 2 + 3 + 3 = 8，等于目标值 8，可能组合记录 (2, 3, 3)；
接着取第四个元素 2，等于前面的元素，跳过；
再取第五个元素 3，3 * 3 = 9，大于目标值 8，终止，当前可能组合记录是 (1, 2, 5), (1, 3, 4), (2, 2, 4), (2, 3, 3)；
统计各个元素的个数 { 1: 2, 2: 2, 3: 2, 4: 2, 5: 2}；
计算所有可能性：(1, 2, 5) 的可能性是 2 * 2 * 2 = 8，(1, 3, 4) 的可能性是 2 * 2 * 2 = 8，(2, 2, 4) 的可能性是 1 * 1 * 2 = 2，(2, 3, 3)的可能性是 1 * 1 * 2 = 2，总计 8 + 8 + 2 + 2 = 20。
算法复杂度是 O(n^2)

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
2. 发现上面的 python 代码非常繁琐，还有优化空间。主要冗余的部分是指针检索的时候跳过重复的部分。调整上面解法的逻辑顺序，先获得每个元素个数的统计，接着去重后再排序，这样的列表更容易处理。

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