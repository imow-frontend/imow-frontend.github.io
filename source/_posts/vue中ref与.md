---
title: vue中ref与$refs
date: 2020-07-20 16:33:01
author: 胡豪
categories: 
  - vue
tags:
  - vue 
---

## 前言
有时候在vue中，也需要通过获取dom去实现一些操作，比如控制滚动条
这个时候就需要ref与$refs来帮忙了

## 如何使用
1.首先在html里写ref
```js
<Table
  ref="getTable"
  :custom-row="pointMark"
  :columns="column"
  :data-source="tableData"
  :pagination="false"
  :scroll="{ y: 200 }"
>
```

2.再js里通过$ref获取,$ref.后面跟着的就是，你在html里自定义的名字
```js
this.$refs.getTable
```
## 实际例子
在项目中，通过点击事件，为了解决调整滚动条的，必须要获取带滚动条盒子的dom

在项目中，我要获取是table子组件里dom 需要借助$el

```js
//先获取到子组件dom
const tableDom = this.$refs.getTable 

//通过$el获取子组件内部的dom,一层层往下找，$el.children是数组，包含下一层所有节点
const tbodyDom = tableDom.$el.children[0].children[0].children[0].children[0].children[0].children[1]

//通过计算滚动条要滚动的高度实现滚动 
const scrollHeight = 102 * this.trIndex   
tbodyDom.scrollTo(0, scrollHeight)
```