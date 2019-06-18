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
注：var 来声明变量 I，声明会提升到作用域的顶部，js 单线程，setTimeout 是异步匿名函数，for 循环结束才会执行 setTimeout 的异步回调，此时 I 等于 4

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
  if (str.length == 1) {
    return str;
  }
  let charObj = {};
  for (let i = 0; i < str.length; i++) {
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
  for (var i = 0; i < 100; i++) {
    i == 99 && resolve();
  }
  console.log(3);
}).then(function() {
  console.log(4);
});
console.log(5);
```

输出：2 3 5 4 1
解答：then 和 settimeout 执行顺序，即 setTimeout(fn, 0)在下一轮“事件循环”开始时执行，Promise.then()在本轮“事件循环”结束时执行。因此 then 函数先输出，settimeout 后输出。
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

```js
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2");
}
console.log("script start");
setTimeout(function() {
  console.log("setTimeout");
}, 0);
async1();
new Promise(function(resolve) {
  console.log("promise1");
  resolve();
}).then(function() {
  console.log("promise2");
});
console.log("script end");
```

解答：使用事件循环机制分析:

1.首先执行同步代码，console.log( 'script start' ) 2.遇到 setTimeout,会被推入宏任务队列 3.执行 async1(), 它也是同步的，只是返回值是 Promise，在内部首先执行 console.log( 'async1 start' ) 4.然后执行 async2(), 然后会打印 console.log( 'async2' ) 5.从右到左会执行, 当遇到 await 的时候，阻塞后面的代码，去外部执行同步代码 6.进入 new Promise,打印 console.log( 'promise1' ) 7.将.then 放入事件循环的微任务队列 8.继续执行，打印 console.log( 'script end' ) 9.外部同步代码执行完毕，接着回到 async1()内部, 由于 async2()其实是返回一个 Promise, await async2()相当于获取它的值，其实就相当于这段代码 Promise.resolve(undefined).then((undefined) => {}),所以.then 会被推入微任务队列, 所以现在微任务队列会有两个任务。接下来处理微任务队列，打印 console.log( 'promise2' )，后面一个.then 不会有任何打印，但是会执行 10.执行后面的代码, 打印 console.log( 'async1 end' ) 11.进入第二次事件循环，执行宏任务队列, 打印 console.log( 'setTimeout' )

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
上面代码中，第二次运行next方法的时候不带参数，导致 y 的值等于2 * undefined（即NaN），除以 3 以后还是NaN，因此返回对象的value属性也等于NaN。第三次运行Next方法的时候不带参数，所以z等于undefined，返回对象的value属性等于5 + NaN + undefined，即NaN。

如果向next方法提供参数，返回结果就完全不一样了。上面代码第一次调用b的next方法时，返回x+1的值6；第二次调用next方法，将上一次yield表达式的值设为12，因此y等于24，返回y / 3的值8；第三次调用next方法，将上一次yield表达式的值设为13，因此z等于13，这时x等于5，y等于24，所以return语句的值等于42。

注意，由于next方法的参数表示上一个yield表达式的返回值，所以在第一次使用next方法时，传递参数是无效的。V8 引擎直接忽略第一次使用next方法时的参数，只有从第二次使用next方法开始，参数才是有效的。从语义上讲，第一个next方法用来启动遍历器对象，所以不用带有参数。


#### 16 0616 react 是如何划分组件的 （react 技术相关）

根据组件的职责通常把组件分为UI组件和容器组件。

UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

两者通过React-Redux 提供connect方法联系起来。

#### 17 0617 请用js实现下 栈 这种数据结构(数据结构相关)

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
  return this.items.length
}

// 检查栈是否为空
Stack.prototype.isEmpty = function() {
  return this.items.length === 0
}

// 清空栈内元素
Stack.prototype.clear = function() {
  this.items = []
}
```
#### 18、0618 静态方法
```js
  class test {
    static colorChange(newColor) {
      this.newColor = newColor
      return this.newColor
    }

  constructor({ newColor = 'green' } = {}) {
    this.newColor = newColor
  }
}

const newTest = new test({ newColor: 'purple' });
newTest.colorChange('orange');

- A: orange
- B: purple
- C: green
- D: TypeError

```
解答：D
test 是一个静态方法。静态方法被设计为只能被创建它们的构造器使用（也就是test),并且不能传递给实例。因为 newTest 是一个实例，静态方法不能被实例使用，因此抛出了 TypeError 错误.


