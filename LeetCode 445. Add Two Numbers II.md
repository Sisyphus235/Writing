Question 445.

You are given two non-empty linked lists representing two non-negative integers. The most significant digit comes first and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Follow up:
What if you cannot modify the input lists? In other words, reversing the lists is not allowed.

Example:

```bash
Input: (7 -> 2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 8 -> 0 -> 7
```

# 题目分析

## tag

类别是 linked list。

## 题目

两个正序链表存储两个正整数，求和并用同样的方式返回。

## 思路
```python
# Definition for singly-linked list.
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None
```

相比于第 2 题，这道题目的难点在于正序链表，这样进位会出现在单向链表的前一位。
1. 第一个思路和之前一样，将链表转化为整数，加和后再转换为单向链表，这个思路几乎不受影响，因为进位的难点被整数加法解决。
2. 同样，资源主要消耗在单向链表和整数的相互转化上。如何解决进位问题是重点。整数的计算顺序是从个位向高位数字的，无法强行逆向处理，那就只能逆向处理单向链表。“逆向”二字直指 stack 的数据结构。
l1: (7 -> 2 -> 4 -> 3) 
l2: (5 -> 6 -> 4)
先处理两个单向链表，使其逆向，以方便进位计算。
遍历 l1，每个元素依次进入栈 s1, [3, 4, 2, 7]，
遍历 l2，每个元素依次进入栈 s2，[4, 6, 5]。
接下来的思路和第 2 题类似，只不过从第二题的单向链表取值变换成从栈取值。
第一次取值，3 + 4 = 7，返回的单向链表为 None -> 7；
第二次取值，4 + 6 = 10，进位 1，返回的单向链表为 None -> 0 -> 7；
第三次取值，2 + 5 = 7，加上进位 1，返回的单向链表为 None -> 8 -> 0 -> 7；
第四次取值，7，返回的单向链表为 None -> 7 -> 8 -> 0 -> 7

# python
```python
class ListNode:
    def __init__(self, x):
        self.val = x
        self.next = None


def add_two_numbers_II(l1: ListNode, l2: ListNode) -> ListNode:
    l1_stack = []
    while l1:
        l1_stack.append(l1.val)
        l1 = l1.next
    l2_stack = []
    while l2:
        l2_stack.append(l2.val)
        l2 = l2.next

    carry = 0
    head = None
    while l1_stack or l2_stack or carry:
        total, carry = carry, 0
        if l1_stack:
            total += l1_stack.pop()
        if l2_stack:
            total += l2_stack.pop()
        if total > 9:
            total -= 10
            carry = 1
        cur = ListNode(total)
        cur.next = head
        head = cur

    return head
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
    Stack<Integer> s1 = new Stack<Integer>();
    while (l1 != null) {
        s1.push(l1.val);
        l1 = l1.next;
    }
    Stack<Integer> s2 = new Stack<Integer>();
    while (l2 != null) {
        s2.push(l2.val);
        l2 = l2.next;
    }

    listNode head = null;
    int carry = 0;
    while (!s1.empty() || !s2.empty() || carry != 0) {
        int total = carry;
        carry = 0;
        if (!s1.empty()) {
            total += s1.pop();
        }
        if (!s2.empty()) {
            total += s2.pop();
        }
        if (total > 9) {
            total -= 10;
            carry = 1;
        }
        listNode cur = new listNode(total);
        cur.next = head;
        head = cur;
    }

    return head;
}
```
# 相关题目
[LeetCode 2. Add Two Numbers](https://www.jianshu.com/p/e133590dd94f)