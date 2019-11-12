## 1112 介绍一下标准的CSS的盒子模型？与低版本IE的盒子模型有什么不同的？
解答，分为两种盒模型：W3C盒模型（也称之为标准盒模型）IE盒模型；
+ 标准盒模型: 宽度 = 内容的宽度（content）+ border+ padding + margin
+ IE盒模型:  宽度 = 内容的宽度（content+ border + padding）+ margin

## 1112 box-sizing 属性是什么，简要的介绍一下。
解答：这个css 属性用来控制盒模型的解析模式，默认的模式是 content-box；
+ content-box: w3c 标准盒模型，设置元素的 height/width 属性指的是content部分的高/宽。
+ border-box: IE 盒模型，设置元素的height/width属性指的是border + padding + content部分的高/宽。

## 1112 css的选择器有哪些？哪些属性是可以继承的？
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

## 1112 css优先级如何计算？
  可以使用权重的方式进行相加计算:
  + 元素选择符： 1
  + class选择符： 10
  + id选择符：100
  + 元素标签：1000