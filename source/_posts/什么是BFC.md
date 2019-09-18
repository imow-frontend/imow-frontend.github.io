---
title: 什么是BFC
date: 2019-09-18 09:48:56
tags: css
author: 刘涛
---


## 前言
对于BFC的概念以及应用场景一直都不是很明白，今天着重去了解了一下，做了以下总结。

## 什么是BFC
BFC（Block Formatting Context）直译为“块级格式化范围”。是 W3C CSS 2.1 规范中的一个概念，它决定了元素如何对其内容进行定位，以及与其他元素的关系和相互作用当涉及到可视化布局的时候，Block Formatting Context提供了一个环境，HTML元素在这个环境中按照一定规则进行布局。一个环境中的元素不会影响到其它环境中的布局。比如浮动元素会形成BFC，浮动元素内部子元素的主要受该浮动元素影响，两个浮动元素之间是互不影响的。这里有点类似一个BFC就是一个独立的行政单位的意思。也可以说BFC就是一个作用范围。可以把它理解成是一个独立的容器，并且这个容器的里box的布局，与这个容器外的毫不相干。



## 触发BFC
1. float的值不能为none
2. overflow的值不能为visible
3. display的值为table-cell, table-caption, inline-block中的任何一个
4. position的值不为relative和static 


## BFC的约束条件
1. 内部的Box会在垂直方向上一个接一个的放置
2. 垂直方向的距离有margin决定(属于同一个BFC的两个相邻Box的margin会发生重叠，与方向无关)
3. 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此
4. BFC的区域不会与float的元素区域重叠
5. 计算BFC的高度时，浮动子元素也参与计算
6. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面元素，反之亦然


## BFC的特性
### 特性1：BFC会阻止垂直外边距折叠
  **按照BFC的定义，只有同属于一个BFC时，两个元素才有可能发生垂直margin的重叠**，这个包括相邻元素或者嵌套元素，只要他们之间没有阻挡（**比如边框、非空内容、padding等**）就会发生margin重叠。

  #### ①相邻兄弟元素margin重叠问题
  ```css
  <style>
  p{
     color: #fff;
     background: #888;
     width: 200px;
     line-height: 100px;
     text-align:center;
     margin: 100px;
    }
  </style>
<body>
    <p>ABC</p>
    <p>abc</p>
</body>
  ```
  ![相邻兄弟元素margin重叠问题](https://user-gold-cdn.xitu.io/2018/10/14/16672a66f5c5d5bf?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

  上面例中两个P元素之间距离本该为200px,然而实际上只有100px,发生了margin重叠。遇到这种情形，我们如何处理？**只需要在p外面包裹一层容器，并触发该容器生成一个BFC。那么两个P便不属于同一个BFC，就不会发生margin重叠了。**

  **解决方案**
   ```css
  <style>
  p{
     color: #fff;
     background: #888;
     width: 200px;
     line-height: 100px;
     text-align:center;
     margin: 100px;
    }
  .wrap{
    overflow:hidden;
  }
  </style>
<body>
   <p>ABC</p>
  <div class="wrap">
    <p>abc</p>
  </div>
</body>
  ```
  ![相邻兄弟元素margin重叠问题的解决方案](https://user-gold-cdn.xitu.io/2018/10/14/16672a66f5a0b8c8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

  #### ②父子元素margin重叠问题
   ```css
    <style>
     .box{
        width:100px;
        height:100px;
        background:#ccc;
        }
     .wrap {
       background:yellow;
       }
     .wrap h1{
        background:pink;
        margin:40px;
        }
    </style>
<body>
<div class="box">box</div>
<div class="wrap">
  <h1>h1</h1>
</div>
</body>
  ```
  ![父子元素margin重叠问题](https://user-gold-cdn.xitu.io/2018/10/14/16672a66f9541efe?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

  **解决方案**
   上图wrap元素与h1元素之间l理论上本该有个40px的上下margin值,然而实际上父子元素并没有存在margin值，与此同时，两个div元素的间距为40px。遇到这种情形，我们如何处理？处理方法其实有很多，**在wrap元素中添加:overflow:hidden;或者overflow：auto；使其父元素形成一个BFC；也可以在wrap元素中添加border：1px solid；或是padding：1px；**这些都可以有效解决父子元素margin重叠问题。
![父子元素margin重叠问题解决方案](https://user-gold-cdn.xitu.io/2018/10/14/16672a66f987a8e6?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)


### 特性2：BFC不会重叠浮动元素
如果一个浮动元素后面跟着一个非浮动的元素，那么就会产生一个覆盖的现象，很多自适应的两栏布局就是这么做的。比如下图的效果，参考例子

``` css
<style>
.box1{
  height: 100px;
  width: 100px;
  float: left;
  background: lightblue;
}
.box2{width: 200px;
  height: 200px;
  background: #eee;
}
</style>
<body>
<div class="box1">我是一个左浮动的元素</div>
<div class="box2">喂喂喂!大家不要生气嘛，生气会犯嗔戒的。悟空你也太调皮了，
我跟你说过叫你不要乱扔东西，你怎么又……你看，我还没说完你就把棍子给扔掉了!
月光宝盒是宝物，你把它扔掉会污染环境，要是砸到小朋友怎么办，就算砸不到小朋友，
砸到花花草草也是不对的。</div>
</body>
```
![BFC不会重叠浮动元素1](https://user-gold-cdn.xitu.io/2018/10/14/16672a66f9ada21f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

上图中，文字围绕着浮动元素排列，不过在这里，这显然不是我们想要的。此时我们可以**为.box2元素的样式加上overflow:hidden；使其建立一个BFC,让其内容消除对外界浮动元素的影响。**

![BFC不会重叠浮动元素2](https://user-gold-cdn.xitu.io/2018/10/14/16672a6717d1c1ab?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

这个方法可以用来实现两列自适应布局，效果不错，此时左边的宽度固定，右边的内容自适应宽度。如果我们改变文字的大小或者左边浮动元素的大小，两栏布局的结构依然没有改变！

### 特性3：BFC可以包含浮动----清除浮动
我们都知道浮动会脱离文档流，接下来我们看看下面的例子：

``` css
<style>
    .par {
        border: 5px solid #fcc;
        width: 300px;
    }

    .child {
        border: 5px solid #f66;
        width:100px;
        height: 100px;
        float: left;
    }
</style>
<body>
    <div class="par">
        <div class="child"></div>
        <div class="child"></div>
    </div>
</body>
```
![清除浮动1](https://user-gold-cdn.xitu.io/2018/1/4/160bfae723e59a6f?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

由于容器内两个div元素浮动，脱离了文档流，父容器内容宽度为零（即发生高度塌陷），未能将子元素包裹住。解决这个问题，只需要把把父元素变成一个BFC就行了。常用的办法是给父元素设置overflow:hidden

![清除浮动2](https://user-gold-cdn.xitu.io/2018/1/4/160bfae6ea1917ce?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

