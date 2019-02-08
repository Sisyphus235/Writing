Question 170.
Design and implement a TwoSum class. It should support the following operations: add and find.
* add - Add the number to an internal data structure.
* find - Find if there exists any pair of numbers which sum is equal to the value.

Example:
```
add(1); add(3); add(5);
find(4) -> true
find(7) -> false
```

# 题目分析
## tag
类别是 arrays and strings，特点是连续存储空间中 members 的调度。
## 题目
本题要设计一个类，实现两种功能：插入和搜索。类需要设计一种数据结构，用于存储被插入的元素，同时支持一对整数和的检索。
## 思路
示例：插入 1，插入 3，插入 5，检索和 4 为真，检索和 7 为假。
1. 本题要平衡插入速度和检索速度，首先思考数据存储的内部结构。基本存储方式大概有 array 连续存储，linked list 分散存储，hash table 分散存储等。array 的优势是检索单一元素效率高，linked list 的优势是修改存储内容效率高，hash table 的优势是插入和检索元素都相对较快，但需要解决 hash clash 问题。本题的插入和检索都不需要顺序，这几种存储方式都可以考虑。
插入功能先要思考相同元素如何处理？效率最高的是找到相同元素后不记录，但这和本题功能冲突，例如插入 3，再插入 3，如果不记录，则类中信息是“有元素 3，不知道个数”，这样如果搜索 6，无法判断真假。所以插入要记录元素个数，如果用 array 记录每一个插入元素，则无法快速获得同一元素个数信息，当然可以用其他方式解决，但要牺牲效率，暂不考虑。如果用 linked list，同一元素个数信息同样无法直接获得，也可以用其他方式解决，同样牺牲效率，暂不考虑。如果用 hash table，可以记录同一元素的个数信息。
检索功能一定会遍历全部存储信息，结合 leetcode 1 和 167，hash table 是相对效率比较高的。
所以最终决定使用 hash table 作为解题类的内部存储功能。

2. 如果要提高时间效率，可以用空间来换取。假如进入这个类的元素有大小范围，例如 0 - 100，则可以创建一个 100 个元素的 array，每个位置的初始值为 0，插入功能将对应位置的值加一，搜索则直接遍历有序 array 快速获得真假值。


# Python
```python
from collections import defaultdict

class twoSum3:

    def __init__(self):
        # 初始化默认值是 int 类型的 dict
        self.numbers = defaultdict(int)  

    def add(self, number):
        self.numbers[number] += 1

    def find(self, target):
        for one in self.numbers.keys():
            other = target - one
            # 如果 pair 值和当前值相等，则已有该元素个数不能小于 2
            if other == one and self.numbers[one] < 2:
                continue
            if other not in self.numbers:
                continue
            return True
        return False

```

# Java
```java
import java.util.HashMap;

public class twoSum3 {
    private HashMap<Integer, Integer> container = new HashMap<Integer, Integer>();

    public void add(int number) {
        if (!container.containsKey(number)) {
            container.put(number, 1);
        } else {
            container.put(number, container.get(number) + 1);
        }
    }

    public boolean find(int number) {
        for (int i : container.keySet()) {
            int match = number - i;
            if (container.containsKey(match)) {
                if (i == match && container.get(i) < 2) {
                    continue;
                }
                return true;
            }
        }
        return false;
    }
}

```

# 相关题目
[LeetCode 1. Two Sum](https://www.jianshu.com/p/b95a3886a026)
[LeetCode 167. Two Sum II](https://www.jianshu.com/p/283fea3b05de)