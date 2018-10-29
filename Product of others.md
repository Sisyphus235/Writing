## Issues
Given an array of integers, return a new array such that each element at index i of the new array is the product of all the numbers in the original array except the one at i.

For example, if our input was [1, 2, 3, 4, 5], the expected output would be [120, 60, 40, 30, 24]. If our input was [3, 2, 1], the expected output would be [2, 3, 6].

Follow-up: what if you can't use division?

## With division
如果可以使用除法，则先获取整个数组乘积比较好，后续计算每个index的对应值，只需要用乘积除以原数组index的值即可。
```python
def product_array1(array):
    # 获得array的乘积
    product = 1
    for i in array:
        product *= i
    # 返回数组是乘积除以当前的值
    new = []
    for i in array:
        new.append(int(product/i))
    return new
```
这样用两次for循环非嵌套结构完成了题目要求，算法时间复杂度是2n，即O(n)。

## Without division
如果不可以使用除法，第一种思路就是遍历数组一遍，将每个数字乘给新数组除了自身index外的位置。
```python
def product_array2(array):
    length = len(array)
    new = [1] * length
    # 遍历整个数组
    for i, num in enumerate(array):
        # 将当前值乘给所有非当前index的位置的数字
        for j in range(length):
            if j == i:
                continue
            new[j] *= num
    return new
```
这样用两次for循环嵌套结构完成了题目要求，算法时间复杂度高，是O(n^2)。

## Without division advanced
接着思考如何提升算法效率，先想算法的上限，如果要求所有其他元素的乘积，至少要遍历一遍数组，也就是说O(n)是不可超过的算法复杂度，至于能否达到需要继续思考。
然后，想到使用除法的时候能达到O(n)的复杂度级别，和上述不用除法的区别在于没有使用for的嵌套。
这样问题就转变到如何使用多个单一for循环来实现非除法的一个算法。
如果一次for循环不能嵌套着给所有应该有这样一个因子的位置的数字，那至少可以记录到这个点位置的当前乘积。
如果用一次正向for循环获得前序乘积，再用一次反向for循环获得逆序乘积，则可以在第三次遍历的时候同时拿到一个位置前后的乘积，这样也就解决了问题。
例如数组[1,2,3]，第一次前序遍历求乘积得到[1,2,6]，第二次逆序遍历求乘积得到[6,6,3]，这样就能求得结果是[2,3,6]。
同时发现，不需要三次平行的for循环，最后求结果的循环可以和前面任意一次循环合并。
```python
def product_array3(array):
    length = len(array)
    post_product = [1] * length
    # 后序遍历获得所有后续乘积
    for i in range(length)[::-1]:
        post_product[i] *= array[i]
        if i != length - 1:
            post_product[i] *= post_product[i+1]
    # 前序遍历获得前序乘积，与后续乘积相乘得到当前值
    new = [1] * length
    pre_product = 1
    for i in range(length):
        new[i] *= pre_product
        if i != length - 1:
            new[i] *= post_product[i+1]
        pre_product *= array[i]
    return new
```