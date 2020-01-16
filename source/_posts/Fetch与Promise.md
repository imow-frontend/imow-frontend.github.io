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

//使用 ES6 的 箭头函数后： 
fetch(url).then(response => response.json())
  .then(data => console.log(data))
  .catch(err => console.log("error", err)) 

//发送json
fetch('/xxx', {
  method: 'post',
  body: JSON.stringify({
      username: '',
      password: ''
  })
});

//发送form
var form = document.querySelector('form');
fetch('/xxx', {
    method: 'post',
    body: new FormData(form)
});  
```

Response(res)对象成员
- ok:是否成功
- headers:响应头对象
- status/statusText:状态码/状态文本
- redirected:是否重定向过

要获取响应头里面的Content-Type,调用方法：
```js
res.headers.get('Content-Type')
```
 


## 举个例子(登录接口)
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
  .catch(e => {
    console.log('errr', e);
  });
}

//async,await使用
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
Fetch 就是ES6提供的一个异步接口，这样省的自己封装了。一个基本的fetch操作很简单，就是通过fetch请求，返回一个promise对象，然后在promise对象的then方法里面用fetch的response.json()等方法进行解析数据，由于这个解析返回的也是一个promise对象，所以需要两个then才能得到我们需要的json数据。

### fetch优势：
1. 语法简洁，更加语义化
2. 基于标准 Promise 实现，支持 async/await
3. 同构方便，使用 isomorphic-fetch

### 优雅的使用fetch 
fetch的确很好用，但是有的浏览器确是不支持的(比如IE)，这时就需要我们手动实现ajax作为替代，或者引入polyfill。
1. 由于 IE8 是 ES3，需要引入 ES5 的 polyfill: es5-shim, es5-sham
2. 引入 Promise 的 polyfill: es6-promise
3. 引入 fetch 探测库：fetch-detector
4. 引入 fetch 的 polyfill: fetch-ie8
6. 使用 async/await 
7. 如需跨域，可使用fetch-jsonp

### 为何不能直接使用Fetch基本操作
fetch是一个低层次的API，你可以把它考虑成原生的XHR，所以使用起来并不是那么舒服，需要进行封装。

1. 当接收到一个代表错误的 HTTP 状态码时,比如400, 500，fetch不会把promise标记为reject, 而是标记为resolve，仅当网络故障时或请求被阻止时，才会标记为 reject。

2. 默认情况下，fetch 不会从服务端发送或接收任何 cookies, 如果站点依赖于用户 session，则会导致未经认证的请求（要发送 cookies，必须设置 credentials 选项）。
```js
fetch(url, {credentials: 'include'})
```

3. fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费。
原生不行，但可以用到 [bluebird](http://bluebirdjs.com/docs/api-reference.html) 查阅方法，通过可以取消 promise，去达到取消fetch的目的。

4. fetch没有办法原生监测请求的进度，而XHR可以。

从这里可以看出来，如果我们要在fetch请求出错的时候及时地捕获错误，是需要对response的状态码进行解析的。又由于fetch返回的数据不一定是json格式，我们可以从header里面Content-Type获取返回的数据类型，进而使用正确的解析方法。  

接收到一个代表错误的 HTTP 状态码时通过抛出错误改进：
```js
checkStatus(response) {//检查响应状态
  if(response.status >= 200 && response.status < 300) {//响应成功
      return response;
  }
  if(response.status === 301 || response.status === 302) {//重定向
      window.location = response.headers.get('Location');
  }
  const error = new Error(response.statusText);
  error.data = response;
  throw error;
}
```

判断数据类型，进而使用正确的解析：
```js
async parseResult(response) {//解析返回的结果
  const contentType = response.headers.get('Content-Type');
  if(contentType != null) {
      if(contentType.indexOf('text') > -1) {
          return await response.text()
      }
      if(contentType.indexOf('form') > -1) {
          return await response.formData();
      }
      if(contentType.indexOf('video') > -1) {
          return await response.blob();
      }
      if(contentType.indexOf('json') > -1) {
          return await response.json();
      }
  }
  return await response.text();
}
```

### 使用async/awiait的原因
```js
(async () => {
  try {
    let response = await fetch(url);
    let data = await response.json();
    console.log(data);
  } catch(error) {
    console.log("error:", error);
  }
})()
```
Promise 将异步操作规范化。使用then连接, 使用catch捕获错误, 堪称完美, 美中不足的是, then和catch中传递的依然是回调函数。
为此, ES7 提供了更标准的解决方案 — async/await.  
使用 await 后，告别面条式调用。从代码可以看到 await 后面可以跟 Promise 对象，表示等待 Promise resolve() 才会继续向下执行，如果 Promise 被 reject() 或抛出异常则会被外面的 try...catch 捕获。

#### Await b()与Promise.then(b)
有的小伙伴可能很困惑.then()与await;可以参考一篇文章[从JS引擎理解Await b()与Promise.then(b)的堆栈处理](https://blog.csdn.net/fundebug/article/details/81127760)

## 总结
由此看出Fetch存在各种问题，实际使用中需要二次封装，相比于ajax、axios，现阶段更推荐使用axios
