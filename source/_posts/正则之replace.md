---
title: 正则之replace
date: 2020-07-22 14:21:27
author: 胡豪
categories: 
  - JavaScript
tags:
  - 正则 
---

## 正则 —— replace

## 前言
在项目中，需要对富文本字符串进行处理，给所有的img标签的src属性加上统一域名

所以采用正则来实现

## replace方法
使用就是字符串.replace（）；
如下：
```js
var str = 'this is tom';

var strReg = str.replace(/tom/g,'jack');

console.log(strReg);//this is jack意思就是在字符串str中全局搜索tom这个字符，并将他换成jack`；
```

其中`$`符号的使用，$1，$2，$3......是表示正则匹配的组，对应正则规则里的一个（）的内容就是一个组。
例子：
```js
var str = '123-mm'

var strReg = str.replace(/(\d+)-([A-Za-z]+)/g,'$2')

console.log(strReg) //123-mm就被mm替换的了，输出mm  
```

## 在项目中体现
data.content是接口中获取的富文本字符串
this.baseUrl是自定义的域名

最早是这么写的
```js
let content = unescape(data.content)
const srcReg = /src=([\'\"]?([^\'\"]*)[\'\"]?)/gi   
if (content) {
  content = content.replace(srcReg, "src='" + this.baseUrl + '$2' + "'")
  this.html = unescape(content)
}
```

后来多了一个条件：需要判断原来img的src路径是否包换http等域名前缀，有则无需加
修改了正则，$位置也发生改变
```js
let content = unescape(data.content)
const srcReg = /src=([\'\"](?!(https|http))([^\'\"]*)[\'\"]?)/gi   //eslint-disable-line
if (content) {
  content = content.replace(srcReg, "src='" + this.baseUrl + '$3' + "'")
  this.html = unescape(content)
}
```

## 例子测试
使用在线正则测试网站[link](https://tool.oschina.net/regex)

测试内容：
```html
<div data-v-772280c7="" class="solution"><p><img src="http://epcare.oss-cn-hangzhou.aliyuncs.com/Answer-20200721-504240889240193.jpg" style="width: 200px;"><img src="http://epcare.oss-cn-hangzhou.aliyuncs.com/Answer-20200721-491064112187750.jpg" style="width: 200px;"><img src="http://epcare.oss-cn-hangzhou.aliyuncs.com/Answer-20200721-556411729561973.jpg" style="width: 200px;"><br></p><img src="/FileUpload/Answer/20200716/512331910820482.png" style="width: 200px;"></div>
```
正则：
```
src=([\'\"](?!(https|http))([^\'\"]*)[\'\"]?)
```

匹配结果：
```
共找到 1 处匹配：
src="/FileUpload/Answer/20200716/512331910820482.png"
```


替换文本:
```
http://198.168.1.2$3
```

替换结果:
```
<div data-v-772280c7="" class="solution"><p><img src="http://epcare.oss-cn-hangzhou.aliyuncs.com/Answer-20200721-504240889240193.jpg" style="width: 200px;"><img src="http://epcare.oss-cn-hangzhou.aliyuncs.com/Answer-20200721-491064112187750.jpg" style="width: 200px;"><img src="http://epcare.oss-cn-hangzhou.aliyuncs.com/Answer-20200721-556411729561973.jpg" style="width: 200px;"><br></p><img http://198.168.1.2/FileUpload/Answer/20200716/512331910820482.png style="width: 200px;"></div>
```