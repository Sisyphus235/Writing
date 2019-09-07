# 1. for() 循环
for() 循环写法 JS 和 Java 很像，用来遍历数组：
```js
const a = [1, 2, 3, 4];
for(let i = 0; i < a.length; i++){
    console.log(i, a[i]);
}

0 1
1 2
2 3
3 4
```
for 循环结构如下：
```js
for (语句 1; 语句 2; 语句 3) {
     要执行的代码块
}
```
语句 1 在循环（代码块）开始之前执行；
语句 2 定义运行循环（代码块）的条件；
语句 3 会在循环（代码块）每次被执行后执行。

如果数组长度在循环中不变，将数组长度用变量存储下来效率更高
```js
const a = [1, 2, 3, 4];
for(let i = 0, len = a.length; i < len; i++){
    console.log(i, a[i]);
}

0 1
1 2
2 3
3 4
```  

# 2. forEach() 循环
forEach() 循环用来遍历数组，遍历的范围在第一次调用 callback 前就会确定。
可以传入 item 元素, index 元素索引 和被遍历的 array 数组本身，this 指向 window，不能使用 continue/break，可以使用 return，return 不会结束循环，功能类似于 continue。
特点是：
* 没有返回值
* 不改变原数组  


```js
const a = [1, 2, 3, 4];
let total = 0;
a.forEach(function(item, index, array){
    if (item === 4) return;
    console.log(item, index, array, this);
    total += item;
});

1 0 [1, 2, 3, 4] Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
2 1 [1, 2, 3, 4] Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
3 2 [1, 2, 3, 4] Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
```  
forEach 循环结构如下：
```js
array.forEach(function(item, index, array){...})
```  
## 2.1 forEach ES5 中 map 循环
map 映射可以传入 item 元素, index 元素索引 和被遍历的 array 数组本身，this 指向 window，不能使用 continue/break，可以使用 return，return 不会结束循环，功能类似于 continue。  
map 的特点是：
* 返回一个新数组
* 不改变原数组的值  


```js
const a = [1, 2, 3, 4];
let total = 0;
a.map(function(item, index, array){
    if (item === 4) return;
    console.log(item, index, array, this);
    total += item;
});
1 0 [1, 2, 3, 4] Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
2 1 [1, 2, 3, 4] Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
3 2 [1, 2, 3, 4] Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
[undefined, undefined, undefined, undefined]

a.map(function(item, index, array){
    if (item === 4) return;
    return item++;
});
[1, 2, 3, undefined]
```  
map 映射结构如下：
```js  
array.map(function(item, index, array){...})
```  
## 2.2 forEach ES5 中 every 循环
循环在第一次 return false 后返回。


## 2.3 forEach ES5 中 some 循环
循环在第一次 return true 后返回。

## 2.4 forEach ES5 中 filter 循环
返回一个新的数组，该数组内的元素满足回调函数。

## 2.5 forEach ES5 中 reduce 循环
对数组中的元素依次处理，将上次处理结果作为下次处理的输入，最后得到最终结果。


# 3. for...in() 循环
for-in() 循环遍历的是对象的属性，不是数组的索引，所以 for-in() 遍历不局限于数组，还可以遍历对象。

```js
const a = [1, 2, 3, 4];
for (let ndex in a){
    console.log("a[" + index + "] = " + a[index]);
}

a[0] = 1
a[1] = 2
a[2] = 3
a[3] = 4

const dict = {a:1, b:2, c:3, d:4}
for (let obj in dict) {
    console.log("key[" + obj + "] = " + dict[obj]);
}

key[a] = 1
key[b] = 2
key[c] = 3
key[d] = 4
```
特别需要注意的是，for-in() 循环没有固定的便利顺序。for-in() 循环会遍历实例或者原型的属性，一般速度为其他循环的 1/7，很少使用。有一个例外，当数组赋值稀疏的时候效果很好，例如
```js
const a = [];
a[0] = 1;
a[100] = 2;
a[10000] = 3;
for (let key in a ) {
    if(a.hasOwnProperty(key)  &&    
        /^0$|^[1-9]\d*$/.test(key) &&    
        key <= 4294967294               
        ) {
        console.log(a[key]);
    }
}

1 
2
3
```  

# for...of() 循环
ES6 引入的循环方法，解决之前循环的问题：
1. forEach 不能 break 和 return；
2. for-in 会遍历对象自定义属性和原型链属性，且遍历顺序随机。

特点：
1. 正确响应 break，continue，return；
2. 不仅支持数组，同时支持大多数类数组对象，例如 DOM nodelist 对象；
3. 支持字符串遍历，将字符串视为一系列 Unicode 字符进行遍历；
4. 支持 map 和 set 对象遍历；
5. 不支持普通对象遍历，普通对象属性遍历应该使用 for-in。

```js
const a = [1, 2, 3, 4];

for (let e of a) {
    if (e === 1) {
        console.log("continue");
        continue;
    }
    console.log(e);
    if (e === 3) {
        console.log("break");
        break;
    }
}

continue 
2 
3 
break

const s = new Set(['s', 'e', 't']);

for (let e of s) {
    console.log(e);
}

s
e
t

const m = new Map([['a', 1], ['b', 2], ['c', 3]]);

for (let key of m.keys()){
    console.log(key);
}

a
b
c

for (let val of m.values()){
    console.log(val);
}

1
2
3

for (let [key, val] of m.entries()) {
    console.log(key, val);
}

a 1
b 2
c 3
```

# Iterator