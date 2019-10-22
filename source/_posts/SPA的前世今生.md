---
title: SPA的前世今生
date: 2019-10-11 15:24:55
author: 胡豪
categories: 
- JavaScript 
tags:
- js基础
---

## 前言
SPA(Single Page Application)，即单页应用，想必现在的前端对其早已耳熟能详，今天就来说道说道，SPA的前世今生。

## 多页面应用
为什么先说多页面，因为传统网站都是多页面的，从大学第一次做网页，到工作使用JQ库，一个个html页面拼凑而成的网站都是多页面的。<br>
每一次页面跳转的时候，后台服务器都会给返回一个新的html文档，这种类型的网站也就是多页网站，也叫做多页应用。<br>
多页面跳转刷新所有资源，每个公共资源(js、css等)需选择性重新加载，常用于 app 或 客户端等。<br>
结构如图： 
![多页面应用结构视图](http://tvax2.sinaimg.cn/large/b535b7d3ly1g8747ayh6oj20go089gld.jpg)