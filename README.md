## 每日面试题集锦

每日的面试题目的分享意在提高大家对于 JavaScript 基础知识的把握。
每天的面试题目标题格式：

**#### 序号、 日期 考察的知识点：xxxx**

### JavaScript 基础

#### 1、 0516 考察的知识点：作用域、js 异步、事件循环。

观察下面代码看看输出什么内容：

```js
for (var i = 1; i <= 3; i++) {
  setTimeout(function() {
    console.log("ttt" + i);
  }, 0);
}
```

解答：3 个 ttt4
注：var 来声明变量 i，声明会提升到作用域的顶部，js 单线程，setTimeout 是异步匿名函数，for 循环结束才会执行 setTimeout 的异步回调，此时 i 等于 4

#### 2、 0517 考察的知识点： 闭包。

实践角度解释闭包：

> 1、即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）。
> 2、在代码中引用了自由变量。

闭包的优缺点：使用闭包主要是为了设计私有的方法和变量。闭包的优点是可以避免全局变量的污染，缺点是闭包会常驻内存，会增大内存使用量，使用不当很容易造成内存泄露。

```js
(1) var count = 10;
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
（2) var count = 10;
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

解答：1，2
注解：这里函数调用两次，第一次返回 2，闭包可以保存变量值，因此第二次调用时外层函数 count=2，最终输出结果是 3；

```js
（3）var count = 10;
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

注解： 闭包函数内部可以引用外部的参数和变量，此例 return 函数没有定义 count 依次往上找直至 Windows，找到就返回，找不到就返回 undefined,这里找到 count=10 最后打印的结构就是 11;

#### 3. 0520 考察的知识点：this 指向问题

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

解答：2 ，1
注：非箭头函数中 this 最终指向调用它的对象，第一个 this 指向 obj，输出 2；setTimeout 是全局函数，所以第二个 this 指向 window，输出 1
箭头函数没有自己的 this, 它的 this 是继承而来; 默认指向在定义它时所处的对象

#### 4. 0521 考察的知识点：this 指向问题、赋值语句

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

解答：

#### 5. 0522 考察知识点：数组的操作 hashMap 的映射

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:

```
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```


解答：

解法一：使用暴力破解法:

> 暴力破解的思路是使用双层 for 循环,外层循环控制第一个数字,内层循环控制第二个数字,当第一个数字和第二个数字之和满足目标数字的时候输出下标，话不多说，直接上代码。

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
        return [i, j];
      }
    }
  }
};
```

解答：https://zhidao.baidu.com/question/924556511528417059.html
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
};
```

#### 6、 0524 考察的知识点：es6 Set 和 Map。

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

Map 转为数组

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

<!--{"yes" => true, "no" => false}-->
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
求这两个数组的并集和交集和差集;
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

#### 8、0601 考察的知识点：var 声明变量的提升

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

#### 9、0602 请写一个阶乘函数 考察知识点：递归

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

#### 10、0605 如何实现一个 promise，promise 的原理

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

#### 11、0611 统计一个字符串出现最多的字母

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


#### 12、0612 1). 变量提升 2). 函数提升 3). 预处理 4). 调用顺序

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

#### 13、0613 vue 组件的通信方式:

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

#### 15、0615 摘自阮一峰老师的 es6 中 generator 的例子：

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

#### 16 0616 react 是如何划分组件的 （react 技术相关）

根据组件的职责通常把组件分为 UI 组件和容器组件。

UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

两者通过 React-Redux 提供 connect 方法联系起来。

#### 17 0617 请用 js 实现下 栈 这种数据结构(数据结构相关)

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

#### 18、0618 静态方法

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

#### 19. 0619 class 和 function 的区别

解答：

1. class 没有变量提升，必须先声明后使用
2. class 不能重复定义，会报语法错误
3. class 定义的类没有私有方法和私有属性
4. class 静态方法与静态属性，
5. 不能用 call apply bind 的方式 来改变他的执行上下文

#### 20 0620 继续异步专题的研究 EventLoop

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


#### 21 0621 继续异步专题的研究 EventLoop 考点 promise 的执行

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

#### 22 0622 继续异步专题的研究 EventLoop 考点：promise的进阶用法，对于then中return一个promise的掌握

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


#### 22 0622 继续异步专题的研究 EventLoop 考点 如果说这边的Promise中then返回一个Promise呢？
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

#### 23 0623 继续异步专题的研究 EventLoop 考点 如果说这边不止一个Promise呢，再加一个new Promise是否会影响结果?

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


#### 24 0624 继续异步专题的研究 EventLoop 考点:在async/await之下，对Eventloop的影响。

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

#### 25 0625 下面代码会输出什么？ 考点: this的指向：

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

#### 26 0626 写一个逆序字符串的函数 考点 js 原生字符串处理的掌握。
解答:
```js
function reverse(str) {
  let res = str.split('');
  return res.reverse().join('');
}
reverse('hello world!'); // output: '!dlrow olleh'
```

#### 27 0627 React使用中用过哪些状态管理工具，紧接着问了redux和mobx的区别
1、Redux鼓励一个应用之中只有一个store，Mobx鼓励实现多个store
2、Redux是函数式编程，而Mobox的模式是面向对象的。
3、Redux鼓励数据的规范化，减少冗余。Mobx 容许数据冗余，但同样能保持数据一致
4、5.Redux更严格，必须调用reducer触发action来改变state， Mobx 最初的一个卖点就是直接修改数据，但是实践中大家还是发现这样无组织无纪律不好，所以后来 Mobx 还是提供了 action 的概念。和 Redux 的 action 有点不同，Mobx 中的 action 其实就是一个函数，不需要做 dispatch。如果想强制要求使用 action，禁止直接修改 observable 数据，使用 Mobx 的 configure
```js
import {configure} from 'mobx';
configure({enforceActions: true});
```

#### 28 0628 call的模拟实现 考点 对于原生js的熟练程度 基本面试必考
解答：主要参考了冴羽老师的博客：[call和apply的模拟实现](https://github.com/mqyqingfeng/Blog/issues/11)
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

#### 29 0629 apply的模拟实现 考点 对于原生js的熟练程度
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

#### 30 0630 bind模拟实现 考点 对于原生js的熟练程度
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

#### 31 0701 页面上有个空的无序列表节点，用 `<ul></ul>` 表示，要往列表中插入3 个`<li>`，每个列表项的文本内容是列表项的插入顺序，取值 1, 2, 3，怎么用原生的 JS 实现这个需求？同时约定，为方便获取节点引用，可以根据需要为 `<ul>` 节点加上 id 或者 class 属性（考点 原生dom的操作）

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

#### 32 0702 接着昨天的问题,如果我们想要弹出来每一个li里面的内容应该如何做呢？
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


#### 33 0703 实现一个防抖函数？ (循序渐进，提供思路和版本)

解答：防抖函数，实际上是一个接收一个函数，一个等待时间的函数，无论你怎样的频繁的操作我等到你不操作的时间开始算起n秒后执行
第一个版本：
```js
function debounce(func,wait){
  var timeout;
  return function () {
    clearTimeout(timeout)
    timeout = setTimeout(func, wait);
  }
}
```
#### 34 实现一个防抖函数？ (循序渐进，提供思路和版本)
第二个版本：上述的第一个版本中this指向已经变了为了能够保留执行时候的this指向。我们来看看第二个版本
```js
function debounce(func,wait){
  var timeout;
  return function(){
    var context = this;
    clearTimeout(timeout)
    timeout = setTimeout(function(){
      func.apply(context)
    },wait)
  }
}
```

#### 35 0705 实现一个防抖函数？ (循序渐进，提供思路和版本)
第三个版本：上面的一个版本实际上还是不够完美的。因为丢失了event对象 这一个版本我们添加一个event对象
```js
function debounce(func,wait){
  var timeout;

  return function(){
    var context = this,
    args = arguments;
    clearTimeout(timeout)
    timeout = setTimeout(function(){
      func.apply(context,args)
    },wait)
  }
}
```

#### 36 0706 说一下new操作符都做了哪些事情 考点：js面向对象系统
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

#### 37 0707 遍历DOM树 （递归、树形结构、原生dom操作）
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

#### 38 0708 ['1', '2', '3'].map(parseInt) what & why ? 考察对于数组的map方法的掌握程度

解答：输出的结果是  [1, NaN, NaN];

首先map的基本的用法要非常的熟悉：(MDN定义)
 > map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

首先看一个我们平时使用map的场景
```js
var array1 = [1, 4, 9, 16];
// pass a function to map
const map1 = array1.map(x => x * 2);
console.log(map1);
// expected output: Array [2, 8, 18, 32]
```

map 函数的一个参数是一个callback回调函数,map会保证数组中的每一个元素都进过这个回调函数的处理，然后返回一个新的数组，map不会对原有的数组进行修改。

上述的题目答案为什么是 [1, NaN, NaN] 呢？ 这还得从这个回调函数callback说起。
这个callback 接收三个参数：

```js
  currentValue：callback 数组中正在处理的当前元素。
  index: callback 数组中正在处理的当前元素的索引。
  array: map 方法被调用的数组。
```

上述这个题目中 我们将map的回调函数设置为parseInt，而parseInt则是用来解析字符串的，使字符串成为指定基数的整数。

>parseInt(string, radix) 接收两个参数，第一个表示被处理的值（字符串），第二个表示为解析时的基数。

了解这两个函数后，我们可以模拟一下运行情况:

```js
  1、parseInt('1', 0) //radix为0时，且string参数不以“0x”和“0”开头时，按照10为基数处理。这个时候返回1
  2、parseInt('2', 1) //基数为1（1进制）表示的数中，最大值小于2，所以无法解析，返回NaN
  3、parseInt('3', 2) //基数为2（2进制）表示的数中，最大值小于3，所以无法解析，返回NaN
```

#### 39 0709 考查数组的reduce()方法
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

  reduce为数组中的每一个元素依次执行回调函数，不包括数组中被删除或未被赋值的元素。
    
  callback接受四个参数：
```js
  Accumulator (acc) 初始值（或上一次回调函数的返回值）
  Current Value (cur) 当前元素值
  Current Index (idx) 当前索引
  Source Array (src) 调用reduced的数组
```
说明：未设置初始值index从1开始;

    函数没有返回值，则默认返回undefined。

#### 40 0710 说说Redux和Vuex的设计思想:
1、软件设计领域有很多通用的思想，比如隔离变化，约定优于配置

解答:[Vuex、Flux、Redux、Redux-saga、Dva、MobX](https://zhuanlan.zhihu.com/p/53599723) 这个链接文章非常值得一看

#### 41 0711 什么是 HTTPS 与 HTTP 的区别是什么？ 考察 http的网络知识

解析：要理解HTTPS 首先需要明白http的缺点是什么，知道背景之后再理解记忆https就比较简单了。

HTTP主要有这些不足，例举如下:
  >1.通信使用明文（不加密），内容可能会被窃听 http 本身并不具备加密的功能。
  2.不验证通信方的身份，因此有可能遭遇伪装
  3.无法证明报文的完整性，所以有可能已遭篡改

HTTPS（全称：Hyper Text Transfer Protocol over Secure Socket Layer），是以安全为目标的HTTP通道，简单讲是HTTP的安全版。即HTTP下加入SSL层，HTTPS的安全基础是SSL，因此加密的详细内容就需要SSL。 现在它被广泛用于万维网上安全敏感的通讯，例如交易支付方面。

HTTP 与 HTTPS 的区别：
  >1.HTTP 是明文传输，HTTPS 通过 SSL\TLS 进行了加密
  2.HTTP 的端口号是 80，HTTPS 是 443
  3.HTTPS 需要到 CA 申请证书，一般免费证书很少，需要交费
  4.HTTP 的连接很简单，是无状态的；HTTPS 协议是由 SSL+HTTP 协议构建的可进行加密传输、身份认证的网络协议，比 HTTP 协议安全。
  5、传输速度方面 HTTPS由于中间的SSL链接会消耗一部分性能问题。

#### 42 0712 什么是HTTP三次握手？为什么是三次而不是两次，具体的过程是怎样的  考察 http的网络知识

TCP为了保证将数据准确无误的送达到目标处，采用了三次握手的策略,同时为了验证客户端和服务端的发送和接收的功能是正常的。

主要过程可以概括为下面四句话：
>1.第一次握手：客户端给服务器发送一个 SYN 报文。
2.第二次握手：服务器收到 SYN(synchronize) 报文之后，会应答一个 SYN+ACK 报文。
3.第三次握手：客户端收到 SYN+ACK 报文之后，会回应一个 ACK(acknowledgement) 报文。
4.服务器收到 ACK 报文之后，三次握手建立完成。

那我们解释一下为什么是三次而不是两次呢？
>1.第一次握手：客户端发送网络包，服务端收到了。这样服务端就能得出结论：客户端的发送能力、服务端的接收能力是正常的。
2.第二次握手：服务端发包，客户端收到了。这样客户端就能得出结论：服务端的接收、发送能力，客户端的接收、发送能力是正常的。不过此时服务器并不能确认客户端的接收能力是否正常。
3.第三次握手：客户端发包，服务端收到了。这样服务端就能得出结论：客户端的接收、发送能力正常，服务器自己的发送、接收能力也正常。

需要三次握手才能确认双方的接收与发送能力是否正常。

如果中间的链接中断，TCP协议会再次以相同的顺序发送相同的数据包。

将这个过程详细一点描述：

刚开始客户端处于 closed 的状态，服务端处于 listen 状态。然后
>1.第一次握手：客户端给服务端发一个 SYN 报文，并指明客户端的初始化序列号 ISN(c)。此时客户端处于 SYN_Send 状态。
2.第二次握手：服务器收到客户端的 SYN 报文之后，会以自己的 SYN 报文作为应答，并且也是指定了自己的初始化序列号 ISN(s)，同时会把客户端的 ISN + 1 作为 ACK 的值，表示自己已经收到了客户端的 SYN，此时服务器处于 SYN_RECV 的状态。
3.第三次握手：客户端收到 SYN 报文之后，会发送一个 ACK 报文，当然，也是一样把服务器的 ISN + 1 作为 ACK 的值，表示已经收到了服务端的 SYN 报文，此时客户端处于 establised 状态。
4.服务器收到 ACK 报文之后，也处于 establised 状态，此时，双方以建立起了链接。

我们看到有两个中间状态，syn_sent和syn_recv，这两个状态叫着「半打开」状态，就是向对方招手了，但是还没来得及看到对方的点头微笑。syn_sent是主动打开方的「半打开」状态，syn_recv是被动打开方的「半打开」状态。客户端是主动打开方，服务器是被动打开方。


#### 43 0713 三次握手的作用？对上面一个题目的补充:
解答：

>1.确认双方的接受能力、发送能力是否正常。
2.指定自己的初始化序列号，为后面的可靠传送做准备。
3.如果是 https 协议的话，三次握手这个过程，还会进行数字证书的验证以及加密密钥的验证等等。

#### 44 0714 三次握手其他问题的补充 

1.（ISN）是固定的吗 也就是初始化的序列号是固定的吗？
三次握手的一个重要功能是客户端和服务端交换ISN(Initial Sequence Number), 以便让对方知道接下来接收数据的时候如何按序列号组装数据。

如果ISN是固定的，攻击者很容易猜出后续的确认号，因此 ISN 是动态生成的。

2.什么是半连接队列?
服务器第一次收到客户端的 SYN 之后，就会处于 SYN_RECV 状态，此时双方还没有完全建立其连接，服务器会把此种状态下请求连接放在一个队列里，我们把这种队列称之为半连接队列。当然还有一个全连接队列，就是已经完成三次握手，建立起连接的就会放在全连接队列中。如果队列满了就有可能会出现丢包现象。

3.三次握手过程中可以携带数据吗?
很多人可能会认为三次握手都不能携带数据，其实第三次握手的时候，是可以携带数据的。也就是说，第一次、第二次握手不可以携带数据，而第三次握手是可以携带数据的。

假如第一次握手可以携带数据的话，如果有人要恶意攻击服务器，那他每次都在第一次握手中的 SYN 报文中放入大量的数据，因为攻击者根本就不理服务器的接收、发送能力是否正常，然后疯狂着重复发 SYN 报文的话，这会让服务器花费很多时间、内存空间来接收这些报文。也就是说，第一次握手可以放数据的话，其中一个简单的原因就是会让服务器更加容易受到攻击了。

而对于第三次的话，此时客户端已经处于 established 状态，也就是说，对于客户端来说，他已经建立起连接了，并且也已经知道服务器的接收、发送能力是正常的了，所以能携带数据页没啥毛病。








