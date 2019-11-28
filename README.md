# 每日面试题集锦

每日的面试题目的分享意在提高大家对于 前端知识点的梳理和把握。

### CSS专题

[css面试题](https://github.com/MagicalBridge/orange_financial_interview/blob/master/css%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98%E7%9B%AE.md)


### JavaScript 专题

#### 0516 考察的知识点：作用域、js 异步、事件循环。

观察下面代码看看输出什么内容：

```js
for (var i = 1; i <= 3; i++) {
  setTimeout(function() {
    console.log("ttt" + i);
  }, 0);
}
```

解答：3 个 ttt4
这道题目有一个坑需要避免，因为i 是从1开始的，所以循环的方式是这样的：
  i = 1   -> i 是否小于等于3  -> i++ ->  i = 2;
  i = 2   -> i 是否小于等于3  -> i++ ->  i = 3;
  i = 3   -> i 是否小于等于3  -> i++ ->  i = 4;
  i = 4   -> i 是否小于等于3  否 退出循环,退出循环的时候 i 已经变成了 4
注：var 来声明变量 `i`，声明会提升到作用域的顶部，js单线程，`setTimeout`是异步匿名函数，for 循环结束才会执行 setTimeout 的异步回调，此时 `i` 等于 4

追问：如何实现让这个函数打印 ttt1 ttt2 ttt3 

```js
// 使用自执行函数 每次循环的时候将当前循环的变量作为参数传入自执行函数
// 和上面的代码不同 虽然 i++ 时候变成了 4 但是 i = 4 时候并不会执行循环
for (var i = 1; i <= 3; i++) {
  (function(i){
    setTimeout(function() {
      console.log("ttt" + i);
    }, 0);
  })(i)
}

// 例如块级作用域
for (let i = 1; i <= 3; i++) {
  setTimeout(function() {
    console.log("ttt" + i);
  }, 0);
}
```

#### 0517 考察的知识点： 闭包。

实践角度解释闭包：

> 1、即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）。
> 2、在代码中引用了自由变量。

闭包的优缺点：使用闭包主要是为了设计私有的方法和变量。闭包的优点是可以避免全局变量的污染，缺点是闭包会常驻内存，会增大内存使用量，使用不当很容易造成内存泄露。

```js
var count = 10;
function add() {
  var count = 1;
  return function () {
    count += 1;
    console.log(count);
  }
}
var s = add();
s();
```
解答：2
注： return 函数可以引用外部函数（父函数）add 的变量 count, 因此内层函数 count = 1,最终输出为 2；

```js
var count = 10;
function add() {
  var count = 1;
  return function () {
    count += 1;
    console.log(count);
  }
}
var s = add();
s();
s();
```

解答：2,3
注解：这里函数调用两次，第一次返回 2，闭包可以保存变量值，因此第二次调用时外层函数 count=2，最终输出结果是 3；

```js
var count = 10;
function add() {
  return function () {
    count += 1;
    console.log(count);
  }
}
var s = add();
s();
```

解答：11
注解： 闭包函数内部可以引用外部的参数和变量，此例 return 函数没有定义 count 依次往上找直至 Windows（作用域链），找到就返回，找不到就返回 undefined,这里找到 count=10 最后打印的结构就是 11;

#### 0520 考察的知识点：this 指向问题

```js
var a = "1";
var obj = {
  a: "2",
  say: function() {
    console.log(this.a);
    setTimeout(function() {
      console.log(this.a);
    });
  }
};
obj.say();
```

解答：2，1
>注：非箭头函数中 `this` 最终指向调用它的对象，第一个 this 指向 obj，输出 2；setTimeout 是全局函数，所以第二个 this 指向 window，输出 1，箭头函数没有自己的 this, 它的 this 是继承而来; 默认指向在定义它时所处的对象。

因此，我们修改下代码：
```js
var a = "1";
var obj = {
  a: "2",
  say: function() {
    console.log(this.a);
    setTimeout(() => {
      console.log(this.a);
    });
  }
};
obj.say();
```
解答：2，2 就像上文解释的那样 虽然window 上面挂载了1 但是 setTimeout 这个函数是定义在 obj 这个函数中的
因此和这个函数拥有相同的this指向。

#### 0521 考察的知识点：this 指向问题、赋值语句

```js
var num = 10;
var obj = {
  num: 8,
  inner: {
    num: 6,
    print: function() {
      console.log(this.num);
    }
  }
};
num = 888;
obj.inner.print();
var fn = obj.inner.print;
fn();
obj.inner.print();
(obj.inner.print = obj.inner.print)();
```

解答：`6 888 6 888` 
+ 在执行 `obj.inner.print()` 这个函数的时候 调用对象是 obj.inner 按this 指向的关系，应该是inner，在inner内部 num的值是6因此`obj.inner.print()`打印出来的是 6；
+ `var fn = obj.inner.print` 执行这个语句，将print的函数重新赋值给了 fn 接着在下面执行 `fn()` 是在全局作用域环境执行的，这个是this指向window，在widow上面num 已经被重新赋值成了 888
+ `obj.inner.print()` 再次执行这个函数时候 第一次执行的效果是一样的。
+ 解答：https://zhidao.baidu.com/question/924556511528417059.html 
+ `(obj.inner.print = obj.inner.print)()` 用一个匿名函数包裹, 在全局环境下执行 输出 888 

#### 5. 0522 考察知识点：数组的操作 hashMap 的映射 （leetcode 第一题 非常经典）

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:

```
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

解答：

解法一：使用暴力破解法:

> 暴力破解的思路是使用双层 for 循环,外层循环控制第一个数字,内层循环控制第二个数字,当第一个数字和第二个数字之和满足目标数字的时候输出下标。

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
  for (let i = 0; i < nums.length; i++) {
    for (let j = i + 1; j < nums.length; j++) {
      if (nums[i] + nums[j] === target) {
        return [i, j]
      }
    }
  }
}
```

解法二：使用 HashMap 解法:

> 第一种解法的时间复杂度是很高的，一般来说,我们为了减小时间复杂度，需要使用空间来换，我们想要使用线性的时间复杂度来解决问题，那么就是说只能遍历一个数字，那另一个数字怎么办呢？这个时候我们可以想到使用一个 HashMap，来建立数字和其坐标位置之间的映射，我们知道 HashMap 是常数级别的查找，这样我们在遍历数组的时候，用 target 减去遍历到的数字，就是另外一个数字了，直接在 HashMap 中查找其是否存在即可。题目中的要求是查找的数字不是第一个数字，比如 target 是 4 遍历到了一个 2，那么另一个 2 不能是之前的 2，整个实现步骤为：

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */

var twoSum = function(nums, target) {
  // 创建map对象
  let map = new Map();
  // 创建空数组用于输出索引
  let arr = [];

  // 第一个for循环作用是对数组中的每一个数字做一个映射
  for (let i = 0; i < nums.length; i++) {
    map.set(nums[i], i);
  }
  // 第二个for循环 找到目标值，看看在 map 中是否存在
  // 存在就输出两个数字对应的索引
  for (let j = 0; j < nums.length; j++) {
    let t = target - nums[j];

    if (map.has(t) && map.get(t) != j) {
      arr.push(j);
      arr.push(map.get(t));
      break;
    }
  }
  return arr;
}
```

解法三：也可以只使用一遍map，在对数组映射的同时反过来检查下里面是否含相应的值
```js
var twoSum = function(nums, target) {
  // 创建map对象
  let map = new Map();
  // 创建空数组用于输出索引
  let arr = [];
  for (let j = 0; j < nums.length; j++) {
    let t = target - nums[j];
    if (map.has(t)) {
      arr.push(j);
      arr.push(map.get(t))
       return arr
    }
    map.set(nums[j],j)
  }
}
```

#### 0524 考察的知识点：es6 Set 和 Map。

ES6 中提供了 Set 数据容器，这是一个能够存储无重复值的有序列表。

Set 和 Array 互转

数组转 Set

```js
let set2 = new Set([4, 5, 6]);
console.log("array to set 1:", set2);
// array to set 2: Set { 4, 5, 6 }
```

Set 转数组 扩展运算符

```js
let set4 = new Set([4, 5, 6]);
console.log("set to array 1:", [...set4]);
// set to array 1: [ 4, 5, 6 ]
```

Map 和 Array 互转

Map 转为数组(还是)

```js
const myMap = new Map().set(true, 7).set({ foo: 3 }, ["abc"]);
[...myMap];
// [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
```

数组 转为 Map

```js
new Map([[true, 7], [{ foo: 3 }, ["abc"]]]);
```

Map 转为对象

```js
function strMapToObj(strMap) {
let obj = Object.create(null);
for (let [k,v] of strMap) {
obj[k] = v;
}
return obj;
}

const myMap = new Map()
.set('yes', true)
.set('no', false);
strMapToObj(myMap)

<!--{yes: true, no: false}-->
```

对象转为 Map

```js
function objToStrMap(obj) {
  let strMap = new Map();
  for (let k of Object.keys(obj)) {
    strMap.set(k, obj[k]);
  }
  return strMap;
}

objToStrMap({yes: true, no: false})

// {"yes" => true, "no" => false}
```

面试题

```js
let map = new Map();
map.set(["a"], 555);
map.get(["a"]);

let map2 = new Map();
let a = ["a"];
let b = ["a"];
map2.set(a, 555);
map2.get(a);
```

注意，只有对同一个对象的引用，Map 结构才将其视为同一个键。

```js
let arr1 = [1, 2, 3, 4, 5];
let arr2 = [4, 5, 6, 7, 8];
// 求这两个数组的并集和交集和差集;
let a = new Set(arr1);
let b = new Set(arr2);
```

(1)数组去重&并集

```js
new Set([...arr1, ...arr2]); //{1,2,3,4,5,6,7,8}
let arr3 = [...new Set([...arr1, ...arr2])]; //[1,2,3,4,5,6,7,8]
```

(2)交集

```js
let arr3 = new Set(arr1.filter(x => b.has(x))); //{4,5}
```

(3)差集

```js
let arr3 = new Set(arr1.filter(x=>!b.has(x))) //{1,2,3}
let arr4 = new Set(arr2.filter(x=>!a.has(x))) //{6,7,8}
[...arr3,...arr4] //[1,2,3,6,7,8]
```

#### 7、0529 考察的知识点：js 深拷贝与浅拷贝

浅拷贝的定义：
当我们创建一个新的对象 这个对象有着原始对象属性的一份精确拷贝，如果属性是基本类型，拷贝的就是基本类型的值如果属性是引用类型，拷贝的就是内存地址，所以如果其中一个对象改变了这个地址，就会影响到另外一个对象。

深拷贝的定义：
将一个对象从内存中完整的拷贝出一份出来，从堆内存中开辟一个新的区域存放新的对象，且修改新对象不会影响原对象。

(1)引例

```js
let a = {
  age: 1
};
let b = a;
a.age = 2;
console.log(b.age);
```

解答：如果给一个变量赋值一个对象，那么两者的值会是同一个引用，其中一方改变，另一方也会相应的改变，因此对于引用类型的复制，简单的赋值无用，这里打印出的 b.age 为 2；

(2)浅拷贝

```js
// Object.assign() 方法用于将所有可枚举属性的值从
// **一个或多个源对象**复制到目标对象。它将返回目标对象。
let a = {
  age: 1
};
let b = Object.assign({}, a);
a.age = 2;
console.log(b.age); // 1
```

解答：1；

```js
// ES6中的解构赋值也是浅拷贝
let a = {
  age: 1 // value是number 类型
};
let b = { ...a };
a.age = 2;
console.log(b.age);
```

解答：1；

```js
let a = {
  age: 1,
  jobs: {
    // value 是引用类型 object
    first: "test"
  }
};
let b = Object.assign({}, a);
a.jobs.first = "ttttt";
console.log(b.jobs.first); // ttttt
```

解答：'ttttt' 浅拷贝只能解决第一层的问题，如果接下去的值中还有对象的话，那么两者享有公同的引用。
这种情况是我们不想看到的。

(2)深拷贝

```js
let a = {
  age: 1,
  jobs: {
    first: "FE"
  }
};
let b = JSON.parse(JSON.stringify(a));
a.jobs.first = "aaaa";
console.log(b.jobs.first);
```

解答：‘FE’

```js
let obj = {
  a: 1
};
obj.b = obj;
let c = JSON.parse(JSON.stringify(obj));
console.log(c);
```

解答： 如果是循环引用的对象，当我们拷贝 obj 对象时，就会循环的遍历 b 属性，直到栈溢出；

```js
let a = {
  age: undefined,
  jobs: function() {},
  name: "yck"
};
let b = JSON.parse(JSON.stringify(a));
console.log(b);
```

解答：{name: 'yck'}在遇到函数或者 undefined 的时候，该对象也不能正常的序列化。

```js
function clone(obj) {
  var buf;
  if (obj instanceof Array) {
    buf = []; // 创建一个空数组
    var i = obj.length;
    while (i--) {
      buf[i] = clone(obj[i]);
    }
    return buf;
  } else if (obj instanceof Object) {
    buf = {};
    for (var k in obj) {
      buf[k] = clone(obj[k]);
    }
    return buf;
  } else {
    return obj;
  }
}
var obj = {
  a: 1,
  b: undefined,
  jobs: function() {}
};
var copy = clone(obj);
console.log(copy);
```

#### 0601 考察的知识点：var 声明变量的提升

```js
function f(shouldInitial) {
  if (shouldInitial) {
    var x = 10;
  }
  return x;
}

f(true); // 10
f(false); // undefined
```

解答：上述函数中 使用 var 声明的变量有一个变量提升的效果。虽然 是在 if 条件语句中
对 x 进行的声明，但是这种声明会提升。相当于这种写法：

```js
function f(shouldInitial) {
  var x;
  if (shouldInitial) {
    x = 10;
  }
  return x;
}

f(true); // 10
f(false); // undefined
```

作为对比我们看这样的一个例子

```js
function f(input：boolean):number{
  let a = 100;

  if(input){
    let b = a+1;
    return b;
  }
  return b
}
// 上述函数 使用ts 编写 肯定会爆出 语法错误 因为使用let 声明的变量只在块级作用域
// 中能够访问到。
```

#### 0602 请写一个阶乘函数 考察知识点：递归

```js
function factorial(n) {
  if (n === 1) {
    return n;
  }
  return n * factorial(n - 1);
}
```

```js
factorial(5) = 5*factorial(5-1) -->
               5*4*factorial(4-1) -->
               5*4*3*factorial(3-1) -->
               5*4*3*2*factorial(2-1) -->
               5*4*3*2*1 = 120
```

当 n=1 时候这个时候直接返回了 1，此时完成了递归计算。

递归中重要的一点就是递归语句和结束条件，如果没有结束条件那么整个递归会无限制的循环下去

同样的递归应用还在很多的方面比如 斐波那切数列的实现上面：
什么是斐波那切数列: 又称黄金分割数列 在数学上有这样的定义：
指的是这样一个数列：1、1、2、3、5、8、13、21、34、……在数学上，斐波纳契数列以如下被以递推的方法定义：F(1)=1，F(2)=1, F(n)=F(n-1)+F(n-2)（n>=3，n∈N\*）

用递归实现: 注意的是 n>=3 这个斐波那切数列的条件

```js
function fibonacci(n) {
  return n < 2 ? n : fibonacci(n - 1) + fibonacci(n - 2);
}

console.log(fibonacci(5)); // 1 1 2 3 5
```

#### 0605 如何实现一个 promise，promise 的原理

解答：promise 是对异步编程的一种抽象。它是一个代理对象，可以将异步对象和回调函数脱离开来，通过 then 方法在这个异步操作上面绑定回调函数 1.状态 promise 有 3 种状态：pending（待解决，这也是初始化状态），fulfilled（完成），rejected（拒绝）。 2.接口：promise 唯一接口 then 方法，它需要 2 个参数，分别是 resolveHandler 和 rejectedHandler。并且返回一个 promise 对象来支持链式调用
实现原理：

```js
const PENDING = "pending";
const FULFILLED = "fulfilled";
const REJECTED = "rejected";
class Promise {
  constructor(executor) {
    this.status = PENDING;
    this.value = "";
    this.onfulfilledArr = []; //成功回调集合
    this.onrejectedArr = []; //失败回调集合
    this.resolve = this.resolve.bind(this);
    this.reject = this.reject.bind(this);
    this.then = this.then.bind(this);
    executor(this.resolve, this.reject);
  }
  resolve(value) {
    if (this.status === PENDING) {
      this.value = value;
      this.onfulfilledArr.forEach(item => {
        item(this.value);
      });
      this.status = FULFILLED;
    }
  }
  reject(value) {
    if (this.status == PENDING) {
      this.value = value;
      this.onrejectedArr.forEach(item => {
        item(this.value);
      });
      this.status = REJECTED;
    }
  }
  then(onfulfilled, onrejected) {
    if (this.status == FULFILLED) {
      const res = onfulfilled(this.value);
      return new Promise(function(resolve, reject) {
        resolve(res);
      });
    }
    if (this.status == FULFILLED) {
      const res = onrejected(this.value);
      return new Promise(function(resolve, reject) {
        reject(res);
      });
    }
    if (this.status === PENDING) {
      const self = this;
      return new Promise(function(resolve, reject) {
        self.onfulfilledArr.push(() => {
          const res = onfulfilled(self.value);
          resolve(res);
        });
        self.onrejectedArr.push(() => {
          const res = onrejected(self.value);
          reject(res);
        });
      });
    }
  }
}
const test = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(100);
  }, 2000);
});
test
  .then(
    data => {
      console.log(data); //100
      return data + 5;
    },
    data => {}
  )
  .then(
    data => {
      console.log(data); //105
    },
    data => {}
  );
```

#### 0611 统计一个字符串出现最多的字母

给出一段英文连续的英文字符窜，找出重复出现次数最多的字母
比如： 输入：afjghdfraaaasdenas 输出 ： a

```js
function findMaxDuplicateChar(str) {
  // 长度是1 返回本身
  if (str.length == 1) {
    return str;
  }
  let charObj = {};
  // 遍历字符串 做一个对象的映射。
  for (let i = 0; i < str.length; i++) {
    // string.charAt(i) 找出对象中的每一个字符
    if (!charObj[str.charAt(i)]) {
      charObj[str.charAt(i)] = 1;
    } else {
      charObj[str.charAt(i)] += 1;
    }
  }

  let maxChar = "",
    maxValue = 1;
  for (var k in charObj) {
    if (charObj[k] >= maxValue) {
      maxChar = k;
      maxValue = charObj[k];
    }
  }
  return maxChar;
}
```

promise 和 setTimeout 执行顺序的问题

```js
setTimeout(function() {
  console.log(1);
}, 0);
new Promise(function(resolve) {
  console.log(2);
  for (var i = 0; i < 1000000000; i++) {
    i == 99999999 && resolve();
  }
  console.log(3);
}).then(function() {
  console.log(4);
});
console.log(5);
```

输出：2 3 5 4 1
解答：then 和 settimeout 执行顺序，即 setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.then()在本轮“事件循环”结束时执行。因此 then 函数先输出，settimeout 后输出。
for 循环是一个同步过程。上述在代码 在控制台中打印 有卡顿就是在执行这个for循环。
参考链接：https://www.jianshu.com/p/4516ad4b3048

```js
async function a() {
  await console.log(1);
  console.log(2);
}
a();
console.log(3);
```

输出：1， 3， 2

```js
async function a() {
  await console.log(1);
  console.log(2);
}
a();

setTimeont(function() {
  console.log(3);
}, 0);
```

输出： 1, 2， 3
使用 await 时，会从右往左执行，当遇到 await 时，会阻塞函数内部处于它后面的代码，去执行该函数外部的同步代码，当外部同步代码执行完毕，再回到该函数内部执行剩余的代码, 并且当 await 执行完毕之后，会先处理微任务队列的代码


#### 0612 1). 变量提升 2). 函数提升 3). 预处理 4). 调用顺序

```js
var c = 1;
function c(c) {
  console.log(c);
  var c = 3;
}
c(2);
```

这里我们先来了解下 “预处理”，那什么是预处理呢？js 语言本身具有预处理机制，js 引擎在预处理期对所有声明的变量和函数进行处理，就是先把变量进行声明并读到内存里。也就是收集用 var 声明的变量信息和函数声明信息

变量和函数的优先顺序，先变量后函数。当变量名和函数名一致时后者会覆盖前者，我们看下下面的小案例

```js
function b() {}
var b;
console.log(typeof b); // function 说明函数声明覆盖了 变量声明
```

我们看看最开始这个例子的解释
先预处理 var c；然后预处理整个 c 函数

```js
function c(c) {
  console.log(c);
  var c = 3;
}
```

此时 c 的 typeof 为 function

接下来再给 c 赋值,值为 1，即 c=1，此时 c 是整型变量 1.typeof 为 number 了。

最后再执行 c(2)调用函数。所以我们调用的时候当然就会报 c 不是一个函数的错误了.

```js
var c = 1;
function c(c) {
  console.log(c);
  var c = 3;
}
console.log(c);
console.log(typeof c);
c(2);
```

#### 0613 vue 组件的通信方式:

vue 中按照组件中组织关系可以分为：**父子组件 兄弟组件 跨级组件**
vue 中 内置的有两种 :

- ref
- $parent $children

这两种方式都是直接得到组件的实例，基于上下文环境访问父组件或者子组件
这两种访问的方式的弊端是没有办法兄弟组件之间通信，或者跨级组件通信。

在 vue 2.2.0 版本新增的是`provide/inject`顾名思义就是提供和注入
在父组件甚至爷爷组件中提供的属性,只要在后代组件中注入就可以使用：

```js
export default{
  provide:{
    name:'louis'
  }
}

export default{
  inject:['name']
  mounted(){
    consolo.log(name) // louis
  }
}
```

借助第三方工具我们也可以进行组件间的通信是一般也是以下两种：

`eventBus` 使用方式就是相当于在全局设置一个 vue 对象作为数据的中转

另一种就是我们耳熟能详的 `vuex` 了,这种方式也是我们比较推崇的使用方式
可以作为数据状态缓存层使用。api 相对规范。

如果说的更加细分一些，我们还要说下父组件传递给子组件绑定的是 props，当然也可以绑定回调但是我们一般不推荐这样使用，子组件通过`$emit`回调的方式将参数传递给父组件的监听事件用于传参。

#### 14、0614 如何简通读解释异步：（作为异步考察的开胃菜 做到通俗易懂）

所谓"异步"，简单说就是一个任务不是连续完成的，可以理解成该任务被人为分成两段，先执行第一段，然后转而执行其他任务，等做好了准备，再回过头执行第二段。

比如，有一个任务是读取文件进行处理，任务的第一段是向操作系统发出请求，要求读取文件。然后，程序执行其他任务，等到操作系统返回文件，再接着执行任务的第二段（处理文件）。这种不连续的执行，就叫做异步。

相应地，连续的执行就叫做同步。由于是连续执行，不能插入其他任务，所以操作系统从硬盘读取文件的这段时间，程序只能干等着。

#### 0615 摘自阮一峰老师的 es6 中 generator 的例子：

```js
function* foo(x) {
  var y = 2 * (yield x + 1);
  var z = yield y / 3;
  return x + y + z;
}

var a = foo(5);
a.next(); // Object{value:6, done:false}
a.next(); // Object{value:NaN, done:false}
a.next(); // Object{value:NaN, done:true}

var b = foo(5);
b.next(); // { value:6, done:false }
b.next(12); // { value:8, done:false }
b.next(13); // { value:42, done:true }
```

上面代码中，第二次运行 next 方法的时候不带参数，导致 y 的值等于 2 \* undefined（即 NaN），除以 3 以后还是 NaN，因此返回对象的 value 属性也等于 NaN。第三次运行 Next 方法的时候不带参数，所以 z 等于 undefined，返回对象的 value 属性等于 5 + NaN + undefined，即 NaN。

如果向 next 方法提供参数，返回结果就完全不一样了。上面代码第一次调用 b 的 next 方法时，返回 x+1 的值 6；第二次调用 next 方法，将上一次 yield 表达式的值设为 12，因此 y 等于 24，返回 y / 3 的值 8；第三次调用 next 方法，将上一次 yield 表达式的值设为 13，因此 z 等于 13，这时 x 等于 5，y 等于 24，所以 return 语句的值等于 42。

注意，由于 next 方法的参数表示上一个 yield 表达式的返回值，所以在第一次使用 next 方法时，传递参数是无效的。V8 引擎直接忽略第一次使用 next 方法时的参数，只有从第二次使用 next 方法开始，参数才是有效的。从语义上讲，第一个 next 方法用来启动遍历器对象，所以不用带有参数。

#### 0616 react 是如何划分组件的 （react 技术相关）

根据组件的职责通常把组件分为 UI 组件和容器组件。

UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

两者通过 React-Redux 提供 connect 方法联系起来。

#### 0617 请用 js 实现下 栈 这种数据结构(数据结构相关)

栈是一种遵从后进先出的（LIFO）原则的有序集合。新添加的或者删除的元素保存在栈的同一端，称作栈顶，另一端称之为栈底。新元素都靠近栈顶，旧元素都接近栈底。

创建栈这种数据结构

```js
function Stack() {
  // 使用数组保存栈里面的元素
  this.item = [];
}
```

栈这种数据结构应该具有以下方法：

> - push(element(s)): 添加一个或者几个新元素到栈顶
> - pop( ): 移除栈顶的元素，同时返回被移除的元素
> - peek( ): 返回栈顶的元素，不对栈做任何的修改
> - isEmpty( ): 如果栈里面没有任何的元素就返回 true
> - clear( ): 移除栈里面的所有的元素
> - size( ): 返回栈里面的元素的个数

```js
// 向栈中添加元素
Stack.prototype.push = function(element) {
  this.items.push(element);
};

// 从栈中移除元素
Stack.prototype.pop = function() {
  this.items.pop();
};

// 查看栈顶元素
Stack.prototype.peek = function() {
  return this.items[items.length - 1];
};

// 查看栈的长度
Stack.prototype.size = function() {
  return this.items.length;
};

// 检查栈是否为空
Stack.prototype.isEmpty = function() {
  return this.items.length === 0;
};

// 清空栈内元素
Stack.prototype.clear = function() {
  this.items = [];
};
```

#### 0618 静态方法

```js
class test {
  static colorChange(newColor) {
    this.newColor = newColor;
    return this.newColor;
  }

  constructor({ newColor = "green" } = {}) {
    this.newColor = newColor;
  }
}

const newTest = new test({ newColor: "purple" });
newTest.colorChange("orange");

A: orange;
B: purple;
C: green;
D: TypeError;
```

解答：D
test 是一个静态方法。静态方法被设计为只能被创建它们的构造器使用（也就是 test),并且不能传递给实例。因为 newTest 是一个实例，静态方法不能被实例使用，因此抛出了 TypeError 错误.

### 0619 class 和 function 的区别

解答：

1. class 没有变量提升，必须先声明后使用
2. class 不能重复定义，会报语法错误
3. class 定义的类没有私有方法和私有属性
4. class 静态方法与静态属性，
5. 不能用 call apply bind 的方式 来改变他的执行上下文

### 0620 继续异步专题的研究 EventLoop

得心应手版本:
```js

setTimeout(()=>{
  console.log(1) 
},0)
Promise.resolve().then(()=>{
  console.log(2) 
})
console.log(3)

// 输出 3 2 1
```
解答：这个版本的面试官们就特别友善，仅仅考你一个概念理解，了解宏任务(marcotask)微任务(microtask)，这题就是送分题。

>这个是属于Eventloop的问题。main script运行结束后，会有微任务队列和宏任务队列。微任务先执行，之后是宏任务。

有时候会有版本是宏任务>微任务>宏任务，在这里需要讲清楚一个概念，以免混淆。这里有个main script的概念，就是一开始执行的代码（代码总要有开始执行的时候对吧，不然宏任务和微任务的队列哪里来的），这里被定义为了宏任务（笔者喜欢将main script的概念单独拎出来，不和两个任务队列混在一起），然后根据main script中产生的微任务队列和宏任务队列，分别清空，这个时候是先清空微任务的队列，再去清空宏任务的队列。


### 0621 继续异步专题的研究 EventLoop 考点 promise 的执行

游刃有余版本:

```js
setTimeout(()=>{
  console.log(1) 
},0)

let a = new Promise((resolve)=>{
  console.log(2)
  resolve()
}).then(()=>{
  console.log(3) 
}).then(()=>{
  console.log(4) 
})

console.log(5) 
```
解答:
此题看似在考`Eventloop`，实则考的是对于`Promise`的掌握程度。`Promise`的then是微任务大家都懂，但是这个then的执行方式是如何的呢，以及Promise的executor是异步的还是同步的需要对promise 有深入的理解才能答对。

这个要从Promise的实现来说，Promise的executor是一个 **同步函数** ，即非异步，立即执行的一个函数，因此他应该是和当前的任务一起执行的。而Promise的链式调用then，每次都会在内部生成一个新的Promise，然后执行then，在执行的过程中不断向微任务(microtask)推入新的函数，因此直至微任务(microtask)的队列清空后才会执行下一波的macrotask。

### 0622 继续异步专题的研究 EventLoop 考点：promise的进阶用法，对于then中return一个promise的掌握

炉火纯青版本：

这一个版本是上一个版本的进化版本，上一个版本的promise的then函数并未返回一个promise，如果在promise的then中创建一个promise，那么结果该如何呢？

```js
new Promise((resolve,reject)=> {
  console.log("promise1")
  resolve()
}).then(()=>{
  console.log("then11")
  new Promise((resolve,reject)=> {
    console.log("promise2")
    resolve()
  }).then(()=> {
    console.log("then21")
  }).then(()=> {
    console.log("then23")
  })
}).then(() => {
  console.log("then12")
})
// promise1
// then11
// promise2
// then21
// then12
// then23
// Promise {<resolved>: undefined}
```
解答：遇到这种嵌套的Promise不要慌，应该清楚的分析当前的队列的行为，
第一轮：
  * current task: promise1是当之无愧的立即执行的一个函数，promise是一个立即执行的函数，立即执行输出[promise1];
  * micro task queue: [promise1的第一个then];
第二轮：
  * current task: then1执行中，立即输出了then11以及新 promise2 的 `promise2`
  * micro task queue: [新promise2的then函数,以及promise1的第二个then函数]
第三轮：
  * current task: 新promise2的then函数输出then21和promise1的第二个then函数输出then12。
  * micro task queue: [新promise2的第二then函数]
第四轮
  * current task: 新promise2的第二then函数输出then23
  * micro task queue: []


### 0622 继续异步专题的研究 EventLoop 考点 如果说这边的Promise中then返回一个Promise呢？
```js

new Promise((resolve,reject) => {
  console.log("promise1")
  resolve()
}).then(() => {
  console.log("then11")
  return new Promise((resolve,reject)=>{
    console.log("promise2")
    resolve()
  }).then(()=>{
    console.log("then21")
  }).then(()=>{
    console.log("then23")
  })
}).then(()=>{
  console.log("then12")
})

// promise1
// then11
// promise2
// then21
// then23
// then12
// Promise {<resolved>: undefined}
```
这里就是Promise中的then **返回** 一个promise的状况了，这个考的重点在于 **Promise而非Eventloop** 了。这里就很好理解为何then12会在then23之后执行，这里Promise1的第二个then相当于是挂在新Promise2的最后一个then的返回值上。

### 0623 继续异步专题的研究 EventLoop 考点 如果说这边不止一个Promise呢，再加一个new Promise是否会影响结果?

```js
new Promise((resolve,reject)=>{
  console.log("promise1")
  resolve()
}).then(()=>{
  console.log("then11")
  new Promise((resolve,reject)=>{
    console.log("promise2")
    resolve()
  }).then(()=>{
    console.log("then21")
  }).then(()=>{
    console.log("then23")
  })
}).then(()=>{
  console.log("then12")
})

new Promise((resolve,reject)=>{
  console.log("promise3")
  resolve()
}).then(()=>{
  console.log("then31")
})

// promise1
// promise3
// then11
// promise2
// then31
// then21
// then12
// then23
```
解答： 这里需要注意一点promise1 和 promise3 是同步关系
第一轮
  * current task: promise1，promise3
  * micro task queue: [ then11, promise2的第一个then，promise3的第一个then]

第二轮
  * current task: then11，promise2，then31
  * micro task queue: [promise2的第一个then，promise1的第二个then]

第三轮
  * current task: then21，then12
  * micro task queue: [promise2的第二个then]

第四轮
  * current task: then23
  * micro task queue: []


### 0624 继续异步专题的研究 EventLoop 考点:在async/await之下，对Eventloop的影响。

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}

async function async2() {
  console.log("async2");
}

console.log("script start");  {1}

setTimeout(function() {
  console.log("setTimeout");
}, 0);

async1();                     {2}

new Promise(function(resolve) {
  console.log("promise1");
  resolve();
}).then(function() {
  console.log("promise2");
});

console.log("script end");

// script start
// async1 start
// async2
// promise1
// script end
// async1 end
// promise2
// setTimeout
```

解答：使用事件循环机制分析:

>1.首先执行同步代码，console.log( 'script start' );
2.遇到 setTimeout, 会被推入宏任务队列;
3.执行 async1(), 它也是同步的，只是返回值是 Promise，在内部首先执行 console.log( 'async1 start' );
4.然后执行 async2(), 然后会打印 console.log( 'async2' );
5.从右到左会执行, 当遇到 await 的时候，阻塞后面的代码，**去外部执行同步代码**;
6.进入 new Promise,Promise 底层实现是一个立即执行函数 会打印 console.log( 'promise1' );
7.将.then 放入事件循环的微任务队列;
8.继续执行同步代码，打印 console.log( 'script end' );
9.外部同步代码执行完毕，接着回到 async1()内部, 由于 async2() 其实是返回一个 Promise, await async2()相当于获取它的值，其实就相当于这段代码 Promise.resolve(undefined).then((undefined) => {}),所以.then 会被推入微任务队列, 所以现在微任务队列会有两个任务。接下来处理微任务队列，打印 console.log( 'promise2' )，后面一个.then 不会有任何打印，但是会执行 
10.执行后面的代码, 打印 console.log( 'async1 end' );
11.进入第二次事件循环，执行宏任务队列, 打印 console.log( 'setTimeout' );

#### 0625 下面代码会输出什么？ 考点: this的指向：

```js
var x = 3;
var foo = {
  x: 2,
  baz: {
    x: 1,
    bar: function() {
      return this.x;
    }
  }
}
var go = foo.baz.bar;

console.log(go());  // 3
console.log(foo.baz.bar()); // 1
```

解答:this由调用者提供，由调用函数的方式来决定。如果是一个对象调用的函数，则this指向该对象，比如foo.baz.bar()。如果函数独立调用比如go()，那么该函数内部的this，则指向undefined。但是在非严格模式中，它会被自动指向全局对象window。
上诉这段代码中，go的执行是在全局环境中执行的,因此指向的是全局环境中的3。

### 0626 写一个逆序字符串的函数 考点 js 原生字符串处理的掌握。
解答:
```js
function reverse(str) {
  let res = str.split('');
  return res.reverse().join('');
}
reverse('hello world!'); // output: '!dlrow olleh'
```

### 0627 React使用中用过哪些状态管理工具，紧接着问了redux和mobx的区别
1、Redux鼓励一个应用之中只有一个store，Mobx鼓励实现多个store
2、Redux是函数式编程，而Mobox的模式是面向对象的。
3、Redux鼓励数据的规范化，减少冗余。Mobx 容许数据冗余，但同样能保持数据一致
4、5.Redux更严格，必须调用reducer触发action来改变state， Mobx 最初的一个卖点就是直接修改数据，但是实践中大家还是发现这样无组织无纪律不好，所以后来 Mobx 还是提供了 action 的概念。和 Redux 的 action 有点不同，Mobx 中的 action 其实就是一个函数，不需要做 dispatch。如果想强制要求使用 action，禁止直接修改 observable 数据，使用 Mobx 的 configure
```js
import {configure} from 'mobx';
configure({enforceActions: true});
```

### 0628 call的模拟实现 考点 对于原生js的熟练程度 基本面试必考
解答：主要参考了冴羽老师的博客：[call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)
```js
var foo = {
  value:1,
  bar:function(){
  console.log(this.value)
}
}

function bar(){
  console.log(this.value)
}

bar.call(foo,1,2,3);
```

```js
Function.prototype.call2 = function (context) {
  // call 函数传入的this指向可能是null 如果是null 用window替代
  var context = context || window;
  // 将调用call的函数 传递给 context 变成它内部的函数
  context.fn = this;

  // 处理参数问题，call函数的参数是不定参数，因此需要整合成一个数组
  var args = [];
  for(var i = 1, len = arguments.length; i < len; i++) {
      args.push('arguments[' + i + ']');
  }
  // call 函数可能具有返回值。
  var result = eval('context.fn(' + args +')');
  // 删除这个多余的属性 
  delete context.fn
  // 返回结果
  return result;
}
```

### 0629 apply的模拟实现 考点 对于原生js的熟练程度
解答：主要参考了冴羽老师的博客：[call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)
```js
Function.prototype.apply = function (context, arr) {
    var context = Object(context) || window;
    context.fn = this;

    var result;
    if (!arr) {
        result = context.fn();
    }
    else {
        var args = [];
        for (var i = 0, len = arr.length; i < len; i++) {
            args.push('arr[' + i + ']');
        }
        result = eval('context.fn(' + args + ')')
    }

    delete context.fn
    return result;
}
```

### 0630 bind模拟实现 考点 对于原生js的熟练程度
解答：主要参考了冴羽老师的博客：[bind的模拟实现](https://github.com/mqyqingfeng/Blog/issues/12)

```js
  Function.prototype.bind2 = function (context) {

    if (typeof this !== "function") {
      throw new Error("Function.prototype.bind - what is trying to be bound is not callable");
    }

    var self = this;
    var args = Array.prototype.slice.call(arguments, 1);

    var fNOP = function () {};

    var fBound = function () {
        var bindArgs = Array.prototype.slice.call(arguments);
        return self.apply(this instanceof fNOP ? this : context, args.concat(bindArgs));
    }

    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();
    return fBound;
}
```

### 0701 页面上有个空的无序列表节点，用 <ul></ul> 表示，要往列表中插入 3 个 <li>，每个列表项的文本内容是列表项的插入顺序，取值 1, 2, 3，怎么用原生的 JS 实现这个需求？同时约定，为方便获取节点引用，可以根据需要为 <ul> 节点加上 id 或者 class 属性（考点 原生dom的操作）
```js
<ul id="list"></ul>
// 获取 list 节点
var container = document.getElementById('list');
// 循环 创建3个li标签
for (var i = 0; i < 3; i++) {
  var item = document.createElement('li');
  item.innerText = i + 1;
  // 使用 appendChild 将元素放进去
  container.appendChild(item);
}
```
或者:
```js
var container = document.getElementById('list');
var html = [];
for (var i = 0; i < 3; i++) {
    html.push('<li>' + (i + 1) + '</li>');
}
container.innerHTML = html.join('');
```
如果我们想要增加代码的容错能力，避免变量的污染，我们可以用下面的这套代码来代替
```js
(() => {
  var ndContainer = document.getElementById('js-list');
  if (!ndContainer) {
    return;
  }

  for (var i = 0; i < 3; i++) {
    var ndItem = document.createElement('li');
    ndItem.innerText = i + 1;
    ndContainer.appendChild(ndItem);
  }
})();
```

### 0702 接着昨天的问题,如果我们想要弹出来每一个li里面的内容应该如何做呢？
使用es6的中的块级作用域的概念可以帮助我们避免踩坑，这个问题前面的题目有所涉及
```js
for (let i = 0; i < 3; i++) {
  const ndItem = document.createElement('li');
  ndItem.innerText = i + 1;
  ndItem.addEventListener('click', function () {
      alert(i);
  });
  ndContainer.appendChild(ndItem);
}
```
而熟悉 addEventListener 文档的候选人会给出下面的方法：
```js
for (var i = 0; i < 3; i++) {
  var ndItem = document.createElement('li');
  ndItem.innerText = i + 1;
  ndItem.addEventListener('click', function () {
    alert(this.innerText);
  });
  ndContainer.appendChild(ndItem);
}
```
因为 EventListener 里面默认的 this 指向当前节点。



###  0706 说一下new操作符都做了哪些事情 考点：js面向对象系统
解答：四大步骤：
1、创建一个空的对象，并且this变量引用该对象.
2、继承函数的原型。
3、属性和方法被添加到this的引用中，并执行该函数。
4、新创建的对象由 this 所引用，并且最后隐式的返回 this。

```js
function new(func) {
	lat target = {};
	target.__proto__ = func.prototype;
	let res = func.call(target);
	if (typeof(res) == "object" || typeof(res) == "function") {
		return res;
	}
	return target;
}
```

###  0707 遍历DOM树 （递归、树形结构、原生dom操作）
```js
function traverse(node){
  // dom nodeType 1 --> 元素节点 nodeType 2 --> 属性节点 nodeType 3 --> 文本节点
  if(node && node.nodeType === 1){
    console.log(node.tagName);
  } 
  
  var i = 0,childNodes = node.childNodes,item;
  for(;i<childNodes.length;i++){
    item = childNodes[i];
    if(item.nodeType === 1){
      traverse(item)
    }
  }
}
```

### 0708 ['1', '2', '3'].map(parseInt) what & why ? 考察对于数组的 map 方法的掌握程度

解答：输出的结果是 [1, NaN, NaN];

首先 map 的基本的用法要非常的熟悉：(MDN 定义)

> map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

首先看一个我们平时使用 map 的场景

```js
var array1 = [1, 4, 9, 16];
// pass a function to map
const map1 = array1.map(x => x * 2);
console.log(map1);
// expected output: Array [2, 8, 18, 32]
```

map 函数的一个参数是一个 callback 回调函数,map 会保证数组中的每一个元素都进过这个回调函数的处理，然后返回一个新的数组，map 不会对原有的数组进行修改。

上述的题目答案为什么是 [1, NaN, NaN] 呢？ 这还得从这个回调函数 callback 说起。
这个 callback 接收三个参数：

```js
  currentValue：callback 数组中正在处理的当前元素。
  index: callback 数组中正在处理的当前元素的索引。
  array: map 方法被调用的数组。
```

通常情况下，map 方法中的 callback 函数只需要接受一个参数，就是正在被遍历的数组元素本身。但这并不意味着 map 只给 callback 传了一个参数。这个思维惯性可能会让我们犯一个很容易犯的错误。

上述这个题目中 我们将 map 的回调函数设置为 parseInt，而 parseInt 则是用来解析字符串的，使字符串成为指定基数的整数。

通常使用parseInt时,只需要传递一个参数，但实际上,parseInt可以有两个参数.第二个参数是进制数.

map方法在调用callback函数时,会给它传递三个参数:当前正在遍历的元素, 元素索引, 原数组本身.第三个参数parseInt会忽视, 但第二个参数不会,也就是说,parseInt把传过来的索引值当成进制数来使用.从而返回了NaN.

> parseInt(string, radix) 接收两个参数，第一个表示被处理的值（字符串），第二个表示为解析时的基数。

了解这两个函数后，我们可以模拟一下运行情况:

```js
  1、parseInt('1', 0) //radix为0时，且string参数不以“0x”和“0”开头时，按照10为基数处理。这个时候返回1
  2、parseInt('2', 1) //基数为1（1进制）表示的数中，最大值小于2，所以无法解析，返回NaN
  3、parseInt('3', 2) //基数为2（2进制）表示的数中，最大值小于3，所以无法解析，返回NaN
```

### 0709 考查数组的 reduce()方法

```js
(1) var arr = [1, 2, 3, 4];
var sum = arr.reduce(function(prev, cur, index, arr) {
    console.log(prev, cur, index);
    return prev + cur;
})
console.log(arr, sum);
(2) [1, 2, 3, 4].reduce((x, y) => console.log(x, y));
```

打印结果:
(1）1 2 1

      3 3 2

      6 4 3

      [1,2,3,4] 10

(2) 1 2, undefined 3, undefined 4

解答：

语法：arr.reduce(callback,[initialValue])

reduce 为数组中的每一个元素依次执行回调函数，不包括数组中被删除或未被赋值的元素。
  
 callback 接受四个参数：

```js
  Accumulator (acc) 初始值（或上一次回调函数的返回值）
  Current Value (cur) 当前元素值
  Current Index (idx) 当前索引
  Source Array (src) 调用reduced的数组
```

说明：未设置初始值 index 从 1 开始;

    函数没有返回值，则默认返回undefined。

####  0710 说说 Redux 和 Vuex 的设计思想:

1、软件设计领域有很多通用的思想，比如隔离变化，约定优于配置

解答:[Vuex、Flux、Redux、Redux-saga、Dva、MobX](https://zhuanlan.zhihu.com/p/53599723) 这个链接文章非常值得一看

####  0711 什么是 HTTPS 与 HTTP 的区别是什么？ 考察 http 的网络知识

解析：要理解 HTTPS 首先需要明白 http 的缺点是什么，知道背景之后再理解记忆 https 就比较简单了。

HTTP 主要有这些不足，例举如下:

> - 1.通信使用明文（不加密），内容可能会被窃听 http 本身并不具备加密的功能。
> - 2.不验证通信方的身份，因此有可能遭遇伪装
> - 3.无法证明报文的完整性，所以有可能已遭篡改

HTTPS（全称：Hyper Text Transfer Protocol over Secure Socket Layer），是以安全为目标的 HTTP 通道，简单讲是 HTTP 的安全版。即 HTTP 下加入 SSL 层，HTTPS 的安全基础是 SSL，因此加密的详细内容就需要 SSL。 现在它被广泛用于万维网上安全敏感的通讯，例如交易支付方面。

HTTP 与 HTTPS 的区别：

> - 1.HTTP 是明文传输，HTTPS 通过 SSL\TLS 进行了加密
> - 2.HTTP 的端口号是 80，HTTPS 是 443
> - 3.HTTPS 需要到 CA 申请证书，一般免费证书很少，需要交费
> - 4.HTTP 的连接很简单，是无状态的；HTTPS 协议是由 SSL+HTTP 协议构建的可进行加密传输、身份认证的网络协议，比 HTTP 协议安全。
> - 5、传输速度方面 HTTPS 由于中间的 SSL 链接会消耗一部分性能问题。

#### 0712 什么是 HTTP 三次握手？为什么是三次而不是两次，具体的过程是怎样的 考察 http 的网络知识

TCP 为了保证将数据准确无误的送达到目标处，采用了三次握手的策略,同时为了验证客户端和服务端的发送和接收的功能是正常的。

主要过程可以概括为下面四句话：

> - 1.第一次握手：客户端给服务器发送一个 SYN 报文。
> - 2.第二次握手：服务器收到 SYN(synchronize) 报文之后，会应答一个 SYN+ACK 报文。
> - 3.第三次握手：客户端收到 SYN+ACK 报文之后，会回应一个 ACK(acknowledgement) 报文。
> - 4.服务器收到 ACK 报文之后，三次握手建立完成。

那我们解释一下为什么是三次而不是两次呢？

> - 1.第一次握手：客户端发送网络包，服务端收到了。这样服务端就能得出结论：客户端的发送能力、服务端的接收能力是正常的。
> - 2.第二次握手：服务端发包，客户端收到了。这样客户端就能得出结论：服务端的接收、发送能力，客户端的接收、发送能力是正常的。不过此时服务器并不能确认客户端的接收能力是否正常。
> - 3.第三次握手：客户端发包，服务端收到了。这样服务端就能得出结论：客户端的接收、发送能力正常，服务器自己的发送、接收能力也正常。

需要三次握手才能确认双方的接收与发送能力是否正常。

如果中间的链接中断，TCP 协议会再次以相同的顺序发送相同的数据包。

将这个过程详细一点描述：

刚开始客户端处于 closed 的状态，服务端处于 listen 状态。然后

> - 1.第一次握手：客户端给服务端发一个 SYN 报文，并指明客户端的初始化序列号 ISN(c)。此时客户端处于 SYN_Send 状态。
> - 2.第二次握手：服务器收到客户端的 SYN 报文之后，会以自己的 SYN 报文作为应答，并且也是指定了自己的初始化序列号 ISN(s)，同时会把客户端的 ISN + 1 作为 ACK 的值，表示自己已经收到了客户端的 SYN，此时服务器处于 SYN_RECV 的状态。
> - 3.第三次握手：客户端收到 SYN 报文之后，会发送一个 ACK 报文，当然，也是一样把服务器的 ISN + 1 作为 ACK 的值，表示已经收到了服务端的 SYN 报文，此时客户端处于 establised 状态。
> - 4.服务器收到 ACK 报文之后，也处于 establised 状态，此时，双方以建立起了链接。

我们看到有两个中间状态，syn_sent 和 syn_recv，这两个状态叫着「半打开」状态，就是向对方招手了，但是还没来得及看到对方的点头微笑。syn_sent 是主动打开方的「半打开」状态，syn_recv 是被动打开方的「半打开」状态。客户端是主动打开方，服务器是被动打开方。

####  0713 三次握手的作用？对上面一个题目的补充:

> - 1.确认双方的接受能力、发送能力是否正常。
> - 2.指定自己的初始化序列号，为后面的可靠传送做准备。
> - 3.如果是 https 协议的话，三次握手这个过程，还会进行数字证书的验证以及加密密钥的验证等等。

####  0714 三次握手其他问题的补充

1.（ISN）是固定的吗 也就是初始化的序列号是固定的吗？
三次握手的一个重要功能是客户端和服务端交换 ISN(Initial Sequence Number), 以便让对方知道接下来接收数据的时候如何按序列号组装数据。

如果 ISN 是固定的，攻击者很容易猜出后续的确认号，因此 ISN 是动态生成的。

2.什么是半连接队列?
服务器第一次收到客户端的 SYN 之后，就会处于 SYN_RECV 状态，此时双方还没有完全建立其连接，服务器会把此种状态下请求连接放在一个队列里，我们把这种队列称之为半连接队列。当然还有一个全连接队列，就是已经完成三次握手，建立起连接的就会放在全连接队列中。如果队列满了就有可能会出现丢包现象。

3.三次握手过程中可以携带数据吗?
很多人可能会认为三次握手都不能携带数据，其实第三次握手的时候，是可以携带数据的。也就是说，第一次、第二次握手不可以携带数据，而第三次握手是可以携带数据的。

假如第一次握手可以携带数据的话，如果有人要恶意攻击服务器，那他每次都在第一次握手中的 SYN 报文中放入大量的数据，因为攻击者根本就不理服务器的接收、发送能力是否正常，然后疯狂着重复发 SYN 报文的话，这会让服务器花费很多时间、内存空间来接收这些报文。也就是说，第一次握手可以放数据的话，其中一个简单的原因就是会让服务器更加容易受到攻击了。

而对于第三次的话，此时客户端已经处于 established 状态，也就是说，对于客户端来说，他已经建立起连接了，并且也已经知道服务器的接收、发送能力是正常的了，所以能携带数据页没啥毛病。

#### 0715 四次挥手

TCP 断开链接的过程和建立链接的过程比较类似，它分成了 4 个动作。

刚开始双方都处于 establised 状态，假如是客户端先发起关闭请求，则：

> - 1.第一次挥手：客户端发送一个 FIN 报文，报文中会指定一个序列号。此时客户端处于 FIN_WAIT1 状态。
> - 2.第二次挥手：服务端收到 FIN 之后，会发送 ACK 报文，且把客户端的序列号值 + 1 作为 ACK 报文的序列号值，表明已经收到客户端的报文了，此时服务端处于 CLOSE_WAIT 状态。
> - 3.第三次挥手：如果服务端也想断开连接了，和客户端的第一次挥手一样，发给 FIN 报文，且指定一个序列号。此时服务端处于 LAST_ACK 的状态。
> - 4.第四次挥手：客户端收到 FIN 之后，一样发送一个 ACK 报文作为应答，且把服务端的序列号值 + 1 作为自己 ACK 报文的序列号值，此时客户端处于 TIME_WAIT 状态。需要过一阵子以确保服务端收到自己的 ACK 报文之后才会进入 CLOSED 状态.
> - 5.服务端收到 ACK 报文之后，就处于关闭连接了，处于 CLOSED 状态。

这里特别需要主要的就是 TIME_WAIT 这个状态了，这个是面试的高频考点，就是要理解，为什么客户端发送 ACK 之后不直接关闭，而是要等一阵子才关闭。这其中的原因就是，要确保服务器是否已经收到了我们的 ACK 报文，如果没有收到的话，服务器会重新发 FIN 报文给客户端，客户端再次收到 ACK 报文之后，就知道之前的 ACK 报文丢失了，然后再次发送 ACK 报文。

至于 TIME_WAIT 持续的时间至少是一个报文的来回时间。一般会设置一个计时，如果过了这个计时没有再次收到 FIN 报文，则代表对方成功就是 ACK 报文，此时处于 CLOSED 状态。

#### 0716 常见的复杂度分析： 算法复杂度分析

```js
function swapTwoInts(a, b) {d
  var temp = a;
  a = b;
  b = temp;
}
```

O(1) 的时间复杂度，所花费的时间和数据规模没有关系，常数级别的。

```js
// 计算从0 到 n 的和
function sum(n) {
  var ret = 0;
  for (var i = 0; i <= n; i++) {
    ret += i;
  }
  return ret;
}
```

O(n) 这种复杂度的一个典型的特点是 有一个循环。

```js
void reverse (string &s) {
  int n = s.size();
  for(int i = 0;i<n/2;i++){
    swap(s[i],s[n-1-i])
  }
}
```

O(n) 随着 n 的增加整个时间的增加是线性增加的。

```js
void selectionSort(int arr[],int n) {
  for(int i = 0; i < n; i++){
    int minIndex = i;
    for(int j = i+1; j < n; j++){
      if(arr[j]<arr[minIndex]){
        minIndex = j;
      }
    }
    swap(arr[i], arr[minIndex])
  }
}
```

选择排序是从数组的开头开始，将第一个元素和其他元素作比较，检查完所有的元素后，最小的放在第一个位置，接下来再开始从第二个元素开始，重复以上一直到最后。

我们可以分析一下指令数,if 的比较数
n = 0 -> （n-1）
n = 1 -> (n-2)
……
0
O($n^2$) 的时间复杂度

#### 0717 手写深拷贝

1、遍历对象

```js
  function deepClone(source){
    if(typeof source == ('string'|| 'number')){
      return source;
    }
    if(!source || typeof source != 'object'){
      throw new Error("error arguments!")
    }
    var newSource = source.constructor === Array? [] : {};
    for(var key in source){
      if(source.hasOwnProperty(key)){
        if(typeof source[key] !== 'object'){
          newSource[key] = source[key]
        }else{
          newSource[key] = deepClone(source[key])
        }
        return newSource;
      }
    }
```
2、用JSON.parse 和JSON.stringify配合使用实现深拷贝  不仅适用于数组还适用于对象！但是这种拷贝的方式不能拷贝函数。
```js
  // 对象深拷贝
  let a = {a:1};
  let b = JSON.parse(JSON.stringify(a));
  a.a = 2;

  console.log(a); // { a: 2 }
  console.log(b); // { a: 1 }

  // 数组的深拷贝 改变原数组 新的数组并不会受影响
  var arr = ['old', 1, true, ['old1', 'old2'], {old: 1}]
  var new_arr = JSON.parse( JSON.stringify(arr) );

  arr[0] = 'new'
  console.log(arr); // [ 'new', 1, true, [ 'old1', 'old2' ], { old: 1 } ]
  console.log(new_arr); // [ 'old', 1, true, [ 'old1', 'old2' ], { old: 1 } ]
```

3、通过jQuery的extend方法实现深拷贝

```js
  let array = [1,2,3,4];
  let newArray = $.extend(true,[],array);
```

4、lodash函数库实现深拷贝

```js
  lodash.cloneDeep()
```

#### 0718 如何判断是否为数组？
1、Object.prototype.toString()返回的是//"[object Type]"的形式，通过call将Array的this上下文切换到Object，从而调用了Object.prototype.toString()，因此返回[object Function]。（https://www.jianshu.com/p/e4237ebb1cf0）

```js
 let a=[1,2, 3, 4];
 console.log(Object.prototype.toString.call(a))//[object Array]
```

2、我们创建的每个函数都有一个prototype（原型）对象，这个属性是一个指针，指向一个对象。在默认情况下，所有原型对象都会自动获得一个constructor（构造函数）属性，这个属性是一个指向prototype属性所在函数的指针。因为constructor属性在构造函数的原型里，并且指向构造函数，那我们就可以利用constructor属性来判断一个实例对象是由哪个构造函数构造出来的，也可以说判断它属于哪个类。

```js
  let a = [1,2, 3, 4];
  console.log(a.constructor); // ƒ Array() { [native code] }
```

3、instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。通俗来将就是判断一个实例对象是否由某个构造函数构造而来。只要对象实例的原型链不发生变化，instanceof便可以正确判断。

```js
  let a = [1,2, 3, 4];
  console.log(a instanceof Array); //true
```

#### 0719 简述cookie、sessionStorage和localStorage的区别

共同点：都是保存在浏览器端，且同源的。
区别：

- (1)cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。
- (2)cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。
- (3)存储大小限制不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。
- (4)数据有效期不同 sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。
- (5)作用域不同 sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。

#### 0720 手写一个冒泡的排序算法 算法实现

```js
<input v-model="sth" />
<input v-bind:value="sth" v-on:input="sth = $event.target.value" />
```

#### 0721 说一下http1.0 http1.1 和http2.0的区别:
影响HTTP 网络请求的因素主要有两个：带宽和延迟：
  带宽：现在的网络都已经告别拨号上网，所以这块不需要特别关注
  延迟：
    1、浏览器阻塞，浏览器会因为一些原因阻塞请求，会对同一个域名，同时建立4个连接（这个因浏览器而异）
       超过最大的连接数之后，后续请求就会被阻塞。
    2、DNS查询，其实也是需要消耗时间的，但是DNS缓存的机制可以达到减少时间的目的。
    3、建立连接：主要是TCP三次握手的时间消耗,如果使用https话消耗会更大。
       三次握手无法复用。
  
  HTTP1.1子啊1999年才开始应用现在的各大浏览器网络请求中，同时也是目前使用最广的HTTP协议
  
  HTTP1.0和HTTP1.1的一些区别：
  1、缓存处理，在HTTP1.0中，使用header里的If-Modified-Since,Expires来做为缓存判断的标准，HTTP1.1则引入了更多的缓存控制策略例如Entity tag，If-Unmodified-Since, If-Match, If-None-Match等更多可供选择的缓存头来控制缓存策略。
  2、带宽优化以及网络连接的使用，HTTP1.0中，存在一些浪费的现象，例如客户端只是需要某个对象一部分，而服务器却将整个对象送过来了，并且不支持断点续传的功能，HTTP1.1则在请求头中引入了range头域，它允许只请求资源的某一个部分，即返回206 这样开发者方便了开发者自由选择以便充分利用带宽和链接。
  3、错误通知的管理，在HTTP1.1中新增了24个错误状态码，例如409表示请求的资源和当前状态发生冲突；410表示服务器上的某一个资源被永久的删除。
  4、HOST头处理，在HTTP1.0中认为每台服务器都绑定一个唯一的IP地址，因此，请求消息中的URL并没有传递主机名（hostname）。但随着虚拟主机技术的发展，在一台物理服务器上可以存在多个虚拟主机（Multi-homed Web Servers），并且它们共享一个IP地址。HTTP1.1的请求消息和响应消息都应支持Host头域，且请求消息中如果没有Host头域会报告一个错误（400 Bad Request）。
  5、长连接，HTTP 1.1支持长连接（PersistentConnection）和请求的流水线（Pipelining）处理，在一个TCP连接上可以传送多个HTTP请求和响应，减少了建立和关闭连接的消耗和延迟，在HTTP1.1中默认开启Connection： keep-alive，一定程度上弥补了HTTP1.0每次请求都要创建连接的缺点。

  HTTP2.0和HTTP1.x的一些区别：
  1、新的二进制格式（Binary Format），HTTP1.x的解析是基于文本。基于文本协议的格式解析存在天然缺陷，文本的表现形式有多样性，要做到健壮性考虑的场景必然很多，二进制则不同，只认0和1的组合。基于这种考虑HTTP2.0的协议解析决定采用二进制格式，实现方便且健壮。

  2、多路复用（MultiPlexing），即连接共享，即每一个request都是是用作连接共享机制的。一个request对应一个id，这样一个连接上可以有多个request，每个连接的request可以随机的混杂在一起，接收方可以根据request的 id将request再归属到各自不同的服务端请求里面。

  3、header压缩，如上文中所言，对前面提到过HTTP1.x的header带有大量信息，而且每次都要重复发送，HTTP2.0使用encoder来减少需要传输的header大小，通讯双方各自cache一份header fields表，既避免了重复header的传输，又减小了需要传输的大小。

  4、服务端推送（server push），同SPDY一样，HTTP2.0也具有server push功能。
  
#### 0722 选择排序实现 考察点 常见的算法实现
  ```js
  function selectSort(arr=[]) {
    if (arr === undefined || arr.length < 2) {
      return;
    }
    let len = arr.length;
    for (let i = 0; i < len; i++) {
      let minIndex = i;
      for (let j = i + 1; i < len; j++) {
        if (arr[j] < arr[minIndex]) {
          minIndex = j;
        }
      }
      // 每一轮循环完毕后 将当前这一轮的 最小的放在前面
      [arr[i], arr[minIndex]] = [arr[minIndex], arr[i]];
    }
    return arr;
  }
  ```

#### 0723 插入排序实现 考察点 常见的算法实现:
```js
  var obj  = {};
  Object.defineProperty(obj, 'name', {
    get: function() {
        console.log('我被获取了')
        return val;
    },
    set: function (newVal) {
        console.log('我被设置了')
    }
  })
  obj.name = 'fei';//在给obj设置name属性的时候，触发了set这个方法
  var val = obj.name;//在得到obj的name属性，会触发get方法
```

#### 0724 如何实现数组的浅拷贝：
在平时的业务开发中，如果是一维数组，我们可以巧妙的借用两个数组方法来进行操作：concat slice

>* concat方法用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组。

```js
var arr = ['old', 1, true, null, undefined];
var newArr = arr.concat(); // 并不会改变原来的数组，
newArr[0] = 'new'
console.log(newArr); // [ 'new', 1, true, null, undefined ]
```

>* slice() 方法返回一个新的数组对象，这一对象是一个由 begin 和 end 决定的原数组的浅拷贝（包括 begin，不包括end）。原始数组不会被改变。

```js
var arr = ['old', 1, true, null, undefined];
var newArr = arr.slice(); // 这里省略了 begin 和 end 意味着拷贝整个数组
newArr[0] = 'new'
console.log(newArr); // [ 'new', 1, true, null, undefined ]
```
上文说道，上述的方式比较适合简单的一维数组的拷贝，如果数组中嵌套了对象或者数组，利用concat拷贝会发生一些问题：
```js
var arr = [{old: 'old'}, ['old']];
var new_arr = arr.concat(); // 或者 slice()

arr[0].old = 'new';
arr[1][0] = 'new';

console.log(arr) // [{old: 'new'}, ['new']]
console.log(new_arr) // [{old: 'new'}, ['new']]
```
我们会发现，无论是新数组还是旧数组都发生了变化，也就是说使用 concat 方法，克隆的并不彻底。

如果数组元素是基本类型，就会拷贝一份，互不影响，而如果是对象或者数组，就会只拷贝对象和数组的引用，这样我们无论在新旧数组进行了修改，两者都会发生变化。

我们把这种复制引用的拷贝方法称之为浅拷贝，与之对应的就是深拷贝，深拷贝就是指完全的拷贝一个对象，即使嵌套了对象，两者也相互分离，修改一个对象的属性，也不会影响另一个。

所以我们可以看出使用 concat 和 slice 是一种浅拷贝。


#### 0725 如何实现数组的扁平化操作 (数组扁平化操作，一般使用递归调用操作)
首先解释一下什么是数组的扁平化操作, 简单的说就是将多维的数组拍平变成一维数组：

例如： arr = [1, [2, [3, 4]]];  数组拍平之后变成了 [1,2,3,4];

一般这种实现，我们首先想到的是递归,直接上代码：
```js
var arr = [1, [2, [3, 4]]];
function flatten(arr) {
  var result = [];
  for (var i = 0, len = arr.length; i < len; i++) {
    if (Array.isArray(arr[i])) {
      result = result.concat(flatten(arr[i]))
    }else {
      result.push(arr[i])
    }
  }
  return result;
}
console.log(flatten(arr))
```

直接使用 `Array.prototype.flat(depth)`这个原生的api也能达到相同的目的.
其中depth 的意思是展开的深度,默认值是1。如果传入这个参数`Infinity`展开任意的
数据

#### 0726 将数组扁平化并去除其中重复数据，最终得到一个升序且不重复的数组
var arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10];

```js
function flatten(arr){
  var result = [];
  for(var i = 0,len = arr.length;i<len;i++){
    if(Array.isArray(arr[i])){
      result = result.concat(flatten(arr[i]))
    }else{
      result.push(arr[i])
    }
  }
  return result
}

function unique(resultArr){
  return [...new Set(resultArr)]
}

function upSort(arr){
  return arr.sort(function(a,b){
    return a-b
  })
}
upSort(unique(flatten(arr)))
```
###  0728 Vue中给data中的对象属性添加一个新的属性时会发生什么，如何解决？
```js
   <div><ul>
     <li v-for="value in obj":key="value">
     {{value}}
     </li> </ul>
    <button @click="addObjB">添加obj.b</button>
    </div>
    export default {
     data (){
      return
       { 
         obj:{
         a:'obj.a'      
       }
    }},
    methods:{
    addObjB (){
        this.obj.b ='obj.b'


       console.log(this.obj)
   }}}
```
解答：Vue实例创建时， obj.b 并未声明，因此就没有被Vue转换为响应式的属性，自然就不会触发视图的更新，这时就需要使用Vue的全局api—— $set() this.$set(this.obj,'b','obj.b')


#### 0806 你是如何理解Vue的响应式系统的?

响应式系统简述:
任何一个 Vue Component 都有一个与之对应的 Watcher 实例。
Vue 的 data 上的属性会被添加 getter 和 setter 属性。
当 Vue Component render 函数被执行的时候, data 上会被 触碰(touch), 即被读, getter 方法会被调用, 此时 Vue 会去记录此 Vue component 所依赖的所有 data。(这一过程被称为依赖收集)
data 被改动时（主要是用户操作）, 即被写, setter 方法会被调用, 此时 Vue 会去通知所有依赖于此 data 的组件去调用他们的 render 函数进行更新。

#### 0806 Vue中的key到底有什么用？

key是为Vue中的vnode标记的唯一id,通过这个key,我们的diff操作可以更准确、更快速
diff算法的过程中,先会进行新旧节点的首尾交叉对比,当无法匹配的时候会用新节点的key与旧节点进行比对,然后超出差异.

diff程可以概括为：oldCh和newCh各有两个头尾的变量StartIdx和EndIdx，它们的2个变量相互比较，一共有4种比较方式。如果4种比较都没匹配，如果设置了key，就会用key进行比较，在比较的过程中，变量会往中间靠，一旦StartIdx>EndIdx表明oldCh和newCh至少有一个已经遍历完了，就会结束比较,这四种比较方式就是首、尾、旧尾新头、旧头新尾.

准确: 如果不加key,那么vue会选择复用节点(Vue的就地更新策略),导致之前节点的状态被保留下来,会产生一系列的bug.
快速: key的唯一性可以被Map数据结构充分利用,相比于遍历查找的时间复杂度O(n),Map的时间复杂度仅仅为O(1).

![图片](https://user-gold-cdn.xitu.io/2019/8/1/16c498ca0e514088?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)


###  0812 Vue中如何监控某个属性值的变化
```js
  1.Vue中监控对象属性的变化 
      watch:{
        obj:{
          handler(newValue, oldValue){
            console.log('obj changed')
           },
          deep:true
         }
      }
  2.watch:{
    'a':{
        handler(newName, oldName){
          console.log('a changed')
        }
       }
     }
  3.computed:{
       a1(){
        return this.obj.a
      }
   }
```
watch 和 computed的不同：
computed监听的变量不能在data中初始化
computed储存需要处理的数据值，有缓存机制，只有改变时才执行
watch 可以监听路由router的变化，props、data、computed内的数据变化

###  0815 vue 中v-model原理及应用

1、v-model是语法糖

```html
<input v-model="sth" />
<input v-bind:value="sth" v-on:input="sth = $event.target.value" />
```

第一行的代码其实只是第二行的语法糖,在给<input />元素添加v-model属性时，默认会把value作为元素的属性，然后把'input'事件作为实时传递value的触发事件
2、修饰符
1.lazy 
在输入框中，v-model默认是在input事件中同步输入框的数据的，使用修饰符lazy则会变为在change事件中同步

```html
<input type="text" v-model.lazy="message"/>
<p>当前输入是：{{message}}</p>
```

2.number 
使用该修饰符可以将输入转化为Number类型，否则我们得到的数据的类型是String。

```html
<input type="text" v-model.number="message"/>
<p>当前输入是：{{message}}</p>
```

3.trim 
这个修饰符可以自动过滤输入的首尾空格。

```html
<input type="text" v-model.trim="message"/>
<p>当前输入是：{{message}}</p>
```

###  0815 Vue中双向绑定 简单原理说明（https://www.cnblogs.com/zhenfei-jiang/p/7542900.html）

1、vue是采用数据劫持结合发布者-订阅者模式的方式，通过`Object.defineProperty()`来劫持各个属性的`setter`，`getter`，在数据变动时发布消息给订阅者，触发相应的监听回调。

2、`Object.defineProperty()`:
Object.defineProperty有三个参数
参数一: 要添加属性的对象，
参数二: 要添加的属性名称
参数三: 将被定义或修改的属性描述符。

```js
var obj  = {};
Object.defineProperty(obj, 'name', {
  configurable: true, // 值为true的时候值才能被改变
  enumerable: true, // 可枚举
  get: function() {
    console.log('我被获取了')
    return val;
  },
  set: function (newVal) {
    console.log('我被设置了')
  }
})
obj.name = 'fei';   //在给obj设置name属性的时候，触发了set这个方法
var val = obj.name; //在得到obj的name属性，会触发get方法
```

3、vue如何实现原理图
![图片](https://upload-images.jianshu.io/upload_images/8560482-d18d5fe20c1ade5c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/730/format/webp)


1、实现一个数据监听器Observer，能够对数据对象的所有属性进行监听，如有变动可拿到最新值并通知订阅者
2、实现一个指令解析器Compile，对每个元素节点的指令进行扫描和解析，根据指令模板替换数据，以及绑定相应的更新函数
3、实现一个Watcher，作为连接Observer和Compile的桥梁，能够订阅并收到每个属性变动的通知，执行指令绑定的相应回调函数，从而更新视图
4、mvvm入口函数，整合以上三者

###  0827 js中的前自增和后自增区别:
- 1. 前自增:算数运算符优先于赋值运算符，先执行++操作，再执行赋值操作
```js
  var a = 1 ;
  var b = ++a ;// a = a+1, b =a 
  console.log(a);// 2
  console.log(b); //2
```
- 2. 后自增：赋值运算符优先于算数运算符，先执行赋值操作，再执行++操作
```js
  var a =1;
  var b = a++ ;// b =a , a = a +1
  console.log(a);//2
  console.log(b);//1
```
在vue的源码中的 `initMinin` 函数中 _uid 使用的是 **后自增**, `vm._uid = uid++` => `vue = {_uid : 0}`
初始化的时候 _uid 就是 0 因此赋值的时候就是0 下次使用使用的时候就变成1了。


###  0902 window.location.search 用法总结
- 1、在bing搜索中 搜索study 这样关键字 浏览器中复制链接如下：

https://cn.bing.com/search?q=study&qs=n&form=QBRE&sp=-1&pq=study&sc=8-5&sk=&cvid=C454F59743B94FE6833BAEE96ED19CEC

- 2、使用 `window.location.search` 方法截取url参数获得以下链接：
console.log(window.location.search): 
?q=study&qs=n&form=QBRE&sp=-1&pq=study&sc=8-5&sk=&cvid=C454F59743B94FE6833BAEE96ED19CEC

- 3、截取的链接中是以问号开头的，需要截取问号后面的拼接参数键值对。使用substring() 方法 注意subsrt()这个方法已经逐渐被废弃
  subString()方法：方法返回一个字符串在开始索引到结束索引之间的一个子集, 或从开始索引直到字符串的末尾的一个子集。
  第二个参数是可以选择的 如果不写则 默认截取到最后,第一个参数是开始的位置的索引.
- 4、在很多的项目中使用 import * as querystring from 'querystring' 
  这种使用方式 逐渐取代之前的那种方式, 

###  0903 Vue.use(plugin)的用法：
  最近在梳理项目的时候，发现项目中使用了很多vue的插件。所以回顾下use的用法：
  - 参数：`{Object | Function} plugin`
  - 用法: 安装 Vue.js 插件。如果插件是一个对象，必须提供 install 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。该方法需要在调用 new Vue() 之前被调用。当 install 方法被同一个插件多次调用，插件将只会被安装一次。


### 跨域问题:
  从一个a站点获取一个页面，然后在这个页面去访问b站点的资源，所以会产生跨域，使用XMLHttpRequest对象去发送请求，就会有跨域限制
  这是浏览器做的限制。

  我们使用script标签就不会有这样的限制

  jsonp的跨域解决方案:
  利用script标签发送请求，在客户端定义一个回调函数，给服务端传递的是回调函数的名称，服务端返回的是一个回调函数的调用。
  服务端的数据包在函数里面。

### 0904: react 代码中 如果return 后面不添加括号会怎样？

我们在使用JSX语法书写react代码时，babel会将JSX语法编译成js，同时会在每行自动添加**分号**（；），如果`return`后换行了，那么就会变成 `return；` 一般情况下会报错：

- **Nothing was returned from render. This usually means a return statement is missing. Or, to render nothing, return null.**

**上面这段英文翻译成中文：**

- 渲染没有返回任何内容。这通常意味着缺少return语句。或者，为了不渲染，返回null。

为了代码可读性我们一般会在return后面添加括号这样代码可以折行书写，否则就在return 后面紧跟着语句，这样也是可以的。

举两个正确的书写例子：
```js
const Nav = () => {
  return (
    <nav className="c_navbar">
      { some jsx magic here }
    </nav>
  )
}

const Nav = () => {
 return <nav className="c_navbar">
    { some jsx magic here }
  </nav>
}
```
错误的写法：
```js
const Nav = () => {
  return
    <nav className="c_navbar">
      { some jsx magic here }
    </nav>
}
```

### 0905 react中的 `componentWillUpdate` 钩子函数,可以直接修改state的值吗？

react组件在每次需要重新渲染时候都会调用`componentWillUpdate()`,

例如，我们调用 `this.setState()`时候

在这个函数中我们之所以不调用`this.setState()`是因为该方法会触发另一个`componentWillUpdate()`,如果我们`componentWillUpdate()`中触发状态更改,我们将以无限循环结束。


### 0905  什么是渲染劫持？
首先，什么是渲染劫持：渲染劫持的概念是控制组件从另一个组件输出的能力，当然这个概念一般和react中的高阶组件（HOC）放在一起解释比较有明了。

高阶组件可以在render函数中做非常多的操作，从而控制原组件的渲染输出，只要改变了原组件的渲染，我们都将它称之为一种渲染劫持。

实际上，在高阶组件中，组合渲染和条件渲染都是渲染劫持的一种，通过反向继承，不仅可以实现以上两点，还可以增强由原组件render函数产生的React元素。

实际的操作中 通过 操作 state、props 都可以实现渲染劫持

### 0906 说说 react中Fragment 的应用：
1、一般来说，我们希望书写有语义的HTML元素，符合相应的嵌套标准，但是有时候 语义化的HTML会被破坏。
比如我们在写jsx书写 `<div>` `<ol>、<ul>` 由于jsx语法的根节点的限制。我们就是会破坏结构。
```js
import React, { Fragment } from 'react';

// 函数组件 在渲染列表的时候 最外层我们并没有使用 div 或者 dl 作为根标签，因为这个函数
// 作用只是渲染列表没有涉及外部的包裹的元素。
function ListItem({ item }) {
  return (
    <Fragment>
      <dt>{item.term}</dt>
      <dd>{item.description}</dd>
    </Fragment>
  );
}

// 父组件中 外层已经提供了 dl标签 这时候 Fragment 起到了作用
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        <ListItem item={item} key={item.id} />
      ))}
    </dl>
  );
}
```

### 0908 react16.6版本中的变化
react.lazy()的应用。react在16.6的版本中添加了 react.lazy() 的这个功能让代码分割变得非常的容易
为了展示这个股票的软件的stackTable相关的内容，我们加载了很多不必要的内容。

这个是非常让我们头疼的。react16.6 版本中新增加的react.lazy() 是一个非常重要的应用。
我在思考我们的项目是否能够直接升级成react16.6的版本。

### 0909 react中选择合适的组件类型原则

SFC(Stateless Functional Compoent):
react 可以使用 function 来创建 component 这种组件 没有生命周期 内部不需要维护 state 只要传入 props 有变化则进行重新渲染。

```js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

// 使用箭头 函数更加简洁
const Welcome = props => <h1>Hello, {props.name}</h1>;
```

HOC(Higher-Order Components)
高阶组件对于 Vue 开发者来说应该是个陌生的概念（不知道，我用 Vue 的时候没见过类似的用法）。从代码上看，高阶组件就是一个方法，传入一个组件，返回另一个组件。

最常见的高阶组件是 react-redux 里面的 connect 方法，通过传入 组件和 map*ToProps 方法，让组件和 store 连接。组件内部就可以直接通过 props 获得 connect 之后的值。

```js
function logProps(WrappedComponent) {
  return class extends React.Component {
    componentWillReceiveProps(nextProps) {
      console.log('Current props: ', this.props);
      console.log('Next props: ', nextProps);
    }
    render() {
      return <WrappedComponent {...this.props} />;
    }
  }
}

exprot default connect(
  mapStateToProps,
  mapDispatchToProps,
)(Component);

```
Dynamic Component:

有些业务场景下，在执行时才能确定具体的标签或者组件是什么。在 React 的世界里面，以大写字母开头会被当成动态组件加载，而小写字母开头会被认为是 HTML DOM tag。

```js
// Heading.js
render() {
  const { tag: Tag, children } = this.props;
  return <Tag>{ children }</Tag>
}
```

### 0910 算法题目：给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。你可以假设数组中无重复元素。

输入: [1,3,5,6], 5
输出: 2

输入: [1,3,5,6], 2
输出: 1

输入: [1,3,5,6], 7
输出: 4

分析：这道题目思路非常简单, 循环一遍数组, 和相应的目标值作比较，首先考虑相等的情况，这种情况非常容易得出
只要判断 nums[i] === target 就可以得出，如果数字是可以插入到数组中的某个特定的位置, 例如第二个例子中
本来3是在第二个位置，现在2进来之后，就占据了3的位置,数组扩容了.因此当数组的nums[i]>target 时候，返回的也是
当前的这个遍历到的元素的位置 i 最后一种情况 是遍历完之后 没有发现符合上述两种情况，说明这个数字比数组中的任何一个数字
都要大，直接插入到数组最后，数组的length 是从1 开始的 length 指的是第一个没有元素的位置

```js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var searchInsert = function(nums, target) {
  for(let i = 0;i<nums.length;i++){
    if(nums[i] >= target){
      return i;
    }
  }
  return nums.length;
};
```

### 给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
输入: [-2,1,-3,4,-1,2,1,-5,4],
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
    
};
```

### 0917为什么说React中的props是只读的？
react官方文档中说道，组件无论是使用函数声明还是通过class声明，都绝不能修改自身的props，props 作为组件对外通信的一个接口，为了保证组件像纯函数一样没有响应的副作用，所有的组件都必须像纯函数一样保护它们的props不被修改

### 0918 如果组件的属性没有传值，那么它的默认值是什么？
默认值是undefined。

### 1026 说一下什么情况下会出现undefined,你对undefined的理解:

1、如果没有声明直接访问一个变量，会提示错误信息 xx is not defined 而不是undefined，因此很多人理解的有错误。
2、一个变量声明了，但是没有赋值，值默认为是undefined
```js
var b
console.log(b) // b 就是一个undefined类型的值
```
3、一个变量声明了，并且赋值了undefined的值
```js
var c = undefined
console.log(c) // c 就是一个undefined类型的值
```
4、一个对象中,获取某一个并不存在的属性也是返回 undefined
```js
var d = {};
console.log(d.age) // 由于d对象中没有age属性 所以返回undefined
```

### 你能简单的说一下js中数据类型吗？说一下js中的数据类型吧，js 中有哪几种数据类型。

分析，这种问题属于送分的题目，概念性很强，但是回答好也是不容易的，面试官可能会以此为维度继续往下深度挖掘，直到，挖到非常的底层
让面试者，没有办法回答出来。

可以这样回答:

js的数据类型按照大类型分可以分为两种：基本数据类型（也叫做原始数据类型）和引用数据类型（也叫做对象类型），

基本数据类型有以下几种：number string undefined boolean null 在es6 中又加入了 symbol这种数据类型。
js中除了原始类型之外都是对象类型了。


### 原始类型数据有什么特点？和对象类型有什么不同
回答: 原始数据类型在数据存储的位置上来分，是存储在栈中的，存储的是值，正因为如此，原始类型是没有函数或者方法可以调用的，比如 'undefined.toString()' Uncaught TypeError: Cannot read property 'toString' of undefined 爆出这个错误。

除了这个之外，js中的 number 这种原始类型也会有一些坑，js中的number 是浮点类型的 比如 0.1+0.2 !== 0.3 也正是因为这种情况，我们在实际的项目中是不怎么建议在前端做数值的计算的，即使是做计算，金额也是做展示使用而不是做判断，否则容易采坑。

另外 string 这种原始数据类型 是不可以改变的，我们对于string 做的所有操作都是相当于拷贝了一个副本，原本的字符串不会发生变化。

### 你还是没有说出对象类型的特点啊？
回答：对象类型和原始类型不同的是，对象类型存储的是（地址）指针，当我们创建一个对象的时候,计算机会在内存中开辟一个空间存放这个值，当我们找到这个空间的时候，会拥有一个指针。

这里举一个例子:
const a = [] 这里给常量a分配了一个内存地址 #001,那么在地址 a 位置存放了一个空数组 [],
const a = [];const b = a; b.push(1) 这里面当我们将a 赋值给b 的时候实际上 给的是一个地址，也是#001
当我们在这个地址上进行修改的时候,所有引用这个地址的变量的值都会发生变化。

### null 是原始类型还是 对象类型 为什么使用typeof 判断的时候返回的是object
实际上 null 是原始类型 之所以 typeof 返回的是object 这实际上是js 中存在已久的bug，现在也没有办法修复，修复之后所造成的影响太大了。

### 如果一个函数参数是对象会发生什么问题？
```js
function test(person) {
  person.age = 26
  person = {
    name: 'yyy',
    age: 30
  }

  return person
}
const p1 = {
  name: 'yck',
  age: 25
}
const p2 = test(p1)
console.log(p1) // -> ?
console.log(p2) // -> ?
```

首先函数传参是传递对象指针的副本，就拿这个例子来说,p1 传递进去，在内部 修改了p1的值，当然p1的值也会发生改变，
但是 当我们在函数内部重新给person 赋值了一个对象的时候, 出现了分歧, 实际上这个时候 被重新赋值的这个person和
原本传递进去的p1已经没有任何的关系了。

### js中你都是怎么判断类型的，判断类型中遇到了什么问题？
我们可以使用：typeof 判断类型 除了null 之外都是可以正确判断的，返回值是一个string 类型
```js
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
```
typeof 对于对象来说，除了函数 都是返回object 因此并不能准确的判断变量到底是什么类型
```js
typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'
```
一般使用中会做一些对象类型判空处理，防止一些空指针的异常场景

当然还可以使用 instanceof 用于实例和构造函数的对应，如果我们使用typeof 是没有办法准确的区分 数组的 因为typeof 除了 function
全部返回的是 object 使用instanceof 就可以 [1,2,3] instanceof Array 返回的就是true。或者 我们平时使用构造器创建对象的时候
可以使用 对象 instanceof 构造器 是否为true 判断对象是不是这个构造器的实例。

### 说一下js你了解的js中的类型转换：
回答：js 中的类型转换只有三种情况；
1、转换为布尔值
2、转换为数字
3、转换为字符串

我们实际开发中，会经常遇到将一些数据字段值，转换为布尔值进行判断的情况，这个时候，能够准确的识别
是非常重要的，在js中 除了 undefined null false  NaN '' 0 -0 其他所有的值都转换为true,包括所有的对象
最后这个是重点，我们在实际项目中有过采坑的经历，在if的判断条件中, 对某一个初始值为 {} 这个对象做了布尔判断，
直接进入了逻辑，在update钩子函数中,直接爆了空指针的异常问题。吃过生产的亏。

### 说一下this吧
JavaScript 中的this和其他语言有比较大的不同，一句话概括来说，this总是指向一个对象，具体指向哪个对象是基于函数的执行环境
动态绑定的，而非函数被声明时的环境。

实际的场景中 this 的指向大概分为4种:
1、作为对象的方法调用
2、作为普通函数调用
3、构造器调用
4、Function.prototype.call 或者 Function.prototype.apply 调用

1、当作为函数的方法被调用时，this 指向该对象:
```js
var obj = {
  a: 1,
  getA: function(){
    alert ( this === obj );    // 输出：true
    alert ( this.a );    // 输出: 1
  }
} 
```

2、作为普通函数调用:
也就是我们常说的普通函数方式，此时的this总是指向全局对象。在浏览器的JavaScript里，这个全局对象是window对象。
```js
window.name = 'globalName';
var getName = function(){
  return this.name;
}
console.log( getName() );  // 输出：globalName”
// 或者
window.name = 'globalName';
var myObject = {
  name: 'sven',
  getName: function(){
    return this.name;
  }
};
var getName = myObject.getName;
console.log( getName() );  // globalName
```

3、当用new运算符调用函数时，该函数总会返回一个对象，通常情况下，构造器里的this就指向返回的这个对象，见如下代码：
```js
var MyClass = function(){
  this.name = 'sven';
};

var obj = new MyClass();
alert ( obj.name ); // 输出：sven”
```
这里会有一个坑，如果一个构造器显示的返回了一个object类型的对象，那么此次运算的结果
会最终会返回这个对象而不是this
```js
var MyClass = function(){
  this.name = 'sven';
  return {    // 显式地返回一个对象
    name: 'anne'
  }
};

var obj = new MyClass();
alert ( obj.name );     // 输出：anne”
```
4、Function.prototype.call 或者 Function.prototype.apply 调用
当一个函数借助call 或者apply 调用的时候,this可以动态的绑定。

### 9、箭头函数中的this 指向哪里呢？
```js
function a() {
  return () => {
    return () => {
      console.log(this)
    }
  }
}
console.log(a()()())
```
回答：首先箭头函数其实是没有 this 的，箭头函数中的 this 只取决包裹箭头函数的第一个普通函数的 this。在这个例子中，因为包裹箭头函数的第一个普通函数是 a，所以此时的 this 是 window。另外对箭头函数使用 bind 这类函数是无效的。

### 说一下闭包吧,什么是闭包，实际开发过程中有哪些应用？你觉得闭包的缺点是什么？
回到：闭包的定义：函数A内部有一个函数B，函数B可以访问函数A中的变量，那么函数B就是闭包。

```js
function A() {
  let a = 1
  window.B = function () {
    console.log(a)
  }
}
A()
B() // 1
```
闭包存在的意义是，我们可以间接的访问函数内部的变量。
应用场景:
  循环中使用闭包解决 ‘var’定义函数的问题: 因为`setTimeout`是一个异步函数,所以会先把循环执行完毕，这个时候i就是6了，所以会
  输出一堆6。
```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i)
  }, i * 1000)
}
```
解决的办法分为三种：
  1、使用立即执行函数, 我们使用立即执行函数，将变量i 传入函数，固定在了j上面，这样在下次使用的
  时候，可以用到外部的j 从而达到目的。
  ```js
  for(var i = 0;i<=5;i++){
    ;(function(j){
      setTimeout(function timer(){
        console.log(j)
      },j*1000)
    })(i)
  }
  ```
  2、第二种的解决办法是 使用setTimeOut的第三个参数，这个参数会被当成函数timer的参数传入
  ```js
  for(var i = 0;i<=5;i++){
    setTimeout(function timer(j){
      console.log(j)
    },1000*i,i)
  }
  ```
  3、第三种方式是使用 let 来定义 i 这也是最为推荐的一种方式。
  ```js
  for(let i = 0; i<=5;i++){
    setTimeout(function timer(){
        console.log(i)
    })
  }
  ```
### 你是如何理解js中的原型的？简单的说一下js中的原型吧。
  回答：js中的面向对象系统就是基于原型的,我觉得可以通过以下四个方面理解原型:
    1、所有的引用类型（数组，对象，函数）都具有对象的特性，可以自由的扩展属性
    2、所有的引用类型 (数组，对象，函数) 都有一个__proto__ 属性，属性值是一个普通的对象。
    3、所有的函数，都有一个prototype的属性，属性值也是一个对象，
    4、所有的引用类型（数组，对象，函数）,__proto__ 属性值指向它的构造函数的prototype属性值。
  ```js
    // 要点一：自由扩展属性
    var obj = {}; obj.a = 100;
    var arr = []; arr.a = 100;
    function fn () {}
    fn.a = 100;
    
    // 要点二：__proto__
    console.log(obj.__proto__);
    console.log(arr.__proto__);
    console.log(fn.__proto__);

    // 要点三：函数有 prototype
    console.log(fn.prototype)

    // 要点四：引用类型的 __proto__ 属性值指向它的构造函数的 prototype 属性值
    console.log(obj.__proto__ === Object.prototype)
  ```
### 如何判断一个对象中的属性是属于这个对象本身的属性呢？
  回答：使用 hasOwnProperty 可以进行遍历对象 打印出来真正属于这个对象本身的属性而不是原型上的属性
  ```js
  var item
  for (item in f) {
    // 高级浏览器已经在 for in 中屏蔽了来自原型的属性，但是这里建议大家还是加上这个判断，保证程序的健壮性
    if (f.hasOwnProperty(item)) {
      console.log(item)
    }
  }
  ```
### 我知道的是，在高级浏览器中 使用for in 遍历已经屏蔽了来自原型额属性，为什么还是使用上述的这种冗余写法呢？
  为了浏览器的兼容性的问题

### 为什么js中对于异步处理非常的重要？
  本质上的原因是 js 是一个单线程的语言，在同一时间内只能做一件事情，一心不可二用。
  下面这个是一个死循环,因为跑到 while循环的时候 一致执行，没有剩余的资源和时间去执行这个setTimeout函数
  ```js
  var a = true;
  setTimeout(function(){
    a = false;
  }, 100)
  while(a){
    console.log('while执行了')
  }
  ```
### delete 这个api 只能删除 对象中的属性，不能删除变量。

### 什么是构造函数，它和普通函数有什么区别？
+ 其实任何函数都可以当做构造函数
+ 只要把一个函数通过new的方法进行调用，我们就把这一次函数的调用方法称之为：构造函数的调用

### 你能说一下 构造函数的执行过程吗？
`var p1 = new Person();`
+ 1、创建一个对象，（我们把这个对象称之为Person构造函数的实例）- p1
+ 2、创建一个内部对象 this 将this 指向了该实例
+ 3、执行函数内部的代码，其中，操作this的部分就是操作了该实例
+ 4、返回值：
  - a 如果函数没有返回值 （没有return语句） 那么就会返回构造函数的实例 p1
  ```js
    function fn(){}  var f1 = new fn() // f1 就是fn的实例
  ```
  - b 如果函数返回了一个基本的数据类型，那么本次构造函数的返回值是该实例
  ```js
    function fn2(){
      return "abc"
    }
    var f2 = new fn2() // f2 也是fn2的实例
  ```
  - c 如果函数返回一个复杂的数据类型的值，那么本次函数的返回值就是该值
  ```js
    function fn3(){
      return [1,2,3]
    }
    f3 = new fn3() // f3不再是 fn3的实例
  ```
### JS中的继承的概念:
  + 通过[**某种方式**]让一个对象可以访问到另一个对象中的属性和方法，我们把这种方式称之为继承

### 为什么要使用继承？
  + 有些对象会有方法（动作、行为）而这些方法都是函数，如果把这些方法和函数放在构造函数中声明就会导致内存的浪费

### 为什么要使用模块化？有哪几种实现模块化的方式？模块化是如何发展的？
  + 解决命名冲突的问题
  + 提供复用性代码
  + 提高代码的可维护性

  在发展中，比较熟悉的一种实现方式是 **立即执行函数**，通过函数的作用于解决了变量冲突的问题，
  ```js
  (function(globalVariable){
    globalVariable.test = function() {}
    // ... 声明各种变量、函数都不会污染全局作用域
  })(globalVariable)
  ```
  AMD 和 CMD 规范（这两种模块化的形式我在工作中没有实际中没有用到过）

  CommonJs规范 这种规范最早是在node 中使用的，现在也在广泛的使用，比如在写webpack配置文件的时候。

  ESmodule 


css 常见知识点:
###  如何分类css的中标签:
回答: css 中的标签其实有很多，但是按照大类别分 可以分为“块级元素”和“内联元素”

### 什么是内联元素，什么是块元素？特点是什么？img 是什么元素。如何将行内元素转换为块元素
块级元素：一个水平流上只能放置一个元素，多个块级元素换行显示，且元素高度、行高，内边距padding 外边距margin都是可以控制的
内联元素：多个内联元素可以在同一行显示，且高度、行高 内边距、外边距不可控制，默认的高度和宽度是由内部的内容撑开的。
img 标签我看的资中属于 行内替换元素（replaced inline element） 属于inline element 

行内元素转换为块元素的方式是：设置disply属性 block table list-item 都是可以具有快速的特性，但是用的最多的是 block

### 如何将多个元素，设置在同一行？
第一种方式是使用浮动 float 第二种方式是设置元素 disolay-inline-block 

### 你能想出几种清除浮动的方式，为什么要清除浮动？

### 说一下 display:none 和 visibility: hidden 并且说出其他几种隐藏元素的方式
  + display：none 隐藏对应的元素，在文档布局中不再给她分配空间，它各边的元素 会合拢，就当它不存在
  + visibility：hidden 隐藏对应的元素，但是在文档布局中仍然保留原来的空间。
  ```js
  1、opacity: 0;
  2、visibility: hidden;
  3、display: none;
  4、position: absolute;
    top: -9999px;
    left: -9999px;
  5、clip-path: polygon(0px 0px,0px 0px,0px 0px,0px 0px);
  ```
### 单行文字超出部分省略号显示
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
### （算法题）求多个数组之间的交集（阿里）
  认真的阅读题目就可以想到，这道题目的结果应该是输出一个集合，es6 中新增两种重要的数据结构 set 和 map 用于
  补充es5中的数组和对象。
  
  这道题目的思路就是 使用set数据结构结合数组的filter方法进行筛选出符合条件的数据
  ```js
    let a = new Set([1,2,3]);
    let b = new Set([4,3,2]);
    let intersect = new Set([...a].filter(x => b.has(x)))
    //set{ 2, 3}
  ```

### React组件的构造函数有什么作用？
React组件的class是基于es6的语法规范实现的，react的渲染有两种情况，一个是初次渲染，一个是状态更新之后的再次渲染，构造函数在组件的初次渲染中只会运行一次，构造函数里进行的操作一般有三种用途：
1、指定this -->  super(props)
2、设置初始化的状态 --> this.setState({});
3、为组件上的构造函数绑定this;

### 如何理解react中的super() 和 super(props)?
react 中的class 是基于es6的规范实现的, 继承是使用extends关键字实现继承的，子类必须在constructor()中调用super() 方法否则新建实例
就会报错，报错的原因是 子类是没有自己的this对象的，它只能继承父类的this对象，然后对其进行加工，而super()就是将父类中的this对象继承给子类的，没有super() 子类就得不到this对象。

如果你使用了constructor就必须写super() 这个是用来初始化this的，可以绑定事件到this上
如果你想要在constructor中使用this.props,就必须给super添加参数 super(props)
注意，无论有没有 constructor，在render中的this.props都是可以使用的，这是react自动附带的
如果没有用到constructor 是可以不写的，react会默认添加一个空的constroctor.

### lombok @Data 注解到底干了个啥?
在java bean 定义的时候，需要给每个字段提供set 和 get 属性，lombok这个插件所做的事情就是在编译期间
替我们干了这个事情。

### 请描述一下状态码304
  表示浏览器端有缓存，并且服务端未更新，不用向服务器端请求资源。
### 实现一个方法，找出一个数组中重复的元素(10分)
举例
arr : [1,2,3,4,1,1,2,4,4]
输出 [1,2,4]
```js
Array、prototype、repeNum : function(){
  let new_arr : this、sort();  //先排序
  let res : [] ;
  for( let i : 0 ; i < new_arr、length ; i++){
      if(new_arr[i] :: new_arr[i+1] &&    //判断是否重复,是否已经放入容器
      new_arr[i] !:new_arr[i-1]){
        res、push(new_arr[i]);
      }
  }
  return res
}
```

### 将这段英文this is a pen首字母大写(10分)
法一：
```js
function bigLetter(str){
  let newArr : str、split(" ")、map((v,i):>{
    return v、slice(0,1)、toUpperCase() + v、slice(1)
  })
  return newArr、join(" ")
}
```

法二：
```js
function bigLetter(str){
  bigStr : str、toLowerCase()、replace(/\b\w+\b/g, function(word){
    return word、substring(0,1)、toUpperCase()+word、substring(1);
  });
  return bigStr;
}
```
### 请写出你常用的10个linux命令并说明作用(20分)
 - ls命令: 显示目录内容，类似DOS下的DIR
 - mv命令: 更改文件或者目录的名字。mv[options]source destination
  -f：强制模式，覆盖文件不提示。
  -i：交互模式，当要覆盖文件的时候给提示。
  将main.html 修改名称为 index.html mv -f main.html index.html 不加 -f 其实也是可以的

### 请写出你常用的5个git命令并说明作用(15分)
 - git clone [url]: 下载一个项目和它的整个代码历史
 - git add [file1] [file2] ... 添加指定文件到暂存区
 - git add . 添加当前目录的所有文件到暂存区
 - git commit -m [message] 提交暂存区到仓库区
 - git commit [file1] [file2] ... -m [message] 提交暂存区的指定文件到仓库区
 - git branch 列出所有本地分支
 - git branch -r 列出所有远程分支
 - git branch -a 新建一个分支，但依然停留在当前分支

### 跟keep-alive 相关的生命周期有哪些？描述下这些生命周期
  1、activated: 页面第一次进入的时候，钩子触发的顺序是 created > mounted > activated
  2、deactivated: 页面退出的时候会触发deactivated,当再次前进或者后退的时候只触发activated

### 项目中有没有用过Eslint

ESLint（中文站点）是一个开源的 JavaScript 代码检查工具，使用 Node.js 编写，由 Nicholas C. Zakas 于 2013 年 6 月创建。ESLint 的初衷是为了让程序员可以创建自己的检测规则，使其可以在编码的过程中发现问题而不是在执行的过程中。ESLint 的所有规则都被设计成可插入的，为了方便使用，ESLint 内置了一些规则，在这基础上也可以增加自定义规则。

### 1101 你了解es6数组中的扩展运算符吗？怎样使用？有什么使用场景？
扩展运算符语法是（...）可以将一个数组转换为用逗号分隔的参数序列

用法实例:
```js
console.log(...[1, 2, 3]) // 1 2 3
使用场景: 一般使用 函数的参数调用中

function add(x, y){
  return x + y
}
const params = [3, 24]
add(...params) // 27

扩展运算符和正常的函数的参数可以放在一起使用非常的灵活

扩展运算符后面还可以跟上表达式,

let x=0;
const arr = [
  ...(x > 0 ? 'b':[]),
  "a"
]
上面这行代码,输出的就是 ["a"]

如果上面的x变成一个 1 渲染出来的就是一个 ["b","a"]
```

### 1101 有没有在项目中使用过proxy，你是如何理解proxy的？
proxy 是es6 中新添加的 api 可以理解成在目标对象之前架设一层 "拦截"，外界对这个对象的访问，都必须先通过这层拦截
因此提供了一种机制，可以对外界的访问进行过滤和改写。

### 1101 react-router 中我们常用两种路由模式 hashHistory 和 browserHistory 路由模式，他们在使用的时候有什么区别。
使用 hashHistory 的时候 浏览器的url 应该是这样的   /#/user/liuna
使用 browserHistory 的时候 浏览器的 url 应该是这样的  /user/liuna

虽然 react中推荐使用的是 browserHistory 这种路由方式，这种路由方式 需要server端的配置支持。
使用hashHistory时，因为有 # 的存在，浏览器不会发送request,react-router 自己根据 url 去 render 相应的模块。
也就是它的请求头中 无论你的#后面拼接的是什么都不会 request origin 中将 # 拼接进去，

使用browserHistory时，从 / 到 /user/liuna, 浏览器会向server发送request，所以server要做特殊请求，比如用的 express 的话，你需要 handle 所有的路由 app.get('*', (req, res) => { ... })，使用了 nginx 的话，nginx也要做相应的配置。

### 1101 17、为什么react 官方推荐使用 browserHistory 而不是 hashHistory 这种形式？

首先 browserHistory 其实使用的是 HTML5 的 History API，浏览器提供相应的接口来修改浏览器的历史记录；而 hashHistory 是通过改变地址后面的 hash 来改变浏览器的历史记录；

另一个原因是 hash 部分并不会被浏览器发送到服务端，也就是说不管是请求 http://domain.com/index.html#foo 还是 http://domain.com/index.html#bar ，服务只知道请求了 index.html 并不知道 hash 部分的细节。而 History API 需要服务端支持，这样服务端能获取请求细节。

### 1109 react 什么时候开始使用状态管理器？
  可以分为两个维度去思考：
  + 从项目的整体看：
    1、用户的使用方式复杂。
    2、不同身份的用户有不同的使用方式（比如普通用户和管理员）
    3、多个用户之间可以协作
    4、与服务端进行交互，或者使用websocket
    5、view要从多个来获取数据
  * 从组件的角度看
    1、某一个组件的状态，需要共享
    2、某个状态需要在任何地方都能拿到
    3、一个组件需要改变全局状态
    4、一个组件需要改变另一个组件的状态
### 1109 render 函数中 return如果没有使用 () 会有什么问题？
我们在使用jsx语法书写react代码的时，babel会将jsx语法编译成js，同时会在每行自动的添加 **分号**（;）,如果`return`后换行了，那么就会变成 `return;` 一般情况下会报错：
  + Nothing was returned from render. This usually means a return statement is missing. Or, to render nothing, return null.

  上面这段翻译成中文就是：
  + 渲染没有返回任何内容。这通常意味着缺少return语句。或者，为了不渲染，返回null。

  为了代码可读性，我们一般会在return 后面添加 **圆括号** 这样代码可以折行书写，否则就在return后面紧跟着语句，这样也是可以的。

  举两个正确的例子:
  ```js
    const Nav = () => {
      return (
        <nav className="c_navbar">
          { some jsx magic here}
        </nav>
      )
    }

    const Nav = () => {
      return <nav className="c_navbar">
        { some jsx magic here }
      </nav>
    }
  ```
  错误的写法:
  ```js
  const Nav = () => {
    return
    <nav className="c_navbar">
      { some jsx magic here }
    </nav>
  }
  ```
### 1109 componentWillUpdate  可以直接修改state值吗？
  react 组件在每一次需要重新渲染的时候都会调用 `componentWillUpdate()`， 例如：我们调用 `this.setState()`的时候
  在这个函数中，我们之所以不调用 this.setState() 是因为该方法会触发另外一个 `componentWillUpdate()`,如果我们在
  `componentWillUpdate` 中触发更改状态，我们将以无限循环结束。

### 1109 说说你对react渲染原理的理解？
// Todo 暂时不知道该如何回答这个问题

### 1112 说说react中的Context吧,有什么应用场景？如何使用？
`Context` 提供了一个无需为每层组件手动添加 `props`，就能在组件树间进行数据传递的方法。我们在平时开发中如果不使用redux 这种数据状态管理库，可能在数据传递的时候使用的就是从祖先元素层层传递的方式，当层级较多之后，我们需要将数据通过组件的props接口层层传递，层级如果嵌套太深，总会容易出错。Context 就是为了解决这个问题而出现的。


17、知道浏览器缓存吗？
18、图片懒加载能手写一下吗？
19、函数节流与防抖
20、讲讲怎么用Promise
21、linux如何修改文件权限
22、有了解过webpack吗？能说说吗？
23、loader与plugin的区别？
24、谈谈你对MVVM开发模式的理解 MVVM分为Model、View、ViewModel三者。
25、.npmrc 是一个什么玩意，脚手架为什么需要这个东西？
26、.prettierrc 是一个什么东西，有什么作用。
27、babel.config.js 这个配置文件的作用是什么
28、postcss.config.js 这个文件是干什么用的
29、tsconfig.json 这个文件是干什么用的
30、tslint.json 这个文件是干什么用的
31、vue.config.js 这个文件是干什么用的
32、.browserslistrc 这个文件是干什么用的，有什么作用





