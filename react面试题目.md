### 0616 react 是如何划分组件的 （react 技术相关）

根据组件的职责通常把组件分为 UI 组件和容器组件。

UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。

两者通过 React-Redux 提供 connect 方法联系起来。

### 0627 React使用中用过哪些状态管理工具，紧接着问了redux和mobx的区别
1、Redux鼓励一个应用之中只有一个store，Mobx鼓励实现多个store
2、Redux是函数式编程，而Mobox的模式是面向对象的。
3、Redux鼓励数据的规范化，减少冗余。Mobx 容许数据冗余，但同样能保持数据一致
4、5.Redux更严格，必须调用reducer触发action来改变state， Mobx 最初的一个卖点就是直接修改数据，但是实践中大家还是发现这样无组织无纪律不好，所以后来 Mobx 还是提供了 action 的概念。和 Redux 的 action 有点不同，Mobx 中的 action 其实就是一个函数，不需要做 dispatch。如果想强制要求使用 action，禁止直接修改 observable 数据，使用 Mobx 的 configure
```js
import {configure} from 'mobx';
configure({enforceActions: true});
```

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


### React的核心思想
内存中维护一颗虚拟DOM树，数据变化时（setState），自动更新虚拟 DOM，得到一颗新树，然后 Diff 新老虚拟 DOM 树，找到有变化的部分，得到一个 Change(Patch)，将这个 Patch 加入队列，最终批量更新这些 Patch 到 DOM 中。