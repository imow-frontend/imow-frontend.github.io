---
title: Vue中路由传参
date: 2020-04-15 09:25:49
author: 胡豪
categories: 
  - vue
tags:
  - vue 
---

## 通常我们在路由中传递数据给另一个页面用如下方法：
### 一、params (不会显示在地址栏)
```js
this.$router.push({ name : 'detail' , params : { id: id } })

<router-link :to="{name:'detail',params:{name:1}}"> xxx </router-link>
```
接收参数
```js
this.$route.params.id 
```

### 二、query (参数会显示在url的后面)
注意：页面之间的跳转使用query 不然的话刷新页面后会找不到参数
```js
this.$router.push({ path: '/test', query: { id: id } })

<router-link :to="{path:'/detail',query:{id: id}}">跳转<router-link>
<router-link :to="{name:'detail',query:{id:1}}"> xxx </router-link>  
//name还是path 取决于路由配置的时候有没有命名路由
```
接收参数
```js
this.$route.query.id
```

注：这里大家可能会困惑 path 与 name,我要是写反了会怎么样，这里官网的例子里有解答
```js
const userId = '123'
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```

### 三、通过path跳转 
[#动态路由匹配](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html "Vue官方文档")
[#命名路由](https://router.vuejs.org/zh/guide/essentials/named-routes.html "Vue官方文档")

```js
this.$router.push({ name: 'user', params: { userId: 123 }})  

<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
```
路由：
```js
routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
```
接收参数
```js
this.$route.params.id
```


注意：route与router的区别

$route为当前router跳转对象里面可以获取name、path、query、params等

$router为VueRouter实例，想要导航到不同URL，则使用$router.push方法

返回上一个history也是使用$router.go方法





## nuxt的路由传参 与vue基本一致 
```js
//js写法:
let name = "ayasa"
this.$router.push({ path : ' /404 ' , query : { name } })

//html写法:
<nuxt-link :to=" '/404?name=' + name ">{{ name }}</nuxt-link>
```

## 最后在做项目时遇到了路由传参在接收页面刷新数据消失的问题
解决方法参考以下文章：
[#参考文章1](https://www.jb51.net/article/116416.htm)
[#参考文章2](https://blog.csdn.net/benben513624/article/details/86583149)

在我自己试验的时候，我用query的模式，也遇到了这个问题,在query里我直接的丢了个对象
```js
this.$router.push({ path : ' /404 ' , query : { obj } })   //obj是我data里定义的一个对象
```
但是在接收参数页面第一次是有数据的，刷新后，打印的`this.$route.query.keyWord`就变成`[Object Object]`取不出值
后面我把传值改成了下面这样，就解决了
```js
this.$router.push({ path : ' /404 ' , query : { name , age , other } })
```
