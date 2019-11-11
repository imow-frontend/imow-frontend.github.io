---
title: gulp入门(二)
date: 2019-11-11 16:54:02
author: 胡豪
categories:
  - gulp
tags:
  - gulp
---

## 实践过程遇到的问题

- ### 压缩js 
  gulp 压缩遇到已经压缩过的 min.js 文件无法再继续压缩会报错跳除压缩程序;  
  解决方法一：先把压缩过的js文件提前剪切到dist/js文件下;执行完gulp要记得吧dist里的js覆盖原项目js文件夹  
  当然这种手动的方法比较麻烦，使用gulp插件自动化处理更加优雅
  解决方法二：添加gulp-if判断
  ```js
  var condition = function(f) {
    //这里网上用的是endsWith()方法虽然方便，但是是es6的方法，es5下使用indexOf也同样可以实现
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
  * 知识补充：
  includes()：返回布尔值，表示是否找到了参数字符串；
  startsWith()：返回布尔值，表示参数字符串是否在查找字符串的头部；
  endsWith()：返回布尔值，表示参数字符串是否在查找字符串的尾部。
  ```js
  let str = 'Hello world!';
 
  str.startsWith('Hello') // true
  str.endsWith('!') // true
  str.includes('o') // true
  ```
  同时这三个方法都支持第二个参数，表示开始搜索的位置。endsWith 的行为与其他两个方法有所不同，它针对的是前n个字符,其他两个方法都是针对从第n个位置（不包含n）直到字符串结束。
  ```js
  let str = 'Hello world!';
 
  str.startsWith('world', 6) // true
  str.endsWith('Hello', 5) // true
  str.includes('Hello', 6) // false
  ```

- ### 遇到运行报错

  ```js
    The following tasks did not complete: script
    Did you forget to signal async completion?
  ```
  主要原因是一开始装的gulp是gulp4版本，gulp3版本使用原代码就行

  原代码：

  ```js
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

  解决方法一：

  ```js
  //压缩js文件
  gulp.task("script", done => {
    //找到文件
    gulp 
      .src("js/*.js")
      //压缩文件
      .pipe(uglify())
      //保存压缩后的文件
      .pipe(gulp.dest("dist/js"));
    done();
  });
  ```

  解决方法二：

  ```js
  //压缩js文件
  gulp.task("script", function() {
    //找到文件
    return gulp .src("js/*.js")
      //压缩文件
      .pipe(uglify())
      //保存压缩后的文件
      .pipe(gulp.dest("dist/js")); 
  });
  ```

- ### 压缩图片报错
待解决。。。
暂时拷贝所有原图片  