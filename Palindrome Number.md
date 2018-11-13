## Palindrome Number  

Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.  

Example 1:  
```
Input: 121
Output: true
```  

Example 2:  
```
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```  

Example 3:  
```
Input: 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```  

Follow up:

Coud you solve it without converting the integer to a string?  

## Solution  
如果可以用string解题，头尾两个指针同时向中间走，如果到达中点前出现任何一个位置上string不匹配则为False。
如果无法使用string，则需要生成一个reversed number，比对两个number是否相等来判断。

```python
def isPalindrome(x):
    if x < 0:
        return False
    rev, order = 0, x
    while x:
        end = x % 10
        x = x // 10
        rev = rev * 10 + end
    if order == rev:
        return True
    return False

```

