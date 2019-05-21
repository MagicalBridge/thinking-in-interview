## 每日面试题集锦
每日的面试题目的分享意在提高大家对于JavaScript基础知识的把握。
每天的面试题目标题格式：

**#### 序号、 日期  考察的知识点：xxxx**


#### 1、 0516 考察的知识点：作用域、js 异步、事件循环。

观察下面代码看看输出什么内容：

```js
for (var i = 1; i <= 3; i++) {
  setTimeout(function() {
    console.log("ttt" + i);  
  }, 0);
}
```

解答：3个ttt4
注：var来声明变量I，声明会提升到作用域的顶部，js单线程，setTimeout是异步匿名函数，for循环结束才会执行setTimeout的异步回调，此时I等于4

#### 2、 0517 考察的知识点： 闭包。

实践角度解释闭包：

  >1、即使创建它的上下文已经销毁，它仍然存在（比如，内部函数从父函数中返回）。
  2、在代码中引用了自由变量。

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
注： return 函数可以引用外部函数（父函数）add的变量 count, 因此内层函数 count = 1,最终输出为 2；

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
注：非箭头函数中this最终指向调用它的对象，第一个this指向obj，输出2；setTimeout是全局函数，所以第二个this指向window，输出1
箭头函数没有自己的this, 它的this是继承而来; 默认指向在定义它时所处的对象

#### 4. 0521 考察的知识点：this 指向问题、赋值语句
```js
var num = 10;
var obj = {
    num:8,
    inner: {
        num: 6,
        print: function () {
            console.log(this.num);
        }
    }
}
num = 888;
obj.inner.print(); 
var fn = obj.inner.print;
fn(); 
(obj.inner.print)(); 
(obj.inner.print = obj.inner.print)(); 
```
解答：