Question 2.

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example:

```bash
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
```

# 题目分析

## tag

类别是 linked list。

## 题目

两个逆序链表存储两个正整数，求和并用同样的方式返回。

## 思路
```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
```
题目要求返回一个单向加和链表，第一个思路是尽可能回归到熟悉的整数求和。
1. 将两个单向链表表示的整数转换成真正的整数，求和，再逆向转换为逆序链表。
2. 前面的思路虽然熟悉，但消耗了很多资源在单向链表和整数的转化上。借助整数相加的思路，而不是真正转换成整数求和是正确的方向。从个位开始对应位置相加，直到两个单向链表都没有数字，就可以生成答案，这里要注意的是要有一个变量记录进位。
以题目例子来推演：
l1: (2 -> 4 -> 3)
l2: (5 -> 6 -> 4)
个位开始，l1 的个位是 2，l2 的个位是 5，2 + 5 = 7，所以最终结果的个位是 7， 无进位；
十位继续，l1 的十位是 4，l2 的十位是 6，4 + 6 = 10，所以最终结果的个位是 0， 有进位；
百位继续，l1 的百位是 3，l2 的百位是 4，3 + 4 = 7，加上进位 1 等于 8，所以最终结果的百位是 8， 无进位；
千位继续，l1 无千位，l2 无千位，结束，最终返回是 (7 -> 0 -> 8)

# python
```python
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None


def add_two_numbers(l1: ListNode, l2: ListNode) -> ListNode:
    # 标记特殊值为单向链表起始位置，这样后续才能生产单向链表其他节点
    l = ListNode(-1)
    # 标记头，返回 ListNode 的起始位置
    head = l
    # 进位值
    carry = 0

    # 如果 l1 或者 l2 或者进位有值，则返回链表继续生成
    while l1 or l2 or carry:
        # 当前数字位初始值是之前的进位，当前进位初始值为 0
        total, carry = carry, 0
        # l1 存在则当前数字位值相加
        if l1:
            total += l1.val
            l1 = l1.next
        # l2 存在则当前数字位值相加
        if l2:
            total += l2.val
            l2 = l2.next
        # 当前数字位值相加总和大于 9，则需要进位
        if total > 9:
            total -= 10
            carry = 1
        # 根据当前数字位值生成单向链表节点
        l.next = ListNode(total)
        l = l.next
    
    # 返回 ListNode 的起始位置
    return head.next
```

# java

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
 
public listNode solution(listNode l1, listNode l2) {
        listNode l = new listNode(-1);
        listNode head = l;
        int carry = 0;

        while (l1 != null || l2 != null || carry != 0) {
            int total = carry;
            carry = 0;
            if (l1 != null) {
                total += l1.val;
                l1 = l1.next;
            }
            if (l2 != null) {
                total += l2.val;
                l2 = l2.next;
            }
            if (total > 9) {
                total -= 10;
                carry = 1;
            }
            l.next = new listNode(total);
            l = l.next;
        }

        return head.next;

    }
```