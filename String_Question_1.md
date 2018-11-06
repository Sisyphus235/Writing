# String Question 1

## Given two strings, write a method to decide if one is a permutation of the other

```python
def is_permutation(str1, str2):
	# 长度不同则一定不是相同字符的排列
    if len(str1) != len(str2):
        return False
    for s in str1:
    	# 如果字母不在另一个string中，则不是相同字符排列
        if s not in str2:
            return False
        i = str2.index(s)
        # 删除字符串中相应字符
        str2 = str2[:i] + str2[i+1:]
    if not str2:
        return True

```