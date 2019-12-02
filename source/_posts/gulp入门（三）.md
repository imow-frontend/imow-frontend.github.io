---
title: gulp入门(三)
date: 2019-11-13 13:52:14
author: 胡豪
categories:
  - gulp
tags:
  - gulp
---

## gulp3改造gulp4
gulp3与gulp4的写法存在区别，此篇用来对比整个项目gulpfile.js文件的异同。
如果你安装的是gulp3，想升级使用gulp4，可以使用命令`npm install gulp@4.0`

如果你安装的是gulp4，想h回退使用gulp3，可以使用命令`npm install gulp@3.9.1 `

## 一下是简单的项目gulpfile.js文件实例
### gulp@3.9.1版本
```js
const gulp = require("gulp");//全局gulp
const uglify = require("gulp-uglify"); //压缩js
const gulpif = require("gulp-if");//if条件
const cssUglify = require('gulp-minify-css');//压缩css
const gulpclean = require("gulp-clean");//清除文件


//过滤条件
var conditionJs = function(f) { 
  if (f.path.indexOf(".min.js", f.path.length - ".min.js".length) !== -1) {
    return false;
  } 
  return true;
};
var conditionCss = function(f) { 
  if (f.path.indexOf(".min.css", f.path.length - ".min.css".length) !== -1) {
    return false;
  } 
  return true;
};


gulp.task('clean' , function(){
  gulp.src([
     //'dist', //删除dist整个文件夹
     'dist/*', //删除dist下的test写任意子文件夹里的文件
     '!package.json'  //不删除package.json文件
    ] ).pipe(gulpclean());
});

gulp.task('html', function () {
  gulp.src(["src/*.html","src/favicon.ico"])
      .pipe(gulp.dest("dist/"))
})

gulp.task('css',function(){
  gulp.src('src/css/*.css')
      .pipe(gulpif(conditionCss, cssUglify())) 
      .pipe(gulp.dest('dist/css'))
})
gulp.task('cssmin',['css'], function () {
  gulp.src('src/css/font/*')
      .pipe(gulp.dest("dist/css/font"));
})

//压缩js文件
gulp.task("js", function() {
  gulp.src("src/js/*.js")
      .pipe(gulpif(conditionJs, uglify()))
      .pipe(gulp.dest("dist/js"));
});
//拷贝不需要处理js的文件
gulp.task('jsmin',['js'], function () {
  gulp.src('src/js/layer/**')
      .pipe(gulp.dest("dist/js/layer"));
})

gulp.task('copyImage',['js'], function () {
  gulp.src('src/img/**/* ') 
      .pipe(gulp.dest("dist/img"));
})

gulp.task("build", ["html","cssmin","jsmin","copyImage"]);
```

### gulp@4版本
```js
const gulp = require("gulp");//全局gulp
const uglify = require("gulp-uglify"); //压缩js
const gulpif = require("gulp-if");//if条件
const cssUglify = require('gulp-minify-css');//压缩css
const gulpclean = require("gulp-clean");//清除文件

//过滤条件
var conditionJs = function(f) { 
  if (f.path.indexOf(".min.js", f.path.length - ".min.js".length) !== -1) {
    return false;
  } 
  return true;
};
var conditionCss = function(f) { 
  if (f.path.indexOf(".min.css", f.path.length - ".min.css".length) !== -1) {
    return false;
  } 
  return true;
};


//执行任务
gulp.task('clean' , function(){
  return gulp.src([
     //'dist', //删除dist整个文件夹
     'dist/*', //删除dist下的test写任意子文件夹里的文件
     '!package.json'  //不删除package.json文件
    ] ).pipe(gulpclean());
});

gulp.task('html', function () {
  return gulp.src(["src/*.html","src/favicon.ico"])
      .pipe(gulp.dest("dist/"))
})

gulp.task('css',function(){
  return gulp.src('src/css/*.css')
      .pipe(gulpif(conditionCss, cssUglify())) 
      .pipe(gulp.dest('dist/css'))
})
gulp.task('cssmin',gulp.series('css', function () {
  return gulp.src('src/css/font/*')
      .pipe(gulp.dest("dist/css/font"));
}))

//压缩js文件
gulp.task("js", function() { 
  return gulp.src("src/js/*.js")
      .pipe(gulpif(conditionJs, uglify()))
      .pipe(gulp.dest("dist/js"));
});
//拷贝不需要处理js的文件
gulp.task('jsmin',gulp.series('js', function () {
  return gulp.src('src/js/layer/**')
      .pipe(gulp.dest("dist/js/layer"));
}))

gulp.task('copyImage', function () {
  return gulp.src('src/img/**/* ') 
      .pipe(gulp.dest("dist/img"));
})

gulp.task('build',gulp.series('clean',gulp.parallel('html','cssmin','jsmin','copyImage')));
```

## 总结
gulp系列到此为止，介绍的也是一些常规的基础用法，万丈高楼平地起，高楼怎么造就看砖头怎么码了。有兴趣的去看看前辈grunt [click to link](https://www.gruntjs.net/)