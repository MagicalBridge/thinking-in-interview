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

解答：



