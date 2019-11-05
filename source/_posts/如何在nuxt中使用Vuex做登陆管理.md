---
title: 如何在nuxt中使用Vuex做登陆管理
date: 2019-11-4 10:45:08
author: 胡豪
categories:
  - JavaScript
  - vue
tags:
  - SSR
  - Nuxt
---

## 前言

在vue中vuex可以拿来做状态管理，今天记录下在nuxt框架下如何使用vuex实现项目中的登陆状态的管理


## Vuex
用token进行验证登录,根据约定,把token放在header中  
根目录下创建一个store文件夹，在store中创建一个index.js

### 简述Vuex
Vuex是单项数据流，利用Vuex做状态管理，可以对数据更好地追踪与bug发现  
- state 初始化状态  
- gatter 对数据的过滤操作
- mutations 进行业务逻辑操作，它是同步的
- action 如果需要异步操作，可以写在action中


### /store/index.js
```js
import Vue from "vue";
import Vuex from "vuex";
import axios from "axios";

Vue.use(Vuex);

const store = () =>
  new Vuex.Store({
    state: {
      user: {
        isLogin: false,
        userInfo: "",
        token: "",
        ticket: ""
      }
    },
    mutations: {
      login(state, data) { 
        state.user.isLogin = true;
        state.user.userInfo = data.userInfo;
        state.user.userInfo.token = data.token;
        axios.defaults.headers.common["Authorization"] = data.token;      
      },
      loginout(state) {
        state.user.isLogin = false;
        state.user.userInfo = "";
        axios.defaults.headers.common["Authorization"] = "undefined";
      },
      // 设置用户 openId 对应的 key
      setTicket(state, data) {
        state.user.ticket = data;
      }
    },
    actions: {
      nuxtServerInit({ commit }, { req }) { 
        if (req.session) {
          if (req.session.authUser) {
            commit("login", req.session.authUser);
          } else if (req.session.ticket) {
            commit("setTicket", req.session.ticket);
          }
        }
      }
    }
  });
export default store;
```

## middleware 
官方文档: [https://zh.nuxtjs.org/guide/routing#%E4%B8%AD%E9%97%B4%E4%BB%B6](https://zh.nuxtjs.org/guide/routing/#%E4%B8%AD%E9%97%B4%E4%BB%B6)  
利用middleware实现不登录无法访问

```js
export default async function ({ store,redirect }) {
  //用户未登录就跳转登录页
  if(!store.state.user.isLogin){
    redirect('/login')
  }
}
```
在组件中使用`middleware: 'mustLogin', data() {return {}}`

## 登陆组件login
登陆页，接口验证成功后，更新store存储的状态 `this.$store.commit("login", result.data);`

完整代码如下：
```js
methods: {
  async submitForm(formName) {
    const account = this.loginForm.account;
    const password = this.loginForm.password;
    this.$refs[formName].validate(async valid => {
      if (valid) {
        let result = await login(account, password);
        if (result.success) {
          // 登录成功
          this.$store.commit("login", result.data);
          if (window.history.length > 1) {
            this.$router.go(-1);
          } else {
            this.$router.push("/");
          }
        } else {
          MessageBox({
            type: "error",
            title: "登录信息",
            message: result.message
          });
        }
      } else {
        return false;
      }
    });
  }
} 
```
## 登出组件login
点击退出按钮，接口发送退出请求，更新store存储的状态 `this.$store.commit("loginout",null);`
```html
<span v-if="$store.state.user.isLogin" @click="loginOut">退出</span>
```

```js
methods: {
  async loginOut() { 
    await loginOut();
    this.$store.commit("loginout",null);
    this.$router.push("/")
  }
}
```

## 总结
至此,就是nuxt中使用Vuex的整个流程,具体不同的地方就需要在项目中针对性地去调整修改。