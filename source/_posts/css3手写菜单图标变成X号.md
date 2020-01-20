---
title: css3手写菜单图标变成X号
date: 2020-01-19 15:42:59
author: 胡豪
categories:
  - css
tags:
  - css
---

## 前言
做项目中经常会遇到三条横线的菜单按钮，一般都是点击按钮出现菜单栏，再次点击关闭菜单栏，直接显示X号就显得很生硬，这里使用CSS3动画将这个过渡过程变得非线性感，更讨好眼球。
例子里用的是hover更直接明显，在项目里可以用class来控制


## 如何写
代码如下：
```html
<!DOCTYPE html>
<html>
  <head>
    <title>CSS3 写菜单按钮变形小动画</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/normalize/8.0.1/normalize.min.css"></script>
    <style>
      .menu-button {
        position: absolute;
        top: 18px;
        left: 24px;
        width: 30px;
        height: 24px;
        padding: 0;
        cursor: pointer;
        border: none;
        outline: none;
        background: transparent;
      }
      button {
        -webkit-appearance: button;
      }
      button,select {
        text-transform: none;
      }
      button,input,optgroup,select,textarea {
        font-family: inherit;
        font-size: 100%;
        line-height: 1.15;
        margin: 0;
      }
      .menu-button:after,.menu-button:before {
        content: "";
        position: absolute;
        top: 50%;
        left: 0;
        width: 100%;
        height: 2px;
        border-radius: 2px;
        pointer-events: none;
        -webkit-transition: -webkit-transform 0.25s;
        transition: -webkit-transform 0.25s;
        transition: transform 0.25s;
        transition: transform 0.25s, -webkit-transform 0.25s;
        -webkit-transform-origin: 50% 50%;
        transform-origin: 50% 50%;
      }
      .menu-button:after,.menu-button:before,.menu-button span {
        background: #5f656f;
      }
      .menu-button:before {
        -webkit-transform: translate3d(0, -10px, 0) scaleX(0.8);
        transform: translate3d(0, -10px, 0) scaleX(0.8);
      }
      .menu-button:after {
        -webkit-transform: translate3d(0, 10px, 0) scaleX(0.8);
        transform: translate3d(0, 10px, 0) scaleX(0.8);
      }
      .menu-button span {
        position: absolute;
        left: 0;
        overflow: hidden;
        width: 100%;
        height: 2px;
        text-indent: 200%;
        -webkit-transition: opacity 0.25s;
        transition: opacity 0.25s;
      }
      .header:hover .menu-button--open:before {
        -webkit-transform: rotate(45deg);
        transform: rotate(45deg);
      }
      .header:hover .menu-button--open:after {
        -webkit-transform: rotate(-45deg);
        transform: rotate(-45deg);
      }
      .header:hover .menu-button--open span {
        opacity: 0;
      }
    </style>
  </head>

  <body>
    <!--header-->
    <div class="header">
      <button class="menu-button menu-button--open"><span>Menu</span></button>
    </div>
  </body>
</html>
```
