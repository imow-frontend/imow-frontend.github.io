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
Fetch是一种HTTP数据请求的方式，是XMLHttpRequest的一种替代方案。fetch不是ajax的进一步封装，而是原生js。Fetch函数就是原生js，没有使用XMLHttpRequest对象。

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

```js
fetch(url).then(function(response) {
  return response.json();
}).then(function(data) {
  console.log(data);
}).catch(function(e) {
  console.log("Oops, error");
});
```

使用 ES6 的 箭头函数后：
```js
fetch(url).then(response => response.json())
  .then(data => console.log(data))
  .catch(e => console.log("Oops, error", e))
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

