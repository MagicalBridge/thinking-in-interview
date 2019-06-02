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

1.基本用法

创建 Set `new Set()`

值得注意的是,NaN 被 Set 认为是相同的,{}被认为是不同的，在向 Set 加入值时，Set 不会转换数据类型，内部在判断元素是否存在时用的类似于精确等于(===)的方法，“2”和 2 是不同的，NaN 等于其自身。

```js
var set = new Set([{}, {}]);
set; //{{},{}}
set.size; // 2  Set.size 返回Set实例的成员总数

var set = new Set([NaN, NaN]);
set; //{NaN}
set.size; // 1
```

向 Set 中添加元素。

```js
let set1 = new Set();
set1.add(1);
set1.add(2);
set1.add(3);
console.log("added:", set1); //  added: Set { 1, 2, 3 }
```

从 Set 中删除元素。

```js
let set1 = new Set();
set1.add(1);
set1.add(2);
set1.add(3);
set1.delete(1); // 返回 true
set1.delete(4); // 返回 false
console.log("deleted:", set1);
// deleted: Set { 2, 3 }
```

判断某元素是否存在。

```js
let set1 = new Set()
set1.add(1)
set1.add(2)
set1.add(3)
set1.delete(1)
console.log('has(1):', set1.has(1))
console.log('has(2):', set1.has(2))
<!--has(1): false-->
<!--has(2): true-->
```

清除所有元素。

```js
let set1 = new Set()
set1.add(1)
set1.add(2)
set1.add(3)
set1.clear()
console.log('cleared:', set1)
<!--cleared: Set {}-->
```

2.Set 和 Array 互转

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

3.遍历方法
Set.keys() : 返回键名的遍历器
Set.values()：返回键值的遍历器
Set.entries(): 返回键值对的遍历器
Set.forEach() :回调函数遍历
forEach()方法遍历
可以使用 forEach 方法来遍历 Set 中的数据项，该方法传入一个回调函数 callback，还可以传入一个 this，用于回调函数之中：

回调函数 callback 中有三个参数：元素值；元素索引；将要遍历的对象；

```js
let set = new Set([1, 2, 3, 3, 3, 3]);
set.forEach(function(value, key, ownerSet) {
  console.log(value);
  console.log(key);
});
```

Set 中的 value 和 key 是相同的，这是为了让 Set 的 forEach 方法和数组以及 Map 的 forEach 方法保持一致，都具有三个参数。

数据结构 map 基本概念
ES6 新增了 Map 数据结构，Map 对象保存键值对，任何值（原始值或对象）都可以作为一个键或一个值。 1.基本用法

set(key,value):set 方法设置键名 key 对应的键值为 value，然后返回整个 Map 结构。

```js
let map = new Map();
map.set('dsssddddddddsdsdsdssfw',8);
map.set({a:1},'ddsdsds');

get(key):获取 key 的值
map.get('dsssddddddddsdsdsdssfw')//8
map.get('x')//undefined
```

has(key):has 方法返回一个布尔值，表示某个键是否在当前 Map 对象之中。
map.has('dsssddddddddsdsdsdssfw'); //true

delete(key):delete 方法删除某个键，返回 true。如果删除失败，返回 false。

```js
map.delete("dsssddddddddsdsdsdssfw");
map.has("dsssddddddddsdsdsdssfw"); //false
```

```js
clear():清空 Map 对象
map.size // 2
map.clear()
map.size // 0
```

2.遍历方法

```js
const map = new Map([["F", "no"], ["T", "yes"]]);

for (let key of map.keys()) {
  console.log(key);
}
("F");
("T");

for (let value of map.values()) {
  console.log(value);
}
// "no"
// "yes"

for (let item of map.entries()) {
  console.log(item[0], item[1]);
}
// "F" "no"
// "T" "yes"

map.forEach(function(key, value, map) {
  console.log(key + ":" + value);
});
// "F":"no"
// "T":"yes"
```

3.与其他数据结构的互相转换
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
