## 1112、介绍一下标准的CSS的盒子模型？与低版本IE的盒子模型有什么不同的？
解答，分为两种盒模型：W3C盒模型（也称之为标准盒模型）IE盒模型；
+ 标准盒模型: 宽度 = 内容的宽度（content）+ border+ padding + margin
+ IE盒模型:  宽度 = 内容的宽度（content+ border + padding）+ margin

## 1112、box-sizing 属性是什么，简要的介绍一下。
解答：这个css 属性用来控制盒模型的解析模式，默认的模式是 content-box；
+ content-box: w3c 标准盒模型，设置元素的 height/width 属性指的是content部分的高/宽。
+ border-box: IE 盒模型，设置元素的height/width属性指的是border + padding + content部分的高/宽。

## 1112、css的选择器有哪些？哪些属性是可以继承的？
css 的选择器有：
  + id选择器(#app), 
  + 类选择器（.app）, 
  + 标签选择器(div,p,span) ,
  + 相邻选择器（h1+p）,
  + 子选择器 (ul > li)
  + 后代选择器 （li a）
  + 通配符选择器 （*）
  + 属性选择器（a[rel="external"]）
  + 伪类选择器（a:hover, li:nth-child）

可继承的属性：font-size, font-family, color
不可继承的样式：border, padding, margin, width, height
优先级（就近原则）：!important > [ id > class > tag ]
!important 比内联优先级高

## 1112、css优先级如何计算？
  可以使用权重的方式进行相加计算:
  + 元素选择符： 1
  + class选择符： 10
  + id选择符：100
  + 元素标签：1000
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
 // todo 

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

