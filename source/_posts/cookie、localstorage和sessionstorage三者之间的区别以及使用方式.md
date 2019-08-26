---
layout: post
title: cookie、localstorage和sessionstorage三者之间的区别以及使用方式
date: 2019-08-06 10:40:30
tags:
---


## 前言
前端开发的时候，在网页刷新的时候，所有数据都会被清空，这时候就要用到本地存储的技术，前端本地存储的方式有三种，分别是cookie，localstorage和sessionStorage ，这是大家都知道的。

## 使用方式
### cookie：
 **保存cookie值** 

 ``` javacript

 var dataCookie='110';
    document.cookie = 'token' + "=" +dataCookie;

```

**获取指定名称的cookie值**
``` javacript  

  function getCookie(name) { //获取指定名称的cookie值
// (^| )name=([^;]*)(;|$),match[0]为与整个正则表达式匹配的字符串，match[i]为正则表达式捕获数组相匹配的数组；
var arr = document.cookie.match(new RegExp("(^| )"+name+"=([^;]*)(;|$)"));
if(arr != null) {
  console.log(arr);
  return unescape(arr[2]);
}
return null;
}
 var cookieData=getCookie('token'); //cookie赋值给变量。

```

### localStorage和sessionStorage：
**localStorage和sessionStorage所使用的方法是一样的,** 下面以sessionStorage为例子：

``` javacript

var name='sessionData';
var num=120;
sessionStorage.setItem(name,num);//存储数据
sessionStorage.setItem('value2',119);
let dataAll=sessionStorage.valueOf();//获取全部数据
console.log(dataAll,'获取全部数据');
var dataSession=sessionStorage.getItem(name);//获取指定键名数据
var dataSession2=sessionStorage.sessionData;//sessionStorage是js对象，也可以使用key的方式来获取值
 console.log(dataSession,dataSession2,'获取指定键名数据');
sessionStorage.removeItem(name); //删除指定键名数据
  console.log(dataAll,'获取全部数据1');
 sessionStorage.clear();//清空缓存数据：localStorage.clear();
  console.log(dataAll,'获取全部数据2'); 

```

## 三者的异同：

### 生命周期：
 * cookie：可设置失效时间，没有设置的话，默认是关闭浏览器后失效

 * localStorage：除非被手动清除，否则将会永久保存。

 * sessionStorage： 仅在当前网页会话下有效，关闭页面或浏览器后就会被清除。

### 存放数据大小：
 * cookie：4kb左右 

 * localStorage和sessionStorage：可以保存5MB的信息

### http请求：
 * cookie：每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题

 * localStorage和sessionStorage：源生接口可以接受，亦可再次封装来对Object和Array有更好的支持


## 应用场景：
从安全性来说，因为每次http请求都会携带cookie信息，这样无形中浪费了带宽，所以cookie应该尽可能少的使用，另外cookie还需要指定作用域，不可以跨域调用，限制比较多。但是用来识别用户登录来说，cookie还是比stprage更好用的。其他情况下，可以使用storage，就用storage。

storage在存储数据的大小上面秒杀了cookie，现在基本上很少使用cookie了，因为更大总是更好的。

localStorage和sessionStorage唯一的差别一个是永久保存在浏览器里面，一个是关闭网页就清除了信息。localStorage可以用来夸页面传递参数，sessionStorage用来保存一些临时的数据，防止用户刷新页面之后丢失了一些参数。

## 浏览器的支持情况:
 * localStorage和sessionStorage是html5才应用的新特性，可能有些浏览器并不支持，这里要注意。
 ![storage](https://user-gold-cdn.xitu.io/2017/11/25/15ff2d54764e53af?imageView2/0/w/1280/h/960/format/webp/ignore-error/1 "storage")

 * cookie的浏览器支持没有找到，可以通过下面这段代码来判断所使用的浏览器是否支持cookie：

 ``` javacript

if(navigator.cookieEnabled) {
  alert("你的浏览器支持cookie功能");//提示浏览器支持cookie  
} else {
  alert("你的浏览器不支持cookie");//提示浏览器不支持cookie   }


```

### 数据存放处：
 ![dataStorage](https://user-gold-cdn.xitu.io/2017/11/25/15ff2f727028f37b?imageView2/0/w/1280/h/960/format/webp/ignore-error/1 "dataStorage")<center><font color=gray size=2>Cookie、localStorage、sessionStorage数据存放处</font></center>

 ### 各浏览器Cookie大小、个数的限制
  **1. 浏览器允许每个域名所包含的cookie数：**

     Microsoft指出InternetExplorer8增加cookie限制为每个域名50个，但IE7似乎也允许每个域名50个cookie。

     Firefox每个域名cookie限制为50个。

     Opera每个域名cookie限制为30个。

     Safari/WebKit貌似没有cookie限制。但是如果cookie很多，则会使header大小超过服务器的处理的限制，会导致错误发生。

     注：“每个域名cookie限制为20个”将不再正确！

  **2. 当很多的cookie被设置，浏览器如何去响应：**

     除Safari（可以设置全部cookie，不管数量多少），有两个方法：

     最少最近使用（leastrecentlyused(LRU)）的方法：当Cookie已达到限额，自动踢除最老的Cookie，以使给最新的Cookie一些空间。Internet Explorer和Opera使用此方法。

     Firefox很独特：虽然最后的设置的Cookie始终保留，但似乎随机决定哪些cookie被保留。似乎没有任何计划（建议：在Firefox中不要超过Cookie限制）。

  **3. 不同浏览器间cookie总大小也不同：**

     Firefox和Safari允许cookie多达4097个字节，包括名（name）、值（value）和等号。

     Opera允许cookie多达4096个字节，包括：名（name）、值（value）和等号。

     Internet Explorer允许cookie多达4095个字节，包括：名（name）、值（value）和等号。

     注：多字节字符计算为两个字节。在所有浏览器中，任何cookie大小超过限制都被忽略，且永远不会被设置。


## 最后：
不要把什么数据都放在 Cookie、localStorage 和 sessionStorage中，毕竟前端的安全性这么低。只要打开控制台就可以任意的修改 Cookie、localStorage 和 sessionStorage的数据了。涉及到金钱或者其他比较重要的信息，还是要存在后台比较好。
