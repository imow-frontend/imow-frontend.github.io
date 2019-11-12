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

### 简单语法
1. gulp.task(name[,deps],fn)
说明：定义一个gulp任务
name: 类型(必填)：String 指定任务的名称（不应该有空格）
deps:类型(可选)：StringArray，该任务依赖的任务（执行name任务要先去执行的任务）
```js
gulp.task('A' , function(){
   console.log('A') 
});
gulp.task('B' , ['A'] , function(){ //运行B之前先去运行A
   console.log('B')
});
```

2. gulp.src(globs[, options])
说明：src方法指定需要处理的源文件路径，返回当前文件流至可用插件  

globs: 类型(必填)：String/StringArray  需要处理的源文件匹配符路径  

通配符路径匹配示例：
　　“src/a.js”：指定具体文件；  
　　“*”：匹配所有文件    例：src/*.js(包含src下的所有js文件)；  
　　“**”：匹配0个或多个子文件夹    例：src/**/*.js(包含src的0个或多个子文件夹下的js文件)；  
　　“{}”：匹配多个属性    例：src/{a,b}.js(包含a.js和b.js文件)  src/*.{jpg,png,gif}(src下的所有jpg/png/gif文件)；  
　　“!”：排除文件    例：!src/a.js(不包含src下的a.js文件)；  

options:类型(可选)：Object 三个属性 buffer read base  
　　options.buffer：类型：Boolean  默认：true 设置为false，将返回file.content的流并且不缓冲文件，处理大文件时非常有用；  
　　options.read：  类型：Boolean  默认：true 设置false，将不执行读取文件操作，返回null；  
　　options.base：  类型：String  设置输出路径以某个路径的某个组成部分为基础向后拼接
```js
gulp.src('client/js/**/*.js') 
  .pipe(minify())
  .pipe(gulp.dest('build'));   
 
gulp.src('client/js/**/*.js', { base: 'client' })
  .pipe(minify())
  .pipe(gulp.dest('build'));   
```

3. gulp.dest(path[,options])  
说明：watch方法用于监听文件变化，一被变化就执行指定任务  
glob：  需要处理的源文件匹配符路径。类型(必填)：String or StringArray；  
opts：  类型(可选)：Object 具体参看https://github.com/shama/gaze；  
tasks：  类型(必填)：StringArray 需要执行的任务的名称数组；  
cb(event)：  类型(可选)：Function 每个文件变化执行的回调函数；
```js
.pipe(gulp.dest('build')); 
```

4. gulp.watch(glob[,opts],tasks) or gulp.task(glob [,opts ,cd])  
说明：watch方法用于监听文件变化，一被变化就执行指定任务  
glob：  需要处理的源文件匹配符路径。类型(必填)：String or StringArray；  
opts：  类型(可选)：Object 具体参看https://github.com/shama/gaze；  
tasks：  类型(必填)：StringArray 需要执行的任务的名称数组；  
cb(event)：  类型(可选)：Function 每个文件变化执行的回调函数；


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
5. 运行gulp
gulpfile统计目录下终端运行`gulp.script`



