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

- ### gulp4与gulp3

  #### gulp3的写法运行遇到运行报错  
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

  #### 关于gulp3与gulp4版本对于写串行方式运行任务也有不同 

  gulp3，数组方式，例如：
  ```js
  gulp.task("build", ["html","cssmin","jsmin","copyImage"]);
  ``` 
  和
  ```js
  gulp.task('jsmin',['js'], function () {
    gulp.src('src/js/layer/**')
        .pipe(gulp.dest("dist/js/layer"));
  }))
    ```
  gulp4，需用`gulp.series`，例如： 
  ```js
  gulp.task('build',gulp.series(gulp.parallel('html','cssmin','jsmin','copyImage')));
  ```
  和
  ```js
  gulp.task('jsmin',gulp.series('js', function () {
    return gulp.src('src/js/layer/**')
        .pipe(gulp.dest("dist/js/layer"));
  }))
  ```
- ## 压缩图片报错
  一开始怎么试都报错，以为自己代码写错了，后面网上找了半天代码应该没有问题
  ```js
  //图片压缩
  gulp.task("imagemin",function(){
    return gulp.src("src/img/**/*.{png,jpg,gif,ico}")
        .pipe(imagemin())
        .on('error', function(err){ // 报错防止中断
          console.error(err)
          this.emit('end');
        })
        .pipe(gulp.dest("dist/img"));  //放入到dist目录下面的images文件
  })
  ```
  这个时候我想代码没问题，难道是依赖出问题了，我就把node_modules全删了再装一遍，图片压缩就不报错了，可以输出压缩好的图片文件。  