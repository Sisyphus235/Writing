本文基于一篇英文文章，原文链接如下：
[Inside python dict — an explorable explanation](https://just-taking-a-ride.com/inside_python_dict/chapter1.html)

# 0.dict 浅析
dict 由 key, value pair 组成，python 中是通过 hash 实现的，value 和 key 是一对一的关系，没有效率瓶颈，往往 key list 的查询是更需要关注的效率瓶颈。

# 1.list 搜索效率
python 用 list 存储 dict 中的 keys，list 是通过连续内存空间来存储的。dict 中经常需要判断 key 是否存在，如果顺序搜索 list 效率很低，极端情况下要检索所有的 key 才能知道是否存在，算法复杂度是 O(n)。这会导致 dict 的效率会随着 key 的增多线性下降，所以 python 设计了 hash 的方法来解决 list 搜索问题。
```python
def simple_search(simple_list, key):
    idx = 0                            
    while idx < len(simple_list):      
        if simple_list[idx] == key:    
            return True                
        idx += 1                    
    return False     
```

# 2.hash 管理 key list
hash 有很多种方式，这里用余数的方式最简单的展示相关过程，即用每个整数值除以 key 的数量取余数，把余数当成 hash value。
```python
def build_not_quite_what_we_want(original_list):
    new_list = [None] * len(original_list)         # 生成和 key list相同长度的 list 用以存放 key
                                                
    for number in original_list:                
        idx = number % len(new_list)    # 获得余数        
        new_list[idx] = number                  
    return new_list        
```
这时会出现 hash collision，因为余数是会重复的，重复的话最后一个 key 会在新的 list 中覆盖之前的 key，这就要求一种处理哈希碰撞的方法。

## 2.1 linear probing
当出现哈希碰撞时，最简单的处理方式是查找后续可用的 index。新的 hash table 越大碰撞可能性越小，但占用的空间也会越大。平衡这两个参数，一般 hash table 的长度是 key list 长度的两倍。
```python
def build_insert_all(original_list):            
    new_list = [None] * (2 * len(original_list))   # 生成 2倍
                                                
    for number in original_list:                
        idx = number % len(new_list)            
        while new_list[idx] is not None:        
            idx = (idx + 1) % len(new_list)     
        new_list[idx] = number                  
    return new_list  
```

## 2.2 separate chaining
另一种哈希碰撞的解决方案是在碰撞的 index 的位置存储一个链表，将相同哈希值的内容用链表记录下来。这也是 redis 实现 hash 类型的一种方法。