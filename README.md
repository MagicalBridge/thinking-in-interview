## 每日面试题集锦 



#### 1、 0516  考察的知识点：作用域、js异步、事件循环。

观察下面代码看看输出什么内容：

```js
for (var i = 1; i <= 3; i++) {
  setTimeout(function() {
    console.log("ttt" + i);
  }, 0);
}
```

解答：

#### 2、 0517  考察的知识点： 闭包。

闭包的优缺点：使用闭包主要是为了设计私有的方法和变量。闭包的优点是可以避免全局变量的污染，缺点是闭包会常驻内存，会增大内存使用量，使用不当很容易造成内存泄露

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
解答：2
注： return 函数可以引用外部函数add的变量count,因此内层函数count = 1,最终输出为2；
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
  解答：1，2
  注解：这里函数调用两次，第一次返回2，闭包可以保存变量值，因此第二次调用时外层函数count=2，最终输出结果是3；
（3）var count = 10;
    function add() {
      return function () {
        count += 1;
        console.log(count);
      }
    }
    var s = add();
    s();
  解答：11
  注解： 闭包函数内部可以引用外部的参数和变量，此例return 函数没有定义count 依次往上找直至Windows，找到就返回，找不到就返回undefined,这里找到 count=10 最后打印的结构就是11;
  

