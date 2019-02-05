Question 167.
Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.
The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2.
Note:
* Your returned answers (both index1 and index2) are not zero-based.
* You may assume that each input would have exactly one solution and you may not use the same element twice.

Example:
```
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore index1 = 1, index2 = 2.
```
# 题目分析
## tag
类别是 arrays and strings，特点是连续存储空间中 members 的调度。
## 题目
本题是升序 integer array，找寻 2 members 的和等于目标值，返回 2 members 从 1 开始的 index，index 要求升序。
## 思路
示例：[2,7,11,15]
1. 直接的暴力解法是找出所有的 integer pair 然后看是否有加和等于目标值的情况。
[2,7], [2,11], [2,15], [7,11], [7,15], [11,15]
2 + 7 = 9，所以返回 [1, 2]
数学中的组合可以计算出所有要枚举的可能性个数，算法复杂度是 O(n^2)

2. 观察上述解法找冗余判断，发现 [2,7] 的组合已经等于目标值，后续所有比 7 大的数字与 2 的组合都一定比目标值大，无需罗列判断。
观察结果推出优化方向是利用好数组的升序特征，找到两个起始点，依据升序替换数字。
而一个数组的两个起始点最对应的就是开始和终止的位置，于是设置 start = 2, end = 15。接下来 2 + 15 > 9，所以需要一个用一个较小的数字替换这里的两个值，没有值比start 更小，只有 end 可以被替换为更小的值。于是用 11 替换 15， 2 + 11 > 9，用 7 替换 11，2 + 7 = 9，返回 [1, 2]。
刚才的例子不断被替换的是 end，因为加和一直大于目标值，同理，如果加和小于目标值，则替换 start。
设置了 start 和 end，不断遍历数组寻找替换值，算法复杂度是 O(n)。

3. 虽然上述方法已经是理想的算法复杂度了，因为解决这个问题一定要遍历一次数组，但是还可以在细节上继续优化。
上面替换的决策是通过加和计算后与目标值比较实现的，实际上可以先判断替换值和目标值的关系，可以省略加和的计算资源。
但是这样的优化同时引入多一次比较的逻辑复杂性，而没有数量级或者量级上的收益，可以不做优化。


# Python
```python
def sorted_two_sum(numbers: list, target: int) -> list:
    """
    :param numbers: input integer arrays
    :param target: 
    :return: 1-based index sorted array
    """
    start = 0
    end = len(numbers) - 1
    while start < end:
        current_sum = numbers[start] + numbers[end]
        if current_sum == target:
            return [start + 1, end + 1]
        elif current_sum < target:
            start += 1
        else:
            end -= 1
    return []
```

# Java
```java
public class twoSum2 {
    public int[] solution(int[] numbers, int target) {
        int start = 0;
        int end = numbers.length - 1;
        while (start < end) {
            int current_sum = numbers[start] + numbers[end];
            if (current_sum == target) {
                return new int[] {start + 1, end + 1};
            } else if (current_sum < target) {
                ++start;  
            } else {
                --end;
            }
        }
        return new int[] {};
    }
}
```

# 相关题目
[LeetCode 1. Two Sum](https://www.jianshu.com/p/b95a3886a026)