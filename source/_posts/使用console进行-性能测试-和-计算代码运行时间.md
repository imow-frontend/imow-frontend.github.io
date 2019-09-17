---
title: 使用console进行 性能测试 和 计算代码运行时间
date: 2019-09-17 14:42:09
categories: 
- JavaScript 
tags:
- 性能测试 
---

# 前言
对于前端开发人员，在开发过程中经常需要监控某些表达式或变量的值，如果使用用 debugger 会显得过于笨重，最常用的方法是会将值输出到控制台上方便调试。最常用的语句就是console.log(expression)了。


## trace

`console.trace()`用来追踪函数的调用过程。

在大型项目尤其是框架开发中，函数的调用轨迹可以十分复杂，`console.trace()`方法可以将函数的被调用过程清楚地输出到控制台上。
```html
<button onclick="myFunction()">跟踪轨迹</button>
```

```js 

function myFunction() {
  console.log(1)
  myOtherFunction();
  console.log(2)
}

function myOtherFunction() {
  console.log(3);
  myOtherFunction2();
  console.log(4);
}
function myOtherFunction2() {
  console.trace();
}
```
打印结果：
![Markdown](http://i1.fuimg.com/568986/00e48355e16c2cbe.png)


## table

使用console将对象以表格呈现

可将传入的对象，或数组以表格形式输出，相比传统树形输出，这种输出方案更适合内部元素排列整齐的对象或数组，不然可能会出现很多的 undefined。
```js
var Teletubbies = {
  Tinky:{ 
    name: 'Tinky',
    age: '3'
  },
  lala: {
    name: 'lala',
    age: '6'
  },
  Daisy: {
    name: 'Daisy',
    age: '4'
  },
  po:{
    name:'po',
    age:'5'
  }
};
console.table(Teletubbies);
```
打印结果：
![Markdown](http://i1.fuimg.com/568986/e7119ca8ae2455ac.png)


## console.time进行时间计算

计算程序的执行时间

可以将成对的console.time()和console.timeEnd()之间代码的运行时间输出到控制台上
```js
console.time('计时器');
for (var i = 0; i < 1000; i++) {
  for (var j = 0; j < 1000; j++) {}
}
console.timeEnd('计时器');
```
以上代码可以计算出`console.time('计时器')`和`console.timeEnd('计时器')`之间的代码块所需要的时间。


## 还可以console.log配合Date对象计算
除了console.time()还可以用console.log配合Date对象计算
```js
var beginTime = +new Date();
  for (var i = 0; i < 1000; i++) {
    for (var j = 0; j < 1000; j++) {}
  }
var endTime = +new Date();
console.log("排序用时共计"+(endTime-beginTime)+"ms");


//或者
var beginTime  = new Date().getTime() 
for (var i = 0; i < 1000; i++) {
    for (var j = 0; j < 1000; j++) {}
  }
var endTime  = new Date().getTime()
console.log("排序用时共计"+(endTime-beginTime)+"ms");

//或者
var beginTime  = new Date().getTime() 
for (var i = 0; i < 1000; i++) {
    for (var j = 0; j < 1000; j++) {}
  }
var endTime  = new Date().getTime()
console.log("排序用时共计"+(endTime-beginTime)+"ms");
```
那么你肯定会问这三者的的区别

### Date.now()与 new Date().getTime() 的区别
它们都可以获取时间戳，但是实际上性能是不一样的（虽然差别不大，但是也算是一种性能的追求），可以做个测试
```js
  console.time('+new Date()')
  for(var i = 0; i < 100000; i++) {
    var o = + new Date()
  }
  console.timeEnd('+new Date()')

  console.time('new Date().getTime:')
  for(var j = 0; j < 100000; j++) {
    var p = new Date().getTime();
  }
  console.timeEnd('new Date().getTime:')

  console.time('Date.now()')
  for(var k = 0; k < 100000; k++) {
    var q = Date.now()
  }
  console.timeEnd('Date.now()') 

  //输出
  +new Date(): 42.31396484375ms
  new Date().getTime:: 33.93408203125ms
  Date.now(): 24.561767578125ms
```
测试用例[https://jsperf.com/date-now-vs-new-date]

![Markdown](http://i2.tiimg.com/568986/92dcb75722e2b402.png)

## profile

使用`console.profile`测试程序性能

开发中，我们常常要评估段代码或是某个函数的性能。在函数中手动打印时间固然可以，但显得不够灵活而且有误差。借助控制台以及console.profile()方法我们可以很方便地监控运行性能。

```js
function parent() {
      for (var i = 0; i < 10000; i++) {
            childA()
      }
}

function childA() {
      for (var i = 0; i < 2000; i++) {}
}

console.profile('性能分析');
parent();
console.profileEnd('性能分析');

//执行后
//Profile '性能分析' started.
//Profile '性能分析' finished.
```
![Markdown](http://i1.fuimg.com/568986/196edcdae0dc0ed8.png)
