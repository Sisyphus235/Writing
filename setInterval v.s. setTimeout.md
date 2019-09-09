# setTimeout
## 基本语法
```js
let timeoutID = window.setTimeout(func[, delay, param1, param2, ...]);
let timeoutID = window.setTimeout(code[, delay]); 
let timeoutID = window.setTimeout(function, milliseconds);
```
timeoutID 是该延时操作的数字 ID , 此 ID 随后可以用来作为 window.clearTimeout 方法的参数。
func 是你想要在 delay 毫秒之后执行的函数。
code 是指你想要在 delay 毫秒之后执行的代码字符串 (使用该语法是不推荐的，不推荐的原因和 eval() 一样)
delay 是延迟的毫秒数 (一秒等于1000毫秒)，函数的调用会在该延迟之后发生。如果省略该参数，delay 取默认值 0 。实际的延迟时间可能会比 delay 值长。
## 基本使用
setTimeout() 是绑定在浏览器 window 的一个方法，用来指定一段程序在一段时间后执行，并回调该定时器编号。
一般通过 clearTimeout 来取消执行。
```js
setTimeout(function(){console.log('test123');},3000);

// 输出是
6  // 这是定时器编号
test123  // 这是被执行的程序输出
```
## 使用原理
原理是将执行程序加载到任务队列，当轮到该任务执行时，检查时间是否到达，到达则执行。实际执行时间不是设定的时间
```js
let startTime=new Date();
setTimeout(function(){console.log(new Date()-startTime);},100);

// 输出是
7  // 定时器编号
105  // 实际执行间隔时间
```

# setInterval
## 基本语法
```js
let intervalID = window.setInterval(func, delay[, param1, param2, ...]);
let intervalID = window.setInterval(code, delay);
```
intervalID 是此重复操作的唯一辨识符，可以作为参数传给 clearInterval()
func 是你想要重复调用的函数。
code 是另一种语法的应用，是指你想要重复执行的一段字符串构成的代码(使用该语法是不推荐的，不推荐的原因和 eval() 一样)。
delay 是每次延迟的毫秒数 (一秒等于1000毫秒)，函数的每次调用会在该延迟之后发生。和 setTimeout 一样，实际的延迟时间可能会稍长一点。

## 基本使用
setInterval() 是绑定在浏览器 window 的一个方法，用来指定一段程序每隔一段时间执行，并回调该定时器编号。
一般通过 clearInterval 来取消执行。
```js
let startTime=new Date();
setInterval(function(){console.log(new Date()-startTime);},100);
for(var i=0; i<1000000000; i++){}

// 输出是
undefined  // 定时器编号未定义
2717  // 因为 for 循环的存在出现较大延时
2801
2904
3009
3101
```
## 使用原理
setInterval 开始时会设置执行时间点，一旦超过时间当任务触发时会即刻执行。
setInterval 指定的是“开始执行”之间的间隔，并不考虑每次任务执行本身所消耗的事件。因此实际上，两次执行之间的间隔会小于指定的时间。比如，setInterval 指定每 100ms 执行一次，每次执行需要 5ms，那么第一次执行结束后 95 毫秒，第二次执行就会开始。如果某次执行耗时特别长，比如需要 105 毫秒，那么它结束后，下一次执行就会立即开始。
为了确保两次执行之间有固定的间隔，可以不用setInterval，而是每次执行结束后，使用setTimeout指定下一次执行的具体时间。
```js
let i = 1;
let timer = setTimeout(function() {
  alert(i++);
  timer = setTimeout(arguments.callee, 2000);
}, 2000);

// 抽象成函数
function interval(func, wait){
  var interv = function(){
    func.call(null);
    setTimeout(interv, wait);
  };
  setTimeout(interv, wait);
}
interval(function(){
  console.log(2);
},1000);
```