Question 3.

Given a string, find the length of the longest substring without repeating characters.

Example 1:

```bash
Input: "abcabcbb"
Output: 3 
Explanation: The answer is "abc", with the length of 3. 
```

Example 2:

```bash
Input: "bbbbb"
Output: 1
Explanation: The answer is "b", with the length of 1.
```

Example 3:

```bash
Input: "pwwkew"
Output: 3
Explanation: The answer is "wke", with the length of 3. 
             Note that the answer must be a substring, "pwke" is a subsequence and not a substring.
```

# 题目分析

## tag

类别是 string。

## 题目

找到输入字符串中最长的连续无重复字符的子字符串。

## 思路

这是一道需要照顾全局和局部的题，用变量记录全局的最大长度，同时记录局部符合条件的子字符串情况。
1. 遍历一遍输入字符串，当前永远记录不重复的子字符串，如果出现重复则从重复字母下一个位置记录子字符串信息。
以 "abcabcbb" 为例
只要输入的字符串不为空，则全局最大长度至少等于 1，子字符串为 ""，起始位置是 0；
从 a 开始遍历，子字符串无重复，子字符串增加为 "a"，起始位置不变；
接着是 b，子字符串无重复，子字符串增加为 "ab"，起始位置不变；
接着是 c，子字符串无重复，子字符串增加为 "abc"，起始位置不变；
接着是 a，子字符串重复，当前长度是 3，大于全局最大长度 1，修改全局最大长度为 3，重复字母 a 的位置是 0，修改起始位置为 1，子字符串修改为 "bca"；
接着是 b，子字符串重复，当前长度是 3，等于全局最大长度 3，全局最大长度不变，重复字母 b 的位置是 1，修改起始位置为 2，子字符串修改为 "cab"；
接着是 c，子字符串重复，当前长度是 3，等于全局最大长度 3，全局最大长度不变，重复字母 c 的位置是 2，修改起始位置为 3，子字符串修改为 "abc"；
接着是 b，子字符串重复，当前长度是 3，等于全局最大长度 3，全局最大长度不变，重复字母 b 的位置是 4，修改起始位置为 5，子字符串修改为 "cb"；
接着是 b，子字符串重复，当前长度是 2，小于全局最大长度 3，全局最大长度不变，重复字母 b 的位置是 6，修改起始位置为 7，子字符串修改为 "b"；
遍历结束，全局最大长度是 3。

```python
def longest_substring(s: str) -> int:
    # 处理输入为空和一个字符的情况
    if not s:
        return 0
    if len(s) == 1:
        return 1

    # 初始化全局 long 和子字符串起始位置 start 以及当前内容 cur
    long = 1
    start, cur = 0, s[0]
    for i in range(1, len(s)):
        # 无重复字符则只修改当前子字符串内容
        if s[i] not in cur:
            cur += s[i]
        else:
            # 重复字符则先计算当前子字符串长度，如果大于全局长度则修改
            length = len(cur)
            long = length if length > long else long
            # 计算新的子字符串起始位置和当前内容
            index = cur.index(s[i])
            start += index + 1
            cur = cur[index + 1:] + s[i]
    long = len(cur) if len(cur) > long else long

    return long
```

2. 上述思路的主要消耗在于不断拼接字符串，实际上需要判断的是新的字符是不是重复字符，如果是重复字符，其重复位置在哪里。这就启发我们跳出“子字符串”的概念，而是关注背后底层的重复问题，查询字符是否存在最快的方式之一就是哈希，用哈希表记录出现过的字符，以记录是否重复，以及重复的位置

# python
```python
def longest_substring_hash(s: str) -> int:
    # 记录出现过的字符和其最后出现的位置
    alpha = {}
    # 记录全局子字符串最大长度
    count = 0
    # 子字符串的起始位置
    first = -1
    for i in range(len(s)):
        # 如果字符重复，且上一个位置是属于当前子字符串，则更新子字符串初始位置
        if s[i] in alpha and alpha[s[i]] > first:
            first = alpha[s[i]]
        # 记录字符的最后出现位置
        alpha[s[i]] = i
        # 判断当前子字符串长度是否大于全局最大长度
        count = max(count, (i - first))
    return count
```

# java

```java
public int longestSubsting(String s) {
    HashMap<Character, Integer> alpha = new HashMap<Character, Integer>();
    int count = 0;
    int first = -1;
    for (int i = 0; i < s.length(); i++) {
        if (alpha.containsKey(s.charAt(i)) && alpha.get(s.charAt(i)) > first) {
            first = alpha.get(s.charAt(i));
        }
        alpha.put(s.charAt(i), i);
        count = Math.max(count, i - first);
    }
    return count;
}
```