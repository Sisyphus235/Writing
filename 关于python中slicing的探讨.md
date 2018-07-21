# 关于python中slicing的探讨


## 1.overview
Python Reference Manual(2016)对slicing的定义如下： 
```
a slicing selects a range of items in a sequence object (e.g. a string, tuple, or list).
slicing may be used as expressions or as targets in assignment or del statements.

The syntax for a slicing:
slicing      ::=  primary "[" slice_list "]"
slice_list   ::=  slice_item ("," slice_item)* [","]
slice_item   ::=  expression | proper_slice
proper_slice ::=  [lower_bound] ":" [upper_bound] [ ":" [stride] ]
lower_bound  ::=  expression
upper_bound  ::=  expression
stride       ::=  expression

There is ambiguity in the formal syntax here: anything that looks like an expression list also looks like a slice, so any subscription can be interpretated as a slicing. Rather than further complicating the syntax, this is disambiguated by difining that in this case the interpretation as a subscription takes priority over the interpretaton as a slicing (this is the case if the slice list contains no proper slice).

The semantics for a slicing are as follows. The primary must evaluate to a mapping object, and it is indexed (using the same __getitem__() method as normal subscription) with a key that is constructed from the slice list, as follows. 
If the slice list contains at least one comma, the key is a tuple containing the conversion of the slice items; otherwise, the conversion of the lone slice item is the key. The conversion of a slice item that is an expression is that expression.  
The conversion of a proper slice is a slice object (see section The standard type hierarchy) whose start,
stop and step attributes are the values of the
expressions given as lower bound, upper bound and stride, respectively, substituting None for missing expressions.
```

## 2.explanation
```
a slicing selects a range of items in a sequence object (e.g. a string, tuple, or list).
```  
> 切片的原材料是**序列对象**，在python中常见为string, tuple和list，换言之，自定义的类如果是序列对象也可以有切片功能  
```
slicing may be used as expressions or as targets in assignment or del statements.
```  
> 切片可以用作**表达式**，或者赋值和删除的作用对象  
```
The syntax for a slicing:
slicing      ::=  primary "[" slice_list "]"
slice_list   ::=  slice_item ("," slice_item)* [","]
slice_item   ::=  expression | proper_slice
proper_slice ::=  [lower_bound] ":" [upper_bound] [ ":" [stride] ]
lower_bound  ::=  expression
upper_bound  ::=  expression
stride       ::=  expression
```  
> * 主要语法是[]，中间包含一个slice_list；
* slice_list是由逗号分隔开的slice_item组成的；
* slice_item可以是表达式或者是proper_slice；
* proper_slice的结构是"[下限]:[上限]:[步长]"；
* 下限、上限和步长都是表达式组成的。  
```
There is ambiguity in the formal syntax here: anything that looks like an expression list also looks like a slice, so any subscription can be interpretated as a slicing. Rather than further complicating the syntax, this is disambiguated by difining that in this case the interpretation as a subscription takes priority over the interpretaton as a slicing (this is the case if the slice list contains no proper slice).
```  
> 表达式的列表和切片会看起来很像，因为都是中括号中间加入表达式。subscription的定义是“subscription ::=  primary "[" expression_list "]"”
为了避免语义复杂化，如果没有proper_slice，则下标的解析优先于切片。  
```
>>> s = 'hello'
>>> print(s[2])  # 2优先解析为下标index
l
>>> print(s[::-1])  # ::-1解析为切片slice
olleh
```   
```  
The semantics for a slicing are as follows. The primary must evaluate to a mapping object, and it is indexed (using the same __getitem__() method as normal subscription) with a key that is constructed from the slice list, as follows. 
If the slice list contains at least one comma, the key is a tuple containing the conversion of the slice items; otherwise, the conversion of the lone slice item is the key. The conversion of a slice item that is an expression is that expression.  
The conversion of a proper slice is a slice object (see section The standard type hierarchy) whose start,
stop and step attributes are the values of the
expressions given as lower bound, upper bound and stride, respectively, substituting None for missing expressions.
```  
> primary是mapping object的说法值得商榷，或许sequence object更合适。index使用的是和subscription一样的\__getitem__()方法。如果slice中含有逗号，则key解析为tuple。如果只有一个slice_item，则生成一个slice object，start, stop, step由表达式赋值，缺省为None。如果某个bound是负数，那么这个bound将会转化为(bound + sequence’s length)。对于Evaluation的结果，如果超出了[0: sequence's length]，那么会使用边界值替代超出值。

## 3.lab
自定义一个可以slice的对象，根据定义，只要是序列对象即可。  
```
class T1:
    def __getitem__(self, key):
        print(key)
        
>>> t = T1()
>>> t[:-1, ]  # slice_item有逗号，解析为一个slice tuple
(slice(None, None, -1),)
>>> t[1:2:3, 4:5:6]  # 每个slice_item赋值start, stop, step
(slice(1, 2, 3), slice(4, 5, 6))
```    
使用built-in的list来实现自定义类的slicing：  
```
class T2:
    def __init__(self, *args):
        self.l = [x for x in args]
    def __getitem__(self, key):
        print(key)
        return self.l[key]
```  

```
>>> t = T2(1, 2, 3)
>>> print(t[int(2*0.5)])  # slice_item可以是表达式
1
2
>>> print(t[:-1])  # 缺省的部分是None
slice(None, -1, None)
[1, 2]
>>> print(t[::-1])
slice(None, None, -1)
[3, 2, 1]
>>> print(t[:-1, ])
list indices must be integers or slices, not tuple
# sequence的slicing不支持逗号分隔的元组，仅支持单个slice的object。T1中的分隔方法在T2中不支持，因为T2的数据类型是list，属于sequence。
```  

## 4.example
```
>>> t = "I am a student."
>>> print(t[2])
a
>>> print(t[-1])  # 负数index相当于index + length，这里是-1+15=14
.
>>> print(t[2:4])  # 从start开始到stop结束，包括start，不包括stop
am
>>>print(t[:4], t[7:], sep='|')  # 缺省start则默认从头开始，缺省stop则默认到最后结束，缺省step默认正向步长为1
I am|student.
```

## 5.take away points
字符串倒序很常用：  
```
>>> s = 'hello world'
>>> print(s[::-1])
dlrow olleh
```