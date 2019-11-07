---
title: gulp入门(一)
date: 2019-11-07 13:31:50
author: 胡豪
categories:
  - gulp
tags:
  - gulp
---

## 前言
说到前端常见的构建工具，有Webpack/Grunt/Gulp  
这里简述一下它们都是干什么的：  
Grunt 和 Gulp 是非常相似的东西，能实现的功能是一样的，当然你可能 Gulp 听的 Grunt 多，通俗的讲，可以把 Gulp 看作 Grunt 的加强版。相对于 Grunt，Gulp增加了监听文件、读写文件、流式处理的功能。  
Webpack 是一个打包模块化 JavaScript 的工具，在 Webpack 里一切文件皆模块(JavaScript、CSS、SCSS、图片、模板)，通过 Loader 转换文件，通过 Plugin 注入钩子，最后输出由多个模块组合成的文件。经过 Webpack 的处理，最终会输出浏览器能使用的静态资源。使用Vue的小伙伴再熟悉不过，Webpack 专注于构建模块化项目。Webpack的缺点也很明显是只能用于采用模块化开发的项目。  
今天重点讲gulp；


## Gulp是什么？

Gulp是前端自动化的工具，它能用来做很多很多繁琐的事情，大大便利了前端开发工作流程

1. 搭建web服务器
2. 使用预处理器Sass，Less
3. 压缩优化，可以压缩JS CSS Html 图片
4. 自动将更新变化的代码实时显示在浏览器
5. 前端测试  
...... 

## 快速入门
Gulp 是一个基于流的自动化构建工具。 除了可以管理和执行任务，还支持监听文件、读写文件。Gulp 被设计得非常简单，只通过下面5种个方法就可以胜任几乎所有构建场景：

- 通过 gulp.task 注册一个任务；
- 通过 gulp.run 执行任务；
- 通过 gulp.watch 监听文件变化；
- 通过 gulp.src 读取文件；
- 通过 gulp.dest 写文件。

## 如何写
首先创建 gulpfile 文件  
利用任何文本编辑器在项目大的根目录下创建一个名为 gulpfile.js 的文件  
各个API就不讲了，官网都有，[Gulp官网直通车](https://www.gulpjs.com.cn/docs/api/concepts/) 


1. 项目初始化 `npm init` 
2. 安装全局gulp 
```js
npm install gulp -g //全局安装
npm install gulp --save-dev //本地安装并加入package.json
```
3. 这里用了一个gulp插件 gulp-uglify，安装 `npm i gulp-uglify -D`
4. 定义一个gulp任务`gulp.task(name[,deps],fn)`   

就先写个压缩 js 吧
```js
const gulp = require("gulp");//全局gulp
const uglify = require("gulp-uglify"); //压缩js

  //压缩js文件
  gulp.task("script", function() {
    //找到文件
    gulp
      .src("js/*.js")
      //压缩文件
      .pipe(uglify())
      //保存压缩后的文件
      .pipe(gulp.dest("dist/js"));
  });
```

## 实践过程遇到的问题
项目中js文件中不仅有我自己写的js，还有各种插件，已经压缩过的min.js文件，如上执行gulp就会报错

- 解决方法一：先把压缩过的js文件提前剪切到dist/js文件下;执行完gulp要记得吧dist里的js覆盖原项目js文件夹；当然这种手动的方法比较麻烦还有点傻憨，使用gulp插件自动化处理更加优雅

- 解决方法二：添加gulp-if判断  
  期间我还使用过.src() 中的 ! 取非判断，但这个非还不够只能，只能屏蔽某个文件夹或者单独的js，要一个个列出来，也不够方便

  ```js
  var condition = function(f) {
    //这里网上用的是endsWith()方法虽然方便，但是是es6的方法，es5下使用indexOf实现
    if (f.path.indexOf(".min.js", f.path.length - ".min.js".length) !== -1) { 
      return false;
    } 
    return true;
  };

  //压缩js文件
  gulp.task("jsmin", function() {
    //找到文件
    return gulp.src("src/js/*.js") 
      //压缩文件
      .pipe(gulpif(condition, uglify()))
      //保存压缩后的文件 
      .pipe(gulp.dest("dist/js"));
  });
  ```
