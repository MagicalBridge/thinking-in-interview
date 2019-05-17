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

