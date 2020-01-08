---
title: Fetch与Promise
date: 2020-01-07 14:21:55
author: 胡豪
categories: 
  - js
tags:
  - js 
---

## 什么是Fecth
Fetch是一种HTTP数据请求的方式，是XMLHttpRequest的一种替代方案。Fetch不是ajax的进一步封装，它们是两个东西。Fetch函数就是原生js，没有使用XMLHttpRequest对象。

XMLHttpRequest 是一个设计粗糙的API，配置和调用方式非常混乱，而且基于事件的异步模型写起来也没有现代的 Promise，generator/yield，async/await 友好。

Fetch 的出现就是为了解决 XHR 的问题，它实现了 Promise 规范，返回 Promise 实例；而 Promise 是为解决异步回调问题而摸索出的一套方案。

## 简述Promise
说Fetch，免不了谈及Promise，这里就再来回顾下/**Promise**/：

Promise是一个对象，用作占位符，用于延迟（可能是异步）计算的最终结果。

一个 Promise 就是一个对象，它代表了一个异步操作的最终完成或者失败。

本质上，Promise 是一个绑定了回调的对象，而不是将回调传进函数内部。

Promise的三种状态：
- 初始化，状态：pending
- 当调用resolve(成功)，状态：pengding=>fulfilled
- 当调用reject(失败)，状态：pending=>rejected


## 使用 Fetch 
基本操作：
```js
fetch(url).then(function(response) {
  return response.json();
}).then(function(data) {
  console.log(data);
}).catch(function(err) {
  console.log(err);
});
```

使用 ES6 的 箭头函数后：
```js
fetch(url).then(response => response.json())
  .then(data => console.log(data))
  .catch(err => console.log("error", err))
```

举个例子(登录接口)
```js
LogIn() {
  let params = {
    username: 'test',
    password: '123456'
  }
  fetch('http://192.168.3.103:9999/api/users/login', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(params)
  })
  .then((res) => {
    return res.json();
  })
  .then((json) => {
    console.log(json)
  })
  .catch(function(e) {
    console.log("Oops, error");
  })
}
```

async,await使用
```js
async LogIn() {
  let params = {
    username: 'test',
    password: '123456'
  }
  try {
      let response = await fetch('http://192.168.3.103:9999/api/users/login', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(params)
      })
      let dataPromise = await response.json();
      console.log(dataPromise)
  } catch(err) {
      console.log(err)
  }
}
```

## 小结
一个基本的fetch操作很简单。就是通过fetch请求，返回一个promise对象，然后在promise对象的then方法里面用fetch的response.json()等方法进行解析数据，由于这个解析返回的也是一个promise对象，所以需要两个then才能得到我们需要的json数据。

fetch优势：
1. 语法简洁，更加语义化
2. 基于标准 Promise 实现，支持 async/await
3. 同构方便，使用 isomorphic-fetch

fetch的确很好用，但是有的浏览器确是不支持(比如IE)，这时就需要我们手动实现ajax作为替代，或者引入polyfill。
1. 由于 IE8 是 ES3，需要引入 ES5 的 polyfill: es5-shim, es5-sham
2. 引入 Promise 的 polyfill: es6-promise
3. 引入 fetch 探测库：fetch-detector
4. 引入 fetch 的 polyfill: fetch-ie8
6. 使用 async/await 

## 为何不能直接使用Fetch基本操作
fetch规范与jQuery.ajax()主要有两种方式的不同：

1. 当接收到一个代表错误的 HTTP 状态码时,比如400, 500，fetch不会把promise标记为reject, 而是标记为resolve，仅当网络故障时或请求被阻止时，才会标记为 reject。

2. 默认情况下，fetch 不会从服务端发送或接收任何 cookies, 如果站点依赖于用户 session，则会导致未经认证的请求（要发送 cookies，必须设置 credentials 选项）。

从这里可以看出来，如果我们要在fetch请求出错的时候及时地捕获错误，是需要对response的状态码进行解析的。又由于fetch返回的数据不一定是json格式，我们可以从header里面Content-Type获取返回的数据类型，进而使用正确的解析方法。

## 使用async/awiait的原因

