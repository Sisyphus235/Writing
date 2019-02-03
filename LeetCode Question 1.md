Question 1
Given an array of integers, return indices of the two numbers such that they add up to a specific target.
You may assume that each input would have exactly one solution, and you may not use the same element twice.
Example:
```
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```

TDD(Test-driven development) analysis:
```python
1. nums = [2, 7, 11, 15], target = 9
>>> [0, 1]
2. nums = [2, 7, 11, 15], target = 8
>>> []
3.nums = [3, 2, 4, 15], target = 6
>>> [1, 2]
if that "each input would have exactly one solution" is deleted 
from the condition, then extra tests should be listed below:
4. nums = [2, 7, 2, 11, 15], target = 9
>>> [[0, 1], [1, 2]]
5. nums = [2, 7, 4, 5], target = 9
>>> [[0, 1], [2, 3]]
6. nums = [2, 6, 4, 4], target = 8
>>> [[0, 1], [2, 3]]
```
# 1.Python
## 1.1直接解法（Python3）:
最直接的解决办法是循环遍历数组，寻找两个元素之和等于目标值的答案。
```python
def twoSum(nums, target):
    i = 0
    while i < len(nums):
        temp = target - nums[i]
        #根据第一个数值寻找符合条件的第二个数值
        j = i + 1
        while j < len(nums):
            if nums[j] == temp:
                return [i, j]
            #找到符合条件的第二个数值后，返回两个数值index的数组
            j += 1
        i += 1
    return []
    #如果没有找到则返回为空
```
时间复杂度O(n^2)(注：n square)，对每一个元素，遍历数组寻找匹配的元素；空间复杂度O(1)，每次只记录temp值。
## 1.2 Hash Table 
这个解法的时间复杂度太高，而空间复杂度很低，需要思考优化的解决方案。然后分析循环遍历2次的目的，第一次是记录index，随机找出一个数，第二次是根据第一个数找是否存在与之匹配的数，并返回它的index。寻找两个数是题中的要求，不能简化处理，但是某个数的index和值的数据结构却可以优化，这种key和value的情形很容易想到hash table，使得我们可以在时间复杂度和空间复杂度上做平衡。
```python
def twoSumHash(nums, target):
    table = {nums[x] : x for x in range(len(nums))}
    #建立一个数与其index的哈希表
    i = 0
    while i < len(nums):
        temp = target - nums[i]
        #根据第一个数寻找符合条件的第二个数
        if temp in table and table[temp] != i:
        #第二个数存在且和第一个数不同（题中条件）
            return [i, table[temp]]
            #找到符合条件的第二个数后，返回两个数index的数组
        i += 1
    return []
    #如果没有找到则返回为空    
```
时间复杂度O(n)，对每一个元素，遍历数组寻找匹配的元素，哈希表把两次遍历n平方的复杂度降低到n的复杂度；空间复杂度O(n)，哈希表提高了空间复杂度。
这样就通过哈希表平衡了上一种方法的时间和空间复杂度。
## 1.3 Enumerate()
在Hash Table基础上，还可以代入python built-in函数来实现。Enumerate(sequence, [start = 0])
sequence： 一个序列、迭代器或其他支持迭代对象；
start：下标起始位置，default是0。
返回值：enumerate的枚举对象，实例如下
```python
students = ['a', 'b', 'c']
list(enumerate(students))
>>>[(0, 'a'), (1, 'b'), (2, 'c')]
list(enumerate(students, start = 1))
>>>[(1, 'a'), (2, 'b'), (3, 'c')]
```
应用enumerate()，解法如下
```python
def twoSumEnumerate(nums, target):
    table = {}
    for i, item in enumerate(nums):
    #使用内置函数enumerate产生index和数值的元组
        if target - item in table:
            return [table[target - item], i]
        table[item] = i
    return []
    #如果没有找到则返回为空    
```
该方法使用的依然是哈希表，但是将前文两步完成的哈希表转变成一步实现的哈希表。时间复杂度O(n)，空间复杂度O(n)。

# 2. Java
在前面分析的基础上，直接使用 Hash Table 解题：
```Java
public class twoSum {
    
    public int[] solution(int[] nums, int target) {
        // 哈希表用来存放遍历中出现的值和个数
        HashMap<Integer, Integer> sumMap = new HashMap<Integer, Integer>();
        for (int i = 0; i < nums.length; i++) {
            if (sumMap.containsKey(target - nums[i])) {
                return new int[]{i, sumMap.get(target - nums[i])};
            }
            sumMap.put(nums[i], i);
        }
        return new int[]{};

    }

}
```