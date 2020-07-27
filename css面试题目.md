
### 什么是内联元素，什么是块元素？特点是什么？img是什么元素。如何将行内元素转换为块元素
**块级元素**：一个水平流上只能放置一个元素，多个块级元素换行显示，且元素高度、行高，内边距padding 外边距margin都是可以控制的,默认情况下,其宽度自动填满其父元素宽度。
**内联元素**：多个内联元素可以在同一行显示，且高度、行高 内边距、外边距不可控制，默认的高度和宽度是由内部的内容撑开的。
img 标签属于行内替换元素（replaced inline element）属于inline element.

>行内元素转换为块元素的方式是：设置disply属性 block table list-item 都是可以具有块级的特性，但是用的最多的是 block

### link 和 @import 的区别?
页面中使用css的方式主要有三种:
* 1、行内添加定义 style 属性值。
* 2、页面头部内嵌调用 (一般在html上面填写的 style 标签)
* 3、外面链接调用 只有两种 link 和 @import 

```html
<link rel="stylesheet" rev="stylesheet" href="CSS文件" type="text/css" media="all" />   

<style type="text/css" media="screen">   
@import url("CSS文件");   
</style>  
```

1、@import url（）机制是不同于link的，link是在加载页面时把css同时加载，而@import url（）则是读取完文件后在加载，所以会出现一开始没有css样式，闪烁一下出现样式后的页面(网速慢的情况下)。
2、@import 是css2里面的，所以古老的ie5不支持。
3、当使用javascript控制dom去改变样式的时候，只能使用link标签，因为@import不是dom可以控制的。
4、link是XHTML标签, 除了能加载css外,还能定义RSS，定义rel连接属性，@import只能加载css。

### 了解过什么是DHTML吗？
1、DHTML是Dynamic HTML的简称，就是动态的HTML(标准通用标记语言下的一个应用)，是相对传统的静态的html而言的一种制作网页的概念。
2、DHTML只是HTML、CSS和客户端脚本的一种集成，即一个页面中包括html+css+javascript(或其它客户端脚本)
3、html+css+javascript（或其他脚本）的优点：html确定页面框架，css和脚本决定页面样式、动态内容和动态定位。


## 1112、介绍一下标准的CSS的盒子模型？与低版本IE的盒子模型有什么不同的？
解答，分为两种盒模型：W3C盒模型（也称之为标准盒模型）IE盒模型；
+ 标准盒模型: 宽度 = 内容的宽度（content）+ border+ padding + margin
+ IE盒模型:  宽度 = 内容的宽度（content+ border + padding）+ margin

## 1112、box-sizing 属性是什么，简要的介绍一下。
解答：这个css 属性用来控制盒模型的解析模式，默认的模式是 content-box；
+ content-box: w3c 标准盒模型，设置元素的 height/width 属性指的是content部分的高/宽。
+ border-box: IE 盒模型，设置元素的height/width属性指的是border + padding + content部分的高/宽。

## 1112、css的选择器有哪些？你能说一下什么是伪类选择器，什么是伪元素选择器吗？他们的使用场景是什么？这些选择器的优先级怎么就计算？哪些属性是可以继承的？

  ### css 的选择器有：
  +id选择器(#app), 
  + 类选择器（.app）, 
  + 标签选择器(div,p,span) ,
  + 相邻选择器（h1+p）,
  + 子选择器 (ul > li)
  + 后代选择器 （li a）
  + 通配符选择器 （*）
  + 属性选择器（a[rel="external"]）
  + 伪类选择器（a:hover, li:nth-child）
  + 伪元素选择器 (a::before, a::after)


  伪类选择器和伪元素选择器的用处:
  > 主要用于标记一些特殊的样式，这些样式是基础选择器和组合选择器解决不了的，甚至js也做不到，因此需要这种选择器来满足一些特殊的需求。

  ### 伪类选择器：
  * 在页面中有些元素在不同的动作下有不同的样式，比如链接在没有点击的时候是一个样式，鼠标放上去有一个样式，点击完成还有另外的样式,这几种情况下，标签本身并没有变化，变化的标签对应的状态，这种时候就可以使用伪类来实现这个需求。在浏览器中，伪类的出现是为了向某些选择器添加特殊的效果或者限制，是在选择器后面添加伪类名称，比如我们希望一个链接的a标签在鼠标放上去有一个下划线:

  ```html
  a:{
    text-decoration: none;
  }
  a:hover{
    text-decoration: underline;
  }
  ```

  #### 伪类选择器一般分为两种用处：1、标记状态 2、筛选功能
  + 标记状态：
    + :link，选取未访问过的超链接元素 
    + :active，选取点中的元素
    + :visited 选取访问过的超链接元素 
    + :focus，选取获得焦点的元素
    + :hover，选取鼠标悬停的元素
  + 筛选功能：根据元素的特点或者索引来给特定的元素加上样式
    + :empty，选取没有子元素的元素。比如选择空的 span。
    + :checked，选取勾选状态的 input 元素， 只对 radio 和 checkbox 生效
    + :disabled，选取禁用的表单元素
    + :first-child，选取当前选择器下第一个元素
    + :last-child，和 first-child 相反，选取当前选择器下最后一个元素
    + :nth-child(an+b)，选取指定位置的元素。这个伪类是有参数的，参数可以支持 an+b 的形式，这里 a 和 b 都是可变的，n 从0起。使用这个伪类可以做到选择第几个，或者选择序号符合 an+b 的所有元素。比如使用 li:nth-child(2n+1)，就可以选中 li 元素中序号是2的整数倍加1的所有元素，也就是第1、3、5、7、9、2n+1个 li 元素。
    + :nth-last-child(an+b)，这个伪类和 nth-child 相似，只不过在计数的时候，这个伪类是从后往前计数。
    + :only-child，选取唯一子元素。如果一个元素的父元素只有它一个子元素，这个伪类就会生效。如果一个元素还有兄弟元素，这个伪类就不会对它生效。
    + :only-of-type，选取唯一的某个类型的元素。如果一个元素的父元素里只有它一个当前类型的元素，这个伪类就会生效。这个伪类允许父元素里有其他元素，只要不和自己一样就可以。

  ### 伪元素选择器
  
  伪元素选择器是用于向某些元素设置特殊效果。伪元素选择器选中的并不是真实的 DOM 元素，所以叫伪元素选择器。伪元素选择器常用的也就下面 5 个：

  + ::first-line，为某个元素的第一行文字使用样式。
  + ::first-letter，为某个元素中的文字的首字母或第一个字使用样式。
  + ::before，在某个元素之前插入一些内容。
  + ::after，在某个元素之后插入一些内容。
  + ::selection，对光标选中的元素添加样式。

  我们平时写的伪元素有时候使用（:）有时候使用（::）为什么两种形式都是可以的？

  > 在 CSS3 中，规定了伪类用一个冒号（:）表示，伪元素用两个冒号表示（::）。但除了 selection，其余四个伪元素选择器已经在 CSS2 中存在且和伪类用的是一样的单冒号表示的。为了向下兼容，现在的浏览器中伪元素选择器用单冒号和双冒号都可以。在没有兼容问题的情况下，还是建议大家按着新的 CSS3 标准来开发。

可继承的属性：font-size, font-family, color
不可继承的样式：border, padding, margin, width, height

## 1112、css优先级如何计算？
  可以使用权重的方式进行相加计算:
  + !important
  + 内联样式: （1000)
  + ID选择符：(100)
  + 类选择器/属性选择器/伪类选择器:（10）
  + 元素选择符/关系选择器/伪元素选择器：(1) 
  + 通配符选择器 （0000）
  1、!important声明的样式优先级高，如果冲突再进行计算
  2、如果优先级相同，则选择最后出现的样式
  3、继承得到的样式的优先级最低

## 1112、css3 新增的伪类有哪些？
  + 1、p:first-of-type 选择属于父元素的首个元素
  + 2、p:last-of-type 选择属于父元素的最后一个元素
  + 3、p:only-of-type 选择属于父元素的唯一个元素
  + 4、p:only-child 选择属于其父元素的唯一一个子元素
  + 5、p:nth-child(2) 选择属于其父元素的第二个子元素


## 1112、水平垂直居中的专题研究
  参考掘进专题文章: [CSS实现水平垂直居中的方法](https://juejin.im/post/5dd610c2f265da47ba2aa94e)

## 1112、display有哪些值，说说他们的作用？
+ display: inline (内联)
+ none -- 隐藏
+ block -- 块显示
+ table -- 表格显示
+ list-item -- 项目列表
+ inlin-block -- 内联块

## 1113、position的值？
+ static（默认）：按照正常文档流进行排列。
+ relative（相对定位）：不脱离文档流，参考自身静态位置通过 top bottom left right 定位
+ absolute（绝对定位）：参考距离最近一个不为 static 的父元素通过top，bottom，left，right 定位。
+ fixed （固定定位）：所固定的参照对象是可视窗口。

注意 absolute 这个的使用规则, fixed 是可视窗口

## 1113、css3 有哪些新的特性

+ 1、RGBA和透明度
+ 2、background-image、background-origin(content-box、padding-box、border-box) background-size、background-repeat
+ 3、word-wrap（对于长的不可分割的单词换行） word-wrap:break-word
+ 4、文字阴影：text-shadow：5px 5px 5px #FF0000 (水平阴影、垂直阴影、模糊距离、阴影颜色)
+ 5、font-size属性：定义自己的字体
+ 6、圆角（边框半径）：border-radius 属性用于创建圆角
+ 7、边框图片：border-image：url（border.png）30 30 round
+ 8、盒阴影：box-shadow： 10px 10px 5px #888888
+ 9、媒体查询：定义两套css 当浏览器的尺寸变化的时候采用不同的样式属性

## 1113、请解释一下css3 中的flexbox（弹性盒模型）以及使用场景
该布局模型的目的是提供一种更加高效的方式来对容器中的条目进行布局、对其和分配空间。在传统的布局方式中，block布局是把块在垂直方向上从上到下依次排列，而inline布局则是在水平方向来排列，弹性盒布局没有这样的方向限制，可以由开发人员自由操控。

使用场景：在移动端前端开发中使用非常广泛，在andriod 和 ios 也完美支持。

## 1118、你知道什么是BFC吗？（回答BFC是什么？BFC 触发的条件有哪些 BFC可以干什么）
BFC的全称是块级格式化上下文，是W3C规范中的一个概念，**它决定了元素如何对其内容进行定位以及与其他元素的关系和相互作用**，当涉及到可视化布局的时候 BFC提供了一个环境，HTML在这个环境中按照一定的规则进行布局，一个环境中的元素不会影响到其他环境中的布局，比如浮动元素会形成BFC,浮动元素的子元素主要受该浮动元素影响，两个浮动元素之间是互相不影响的。

这里的BFC类似于独立的行政单位的意思，BFC是一个作用范围，也可以理解为独立的容器。

## 1118、触发BFC的条件？
+ 根元素或者其它包含它的元素
+ 浮动元素 （元素的`float` 不是 `none`）
+ 绝对定位元素 （元素具有：position 为`absolute` 或者 `fixed`）
+ 行内块`inline-blocks`(元素的 `display: inline-block`)；
+ 表格单元格(元素的`display: table-cell`，HTML表格单元格默认属性)；
+ `overflow`的值不为`visible`的元素；
+ 弹性盒 flex boxes (元素的display: flex或inline-flex)；

## 1118、BFC的约束规则是什么？
+ 内部的盒会在垂直方向上一个接一个排列（可以看做BFC的一个常规流）
+ 处于同一个BFC中的元素相互影响可能会发生外边距重叠
+ 每一个元素的margin box 的左边，与容器块 border box 的左边相互接触（对于从左往右的格式化，否则相反）即使存在浮动也是如此
+ BFC就是页面上一个隔离的容器，容器里面的子元素不会影响到外面的元素，外面的也不会影响到里面的元素
+ 计算BFC的高度时考虑BFC所包含的所有元素，浮动的元素也参与计算
+ 浮动盒区域不叠加到BFC上

## 1118、如何在移动端实现1像素边框（说出为什么出现这个问题，常用的解决办法）


## 1126、为什么需要初始化样式？
因为浏览器的本身的兼容性问题，不同的浏览器对有些标签的默认值是不同的，造成了表现的不同，如果没有对CSS初始化往往会出现浏览器页面显示差异

## 1128、margin 和 padding 分别适合什么样的场景使用？
  何时使用margin：
  + 1、需要border外侧添加空白
  + 2、空白处不需要背景色
  + 3、上下连接的两个盒子之间的空白，需要相互抵消的时候。
  何时使用padding：
  + 1、需要在border内测添加空白
  + 2、空白处需要背景色
  + 3、上下连接的两个盒子空白，希望为两者之和

## 1129、元素竖向的百分比设定是相对于容器的高度吗？
  回答：当按照百分比设定一个元素的宽度时，它是相对于父容器的宽度计算的，但是，对于一些表示竖向距离的属性，例如padding-top
  padding-bottom margin-top margin-bottom 当按照百分比设定他们的时候，依据的也是父元素的宽度，而不是高度。

## 1129、全屏滚动的原理是什么？用到了哪些css属性？
  1、原理：有点类似轮播，整体的元素一直排列下去，假设有五个需要展示的元素，那么高度是500%只是展示100%剩下的可以通过transform 进行Y轴的定位，也可以通过margin-top实现
  2、overflow：hidden；transition： all 1000ms ease；

## 1129、什么是响应式设计？ 响应式设计的基本原理是什么？如何兼容低版本的IE？
  1、响应式网站的设计是一个网站能够兼容多个终端，而不是为每一个终端做一个特定的版本。基本的原理是通过媒体查询检测不同的设备屏幕尺寸做处理，页面头部必须有meta声明的viewport
  ```html
  <meta name=’viewport’ content=”width=device-width, initial-scale=1. maximum-scale=1,user-scalable=no”>
  ```

## Doctype的作用? 标准模式和兼容模式有什么区别？
  声明在文档的第一行，位于html的前面，用于告知浏览器的解析器以什么样的文档标准来解析这个文档，如果没有声明文档，就会以兼容模式呈现。
  + 标准模式：标准模式的排版和JS都是以浏览器支持的最高标准来运行。
  + 兼容模式：兼容模式页面以宽松向后兼容的方式显示，模仿老的浏览器，防止站点无法工作。

## 页面导入样式时候，使用link和@import有什么区别？
  link 是HTML的标签，不但可以引入 CSS 还可以引入 RSS，定义REL 属性等
  @import 是 css2.1提出的，只能引入 css 
  
  link在页面加载时同时加载，@import 要等到页面加载完成之后才加载

## css的加载会造成阻塞吗?
根据网上文章和本地的验证的代码演示可以看到。得到的的结论是:
css的加载不会阻碍dom树的解析，但是会阻塞dom树的渲染。

这种机制的评价:
这应该是浏览器的一种优化机制，因为你加载css时候，可能会修改下面dom节点的样式，如果css不阻塞dom树渲染的话，那么当css加载完之后，dom树
可能又得重新重绘或者回流了，这就造成了一部分的没有必要的性能损耗，所以我干脆就把DOM树的结构先解析完毕，把可以做的工作做完，然后等css加载完毕之后，根据最终的样式渲染DOM树，这种做法性能方面确实好一些。

结论: css加载不会阻塞DOM树解析,但是会阻塞dom树的渲染。

问题：css的加载会影响
位于css加载语句前的那个js代码先执行了，但是位于css加载语句后面的代码迟迟没有执行，直到css加载完成之后，它才执行，这也说明了
css加载会阻塞后面的js代码的执行。

结论:
  1、css的加载不会阻塞DOM树的解析
  2、css加载会阻塞DOM树的渲染
  3、css加载会阻塞后面js语句的执行。

为了避免用户长时间看到白屏，我们应该尽量的提高css的加载速度，比如可以使用以下几种方法：
  1、使用cdn(因为cdn会根据你的网络状况，替你挑选一个具有缓存内容的节点为你提供资源，因此可以减少加载时间)
  2、对css进行压缩处理，使用gulp 或者webpack。
  3、合理使用缓存 设置（catch-control,expires）但是一般需要给文件名添加hash码,子文件更新的时候，重新拉取新的资源。
  4、减少http请求，将多个css文件合并。

## 简单介绍下浏览器的渲染元素的过程:
1、不同的浏览器的渲染过程稍微有些不同，但是总结起来分为三个步骤:


## 页面加载的两个事件 onload 和 DOMContentLoaded 的区别
  1、onload: 就是等待页面的所有资源都加载完成才会触发，这些资源包括css、js、图片视频等。
  2、DOMContentLoaded: 

## 什么是rem？
  rem是一个相对的单位，1rem等于html元素上字体设置的大小，我们只要设置html上font-size的大小，就可以改变rem所代表的大小。
  换句话说假设，我们设置html节点字体大小是16px,那么1rem大小就是16px。

  我们使用rem有两个目的：
  * 1、rem单位所代表的尺寸大小和 *屏幕宽度* 成正比，也就是设置html元素的font-size和屏幕宽度成正比
  * 2、rem单位和px单位很容易进行换算，方便我们按照标注稿写css。

  移动端的标注的尺寸现在一般是750px的，这里的750px指的是设备的实际尺寸，也就是UI标注稿件的尺寸，我们编写的是CSS的尺寸，与设备无关的尺寸。屏幕的尺寸和css尺寸不是1:1的映射关系，而是取决于屏幕密度,比如iphoneX是3倍屏，iphone8是2倍屏，但是两个的屏幕css尺寸都是375px。而实际的设备尺寸，iphonex是1125px，iphone8是750px，我们编码过程中只需要设置css尺寸，设备会自动帮我们映射实际的尺寸。我们按照标注稿写完页面之后，页面应该是可以在其他所有尺寸设备上正常自适应地显示的。

  设置根节点字体大小有三种方式：
  1、通过媒体查询，通过不同屏幕尺寸设置字体大小。
  2、通过设置字体大小:
  `document.documentElement.style.fontSize = document.documentElement.clientWidth / 750 + 'px'`
  3、使用vw设置，vw也是一个相对单位，100vw等于屏幕宽度
  `html
    html{
      font-size: 10vw;
    } 
  ` 

  无论是第一种方式还是第二中方式设置的都是设备的css尺寸。对于iphonex还是iphone6、7、8都是375px

  从兼容性的角度来说,现在大多数的网站使用的是js的方式设置的，兼容性比较好。推荐使用。

## z-index是不是在所有的元素上都会生效呢?
  * 1、首先，z-index属性值并不是在任何元素上都有效果。它仅在定位元素（定义了position属性，且属性值为非static值的元素）上有效果。
  * 2、判断元素在Z轴上的堆叠顺序，不仅仅是直接比较两个元素的z-index值的大小，这个堆叠顺序实际由元素的层叠上下文、层叠等级共同决定。

## 请你说一下 块元素 和 行元素 是什么
  块元素 独占一行，默认填满父元素，可以设置宽度和高度 margin 和 padding 
  行元素 不会独占一行 width 和 height 会失效 并且在垂直方向上 padding 和 margin 会失效。

## 一些常见的属性
  * clientHeight 
  视野中可见的内容的内边距,如果X方向上有滚动条的话 是不包含 x 轴滚动条的高度、边框和外边距的。
  同一种型号的手机上是不变的，比如iPhone 6上就是667px。

  * offsetHeight || offsetWidth  boder + padding + content 
  这两个属性都是只读属性。
  视野中可见的内容的高度(clientHeight) 加上边框和滚动条的高度

  * scrollHeight
  所有的内容（指图一图中有文字的红色框框内）和内边距，这个内容包括肉眼看不见、溢出、被窗口遮挡的部分;

  * scrollTop
  滚动条滚动了多少距离（包括之前已滚动过的隐藏内容）就是scrollTop;

## 如何判断元素是否滚动到底部？
  如果滚动到底部，下面的等式返回true
  element.scrollHeight - element.scrollTop === element.clientHeight

## 什么是web worker? 了解过吗？ 在实际项目中是如何使用的？

## 假设有下面的DOM 结构,<parent><child>content</child></parent>下列哪些做法可以实现child内容垂直居中？

## 如何分类css的中标签:
回答: css 中的标签其实有很多，但是按照大类别分 可以分为“块级元素”和“内联元素”



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

### DOM（文件对象模型）,提供了树状结构的表示方法，以下描述正确的是：
DOM树中总共分为如下几种节点格式：Element类型（元素节点）、Text类型（文本节点）、Comment类型（注释节点）、Document类型（document节点）。
第一题：document可以说是一种节点格式，但节点树的根节点也叫document，所以第一题的说法太绝对，是错的。
第二题：所有的HTML elements（元素节点，其实就是HTML标签）都是element。
第三题：comments属于注释节点


### 水平垂直居中的方法：
常用的使用方法是三种:
垂直居中的方法，如果全写出来，有10多种。面试的时候一般都会说比较常用的几种。
* flex、
* position + transform、
* position + 负margin
是最常见的三种情况。
```html
<div class="outer">
  <div class="inner"></div>
</div>
```

方法一:
flex：容器设置属性就好, 项目不需要设置特别的属性
```css
.outer{
  display: flex;
  justify-content: center;
  align-items: center
}
```

方法二：`position + transform, inner`**宽高未知**
```css
.outer{
  position: relative;
}
.inner{
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%,-50%);
}
```
方法三: `position + 负margin, inner`**宽高已知**
```css
.outer{
  position: relative;
}
.inner{
  width: 100px;
  height: 100px;
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -50px;
  margin-top: -50px;
}
```

方法四: `position + margin auto`设置各个方向的距离都是0，再将 `margin`设为`auto`，也可以实现，前提是`inner宽高已知`
```css
.outer {
  position: relative;
}
.inner {
  width: 100px;
  height: 100px;
  position: absolute;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  margin: auto;
}
```

### 三栏布局
三栏布局是一种很常见的布局方式，两边固定，中间自适应。实现起来有很多种方法

第一种：flex: 两边的项目使用flex basis 宽度固定 中间部分占据全部剩余空间

```html
<div class="container">
  <div class="left">left</div>
  <div class="main">main</div>
  <div class="right">right</div>
</div>
```
```css
.container{
  display: flex;
}
.left{
  flex-basis:200px;
  background: green;
}
.main{
  flex: 1;
  background: red;
}
.right{
  flex-basis:200px;
  background: green;
}
```

第二种：position + margin 使用定位的方式，左右两栏使用绝对定位, 中间部分使用margin将两边撑开

```html
<div class="container">
  <div class="left">left</div>
  <div class="right">right</div>
  <div class="main">main</div>
</div>
```
```css
body,html{
  padding: 0;
  margin: 0;
}
.left,.right{
  position: absolute;
  top: 0;
  background: red;
}
.left{
  left: 0;
  width: 200px;
}
.right{
  right: 0;
  width: 200px;
}
.main{
  margin: 0 200px ;
  background: green;
}
```

第三种：float + margin 这种方式和 上面那一种类似 只不过使用的是 float 实现
```html
<div class="container">
  <div class="left">left</div>
  <div class="right">right</div>
  <div class="main">main</div>
</div>
```
```css
body,html{
  padding:0;
  margin: 0;
}
.left{
  float:left;
  width:200px;
  background:red;
}
.main{
  margin:0 200px;
  background: green;
}
.right{
  float:right;
  width:200px;
  background:red;
}
```

### CSS权重计算方式:

CSS基本选择器包含ID选择器、类选择器、标签选择器、通配符选择器

CSS基本选择器包含ID选择器、类选择器、标签选择器、通配符选择器。 正常情况下，一般都能答出!important > 行内样式 > ID选择器 > 类选择器 > 标签选择器 > 通配符选择器。

但如果这几种选择器同时作用于一个元素时，该元素最后应用哪个样式呢？这就涉及到权重计算的问题。 关于权重计算，有两种不同的计算方式：一种是以二进制的规则计算，一种是以1,10,100,1000这种的计算方式。我更倾向于二进制的这种方式。

各选择器权值：

* 内联样式，权值为1000
* ID选择器，权值为0100
* 类，伪类和属性选择器，权值为0010
* 标签选择器和伪元素选择器，权值为0001
* 通配符、子选择器、相邻选择器等，权值为0000
* 继承的样式没有权值



  














