---
title: web前端常见的安全问题
date: 2019-11-12 13:59:13
author: 刘涛
categories: JavaScript
tags:  
---

## 前言
在互联网时代，数据安全与个人隐私受到了前所未有的挑战，各种新奇的攻击技术层出不穷。如何才能更好地保护我们的数据？本文主要侧重于分析几种常见的攻击的类型以及防御的方法。

## SQL注入
SQL注入是一种常见的Web安全漏洞，攻击者利用这个漏洞，可以访问或修改数据，或者利用潜在的数据库漏洞进行攻击。

### 1.SQL注入的原理
我们先看看下面这个例子来说明其原理：
![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/web%E5%89%8D%E7%AB%AF%E5%B8%B8%E8%A7%81%E7%9A%84%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98/sql1.png)


```JavaScript
<form action="/login" method="POST">
    <p>Username: <input type="text" name="username" /></p>
    <p>Password: <input type="password" name="password" /></p>
    <p><input type="submit" value="登陆" /></p>
</form>
  ```
后端的 SQL 语句可能是如下这样的：
```JavaScript
let querySQL = `
    SELECT *
    FROM user
    WHERE username='${username}'
    AND psw='${password}'
`;
// 接下来就是执行 sql 语句...
  ```
这是我们经常见到的登录页面，但是如果有一个恶意的攻击者输入的用户名是admin' --，密码随意输入，就可以直接登入系统了。why! ----这就是SQL注入<br>
我们之前预想的SQL 语句是:
  SELECT * FROM user WHERE username='admin' AND psw='password' 
但是恶意攻击者用奇怪用户名将你的 SQL 语句变成了如下形式：
  SELECT * FROM user WHERE username='admin' --' AND psw='xxxx'
在 SQL 中,' --是闭合和注释的意思，-- 是注释后面的内容的意思，所以查询语句就变成了：
  SELECT * FROM user WHERE username='admin'

所谓的万能密码，本质上就是SQL注入的一种利用方式。
一次SQL注入的过程包括以下几个过程：
* 获取用户请求参数
* 拼接到代码中
* SQL语句按照我们构造参数的语义执行成功

**SQL注入的必备条件**
**1.可以控制输入的数据**
**2.服务器要执行的代码拼接了控制的数据**
![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/web%E5%89%8D%E7%AB%AF%E5%B8%B8%E8%A7%81%E7%9A%84%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98/sql2.png)


我们会发现SQL注入流程中与正常请求服务器类似，只是黑客控制了数据，构造了SQL查询，而正常的请求不会SQL查询这一步，**SQL注入的本质:数据和代码未分离，即数据当做了代码来执**行。

### 2.危害
1. 获取数据库信息
  * 管理员后台用户名和密码
  * 获取其它数据库敏感信息：用户名、密码、手机号、身份证、银行卡信息......
  * 整个数据库：脱裤
2. 获取服务器权限
3. 植入Webshell，获取服务器后门
4. 读取服务器敏感文件

### 3.如何防御
* **严格限制Web应用的数据库的操作权限，**给此用户提供仅仅能够满足其工作的最低权限，从而最大限度的减少注入攻击对数据库的危害

* **后端代码检查输入的数据是否符合预期，**严格限制变量的类型，例如使用正则表达式进行一些匹配处理。

* **对进入数据库的特殊字符（'，"，，<，>，&，*，; 等）进行转义处理，或编码转换。**基本上所有的后端语言都有对字符串进行转义处理的方法，比如 lodash 的 lodash._escapehtmlchar 库。

* **所有的查询语句建议使用数据库提供的参数化查询接口，**参数化的语句使用参数而不是将用户输入变量嵌入到 SQL 语句中，即不要直接拼接 SQL 语句。例如 Node.js 中的 mysqljs 库的 query 方法中的 ? 占位参数。



## XSS
XSS (Cross-Site Scripting)，跨站脚本攻击，因为缩写和 CSS重叠，所以只能叫 XSS。跨站脚本攻击是指通过存在安全漏洞的Web网站注册用户的浏览器内运行非法的HTML标签或JavaScript进行的一种攻击。

跨站脚本攻击有可能造成以下影响:
* 利用虚假输入表单骗取用户个人信息
* 利用脚本窃取用户Cookie值，被害者在不知情的情况下，帮助攻击者发送恶意请求。
* 显示伪造的文章或图片
  **XSS 的原理是恶意攻击者往 Web 页面里插入恶意可执行网页脚本代码，当用户浏览该页之时，嵌入其中 Web 里面的脚本代码会被执行，从而可以达到攻击者盗取用户信息或其他侵犯用户安全隐私的目的。**

  XSS 的攻击方式千变万化，但还是可以大致细分为几种类型。
 ### 1.非持久型 XSS（反射型 XSS ）###
  非持久型 XSS 漏洞，一般是通过给别人发送**带有恶意脚本代码参数的 URL**，当 URL 地址被打开时，特有的恶意代码参数被 HTML 解析、执行。
  ![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/web%E5%89%8D%E7%AB%AF%E5%B8%B8%E8%A7%81%E7%9A%84%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98/xss1.png)


  举一个例子，比如页面中包含有以下代码：
``` html
<select>
    <script>
        document.write(''
            + '<option value=1>'
            +     location.href.substring(location.href.indexOf('default=') + 8)
            + '</option>'
        );
        document.write('<option value=2>English</option>');
    </script>
</select>
  ```
攻击者可以直接通过 URL (类似：https://xxx.com/xxx?default=<script>console.log(document.cookie)</script>) 注入可执行的脚本代码。不过一些浏览器如Chrome其内置了一些XSS过滤器，可以防止大部分反射型XSS攻击。<br>
非持久型 XSS 漏洞攻击有以下几点特征：
* 即时性，不经过服务器存储，直接通过 HTTP 的 GET 和 POST 请求就能完成一次攻击，拿到用户隐私数据。
* 攻击者需要诱骗点击,必须要通过用户点击链接才能发起。
* 反馈率低，所以较难发现和响应修复。
* 盗取用户敏感保密信息。

为了防止出现非持久型 XSS 漏洞，需要确保这么几件事情：
* Web 页面渲染的所有内容或者渲染的数据都必须来自于服务端。
* 尽量不要从 URL，document.referrer，document.forms 等这种 DOM API 中获取数据直接渲染。
* 尽量不要使用 eval, new Function()，document.write()，document.writeln()，window.setInterval()，window.setTimeout()，innerHTML，document.createElement() 等可执行字符串的方法。
* 如果做不到以上几点，也必须对涉及 DOM 渲染的方法传入的字符串参数做 escape 转义。
* 前端渲染的时候对任何的字段都需要做 escape 转义编码。


### 2.持久型XSS(存储型XSS)###
持久型 XSS 漏洞，一般存在于 Form 表单提交等交互功能，如文章留言，提交文本信息等，黑客利用的 XSS 漏洞，将内容经正常功能提交进入数据库持久保存，当前端页面获得后端从数据库中读出的注入代码时，恰好将其渲染执行。
![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/web%E5%89%8D%E7%AB%AF%E5%B8%B8%E8%A7%81%E7%9A%84%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98/xss2.png) 

举个例子，对于评论功能来说，就得防范持久型 XSS 攻击，因为我可以在评论中输入以下内容
![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/web%E5%89%8D%E7%AB%AF%E5%B8%B8%E8%A7%81%E7%9A%84%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98/xss3.png) 

主要注入页面方式和非持久型 XSS 漏洞类似，只不过持久型的不是来源于 URL，referer，forms 等，而是来源于**后端从数据库中读出来的数据** 。持久型 XSS 攻击不需要诱骗点击，黑客只需要在提交表单的地方完成注入即可，但是这种 XSS 攻击的成本相对还是很高。
攻击成功需要同时满足以下几个条件：
  * POST请求提交表单后端没做转义直接入库。
  * 后端从数据库中取出数据没做转义直接输出给前端。
  * 前端拿到后端数据没做转义直接渲染成 DOM。

持久型 XSS 有以下几个特点：

  * 持久性，植入在数据库中
  * 盗取用户敏感私密信息
  * 危害面积广

### 3.如何防御###
对于 XSS 攻击来说，通常有两种方式可以用来防御。

**1)CSP**
CSP 本质上就是建立白名单，开发者明确告诉浏览器哪些外部资源可以加载和执行。我们只需要配置规则，如何拦截是由浏览器自己实现的。我们可以通过这种方式来尽量减少 XSS 攻击。
通常可以通过两种方式来开启 CSP：
* 设置 HTTP Header 中的 Content-Security-Policy
* 设置 meta 标签的方式

这里以设置 HTTP Header 来举例：
* 只允许加载本站资源
Content-Security-Policy: default-src 'self'

* 只允许加载 HTTPS 协议图片
Content-Security-Policy: img-src https://*

* 允许加载任何来源框架
Content-Security-Policy: child-src 'none'

如需了解更多属性，请查看[Content-Security-Policy文档](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy)
对于这种方式来说，只要开发者配置了正确的规则，那么即使网站存在漏洞，攻击者也不能执行它的攻击代码，并且 CSP 的兼容性也不错。

**2)转义字符**
用户的输入永远不可信任的，最普遍的做法就是转义输入输出的内容，对于引号、尖括号、斜杠进行转义。
``` javascript
function escape(str) {
  str = str.replace(/&/g, '&amp;')
  str = str.replace(/</g, '&lt;')
  str = str.replace(/>/g, '&gt;')
  str = str.replace(/"/g, '&quto;')
  str = str.replace(/'/g, '&#39;')
  str = str.replace(/`/g, '&#96;')
  str = str.replace(/\//g, '&#x2F;')
  return str
}
```
但是对于显示富文本来说，显然不能通过上面的办法来转义所有字符，因为这样会把需要的格式也过滤掉。对于这种情况，通常采用白名单过滤的办法，当然也可以通过黑名单过滤，但是考虑到需要过滤的标签和标签属性实在太多，更加推荐使用白名单的方式。
``` javascript
const xss = require('xss')
let html = xss('<h1 id="title">XSS Demo</h1><script>alert("xss");</script>')
// -> <h1>XSS Demo</h1>&lt;script&gt;alert("xss");&lt;/script&gt;
console.log(html)
 ```
 以上示例使用了 js-xss 来实现，可以看到在输出中保留了 h1 标签且过滤了 script 标签。

 **3) HttpOnly Cookie。**
 这是预防XSS攻击窃取用户cookie最有效的防御手段。Web应用程序在设置cookie时，将其属性设为HttpOnly，就可以避免该网页的cookie被客户端恶意JavaScript窃取，保护用户cookie信息。


 ## CSRF ##
 CSRF(Cross Site Request Forgery)，即跨站请求伪造，是一种常见的Web攻击，它利用用户已登录的身份，在用户毫不知情的情况下，以用户的名义完成非法操作。

 ### 1. CSRF攻击的原理 ###
 ![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/web%E5%89%8D%E7%AB%AF%E5%B8%B8%E8%A7%81%E7%9A%84%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98/csrf1.png) 

 完成 CSRF 攻击必须要有三个条件：
 * 用户已经登录了站点 A，并在本地记录了 cookie
 * 在用户没有登出站点 A 的情况下（也就是 cookie 生效的情况下），访问了恶意攻击者提供的引诱危险站点 B (B 站点要求访问站点A)。
 * 站点 A 没有做任何 CSRF 防御
  
  我们来看一个例子： 当我们登入转账页面后，突然眼前一亮惊现"XXX隐私照片，不看后悔一辈子"的链接，耐不住内心躁动，立马点击了该危险的网站（页面代码如下图所示），但当这页面一加载，便会执行submitForm这个方法来提交转账请求，从而将10块转给黑客。

   ![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/web%E5%89%8D%E7%AB%AF%E5%B8%B8%E8%A7%81%E7%9A%84%E5%AE%89%E5%85%A8%E9%97%AE%E9%A2%98/csrf2.png)

### 2.如何防御 ### 
防范 CSRF 攻击可以遵循以下几种规则：
* Get 请求不对数据进行修改
* 不让第三方网站访问到用户 Cookie
* 阻止第三方网站请求接口
* 请求时附带验证信息，比如验证码或者 Token

**1)SameSite**
可以对 Cookie 设置 SameSite 属性。该属性表示 Cookie 不随着跨域请求发送，可以很大程度减少 CSRF 的攻击，但是该属性目前并不是所有浏览器都兼容。

**2)Referer Check**
HTTP Referer是header的一部分，当浏览器向web服务器发送请求时，一般会带上Referer信息告诉服务器是从哪个页面链接过来的，服务器籍此可以获得一些信息用于处理。可以通过检查请求的来源来防御CSRF攻击。正常请求的referer具有一定规律，如在提交表单的referer必定是在该页面发起的请求。所以**通过检查http包头referer的值是不是这个页面，来判断是不是CSRF攻击**。

但在某些情况下如从https跳转到http，浏览器处于安全考虑，不会发送referer，服务器就无法进行check了。若与该网站同域的其他网站有XSS漏洞，那么攻击者可以在其他网站注入恶意脚本，受害者进入了此类同域的网址，也会遭受攻击。出于以上原因，无法完全依赖Referer Check作为防御CSRF的主要手段。但是可以通过Referer Check来监控CSRF攻击的发生。


**3)Anti CSRF Token**
目前比较完善的解决方案是加入Anti-CSRF-Token。即发送请求时在HTTP 请求中以参数的形式加入一个随机产生的token，并在服务器建立一个拦截器来验证这个token。服务器读取浏览器当前域cookie中这个token值，会进行校验该请求当中的token和cookie当中的token值是否都存在且相等，才认为这是合法的请求。否则认为这次请求是违法的，拒绝该次服务。

**这种方法相比Referer检查要安全很多**，token可以在用户登陆后产生并放于session或cookie中，然后在每次请求时服务器把token从session或cookie中拿出，与本次请求中的token 进行比对。由于token的存在，攻击者无法再构造出一个完整的URL实施CSRF攻击。但在处理多个页面共存问题时，当某个页面消耗掉token后，其他页面的表单保存的还是被消耗掉的那个token，其他页面的表单提交时会出现token错误。


**4)验证码**
应用程序和用户进行交互过程中，特别是账户交易这种核心步骤，强制用户输入验证码，才能完成最终请求。在通常情况下，验证码够很好地遏制CSRF攻击。**但增加验证码降低了用户的体验，网站不能给所有的操作都加上验证码**。所以只能将验证码作为一种辅助手段，在关键业务点设置验证码。
