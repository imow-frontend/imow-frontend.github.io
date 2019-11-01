---
title: 如何理解ES6中Promise
date: 2019-10-22 18:46:32
tags: Javascript
author: 刘涛
---


## 什么是Promise
/**Promise**/是ES6中的一个内置的对象（实际上是一个构造函数，通过这个构造函数我们可以创建一个/**Promise**/对象），它的出现是为了更好地解决JavaScript中异步编程的问题，传统的异步编程最大的特点就是地狱般的回调嵌套，一旦嵌套次数过多，就很容易使我们的代码难以理解和维护。而/**Promise**/则可以让我们通过链式调用的方法去解决回调嵌套的问题，使我们的代码更容易理解和维护，而且/**Promise**/还增加了许多有用的特性，让我们处理异步编程得心应手。/**Promise**/的英文意思是承诺。

## Promise的特点
 * /**Promise**/有三种状态：Pending（进行中）、Resolved（已完成）、Rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。
 * /**Promise**/一共有三种状态，但是他们之间是如何转化的呢？ 其一： 从Pending（进行中）到Resolved（完成）。其二： 从Pending（进行中）到Rejected（已失败）。 且只有这两种形式的转变，即使是Promise对象的结果也无力回天了。
  
  但是/**Promise**/并不是完美无缺的，也是有一定的缺点的，如在Pengding时，我们无法取消状态，另外，我们没法判断Pending究竟是刚刚开始的Pending还是即将要完成的Pending。



  ## 使用Promise
  直接上码
  ```JavaScript
    var promise = new Promise(function(resolve, reject) {
    // ... some code
    
    if (/* 异步操作成功 */){
    resolve(value);
    } else {
    reject(error);
    }
    });
  ```
  其中，由于Promise是构造函数，所以我们使用new来创建一个对象即可， 值得注意的是：function(resolve, reject){}这个函数是必须要写的，否则就不是Promise了。

  这个函数是为了初始化Promise对象，其中这个函数接受了两个函数作为参数， 如果在函数体中我们执行了resolve函数，那么Promise的状态就会由pending转化为resolved（或fullfilled，两者是相同的），类似的，如果我们执行了reject函数，pending就会变成 rejected。  
  注意： 这个例子的if语句不是必要的，这里想要表达的意思是如果得到了异步成功的相关结果，我们就将调用resolve，将pending转化为resolved，并且将异步成功后的value值传递进去以便后面使用，说是以便后面使用是因为Promise还有一个then()方法，即可以定义在异步成功（或失败）之后需要做的事情。这也就是resolve和reject内置函数存在的意义了。

　当创建了这个Promise对象之后，就一定会有一个结果了，但是成功和失败还是不确定的，我们需要根据判断结果的成功和失败来做不同的事情，于是用到了then()方法，如下所示：
 ```JavaScript
    promise.then(function(value) {
 // success
}, function(error) {
 // failure
});
  ```
  下面这个例子做了详尽的说明
  ```JavaScript
    var promise = new Promise(function (resolve, reject) {
      console.log("good");
      var a = 10;
      var b = a + 25;
      if ( b === 35 ) {
      // 一旦异步执行成功，我们就调用内置的 resolve函数，将pending状态转化为resolved，并且传入我们希望传出的执行成功后的结果。
      // 注意： 这里一旦状态转变，那么后面就一定会调用then方法中的第一个参数的函数，然后将我们传入给resolve的参数传给then方法中的第一个方法作为参数，我们就可以在then的第一个方法中使用了。
      resolve(b);
      } else {
      reject("异步执行失败");
      }
    });
    promise.then(function (value) {
      console.log("异步执行成功，输出执行结果：" + value);
    }, function (error) {
      console.log("异步执行失败，输出执行结果：" + error);
    });
  ```


## Promise的方法
###  1. Promise.prototype.then()
上面其实已经介绍了then()方法了，而这里需要强调的有两点。<br>
第一：then()方法是Promise原型上定义的方法。<br>
第二：then()方法支持链式调用，上一个then()方法调用后返回的结果回传给下一个then()方法中。

第一：我们在chrome中输入Promise.prototype可以看到下面的例子：
![Promise.prototype.then()](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/promise/promise01.jpg)
可以看出在Promise的原型中确实是由then方法的。（注：比如我们想看Array这个内置对象有哪些方法，我们就可以直接在chrome中输入Array.prototype，然后就可以看到对应方法的列表了）

第二： then()的作用是为Promise实例添加状态改变时的回调函数。then方法的第一个参数是Resolved状态的回调函数，第二个参数（可选）是Rejected状态的回调函数。
then()由于支持链式调用，所以也可以这样写成这样：
 ```JavaScript
    getJSON("/posts.json").then(function(json) {
      return json.post;
    }).then(function(post) {
    // ...
  });
  ```
  第一个回调函数完成以后，会将返回结果作为参数，传入第二个回调函数。

  再来看看下面这个例子：
  ```JavaScript
    getJSON("/post/1.json").then(function(post) {
      return getJSON(post.commentURL);
    }).then(function funcA(comments) {
      console.log("Resolved: ", comments);
    }, function funcB(err){
      console.log("Rejected: ", err);
    });
  ```
  即第一个then又返回了一个promise，如何这个promise的状态变成了 Resolved，那么就会执行第二个then的第一个函数， 如果变成了 Rejected，就会执行第二个第二个函数。


  ### 2.Promise.prototype.catch()
  Promise.prototype.catch()方法实际上是then(null, rejection)方法的别名， 这里使用catch（）纯粹是为了便于使用和理解。
  ```JavaScript
    getJSON('/posts.json').then(function(posts) {
  // ...
  }).catch(function(error) {
  // 处理 getJSON 和 前一个回调函数运行时发生的错误
  console.log('发生错误！', error);
});
  ```
  在之前的例子中，我们讲解then()方法接受两个参数，第一个参数是pending变成resolved之后执行的函数，它是必选的； 第二个参数是pending变成rejected之后执行的函数，它是可选的。

  我们建议，最后不要使用第二个参数，取而代之我们最好使用catch（），如下所示：
   ```JavaScript
  // bad
    promise
    .then(function(data) {
    // success
    }, function(err) {
    // error
    });
    
    // good
    promise
    .then(function(data) { //cb
    // success
    })
    .catch(function(err) {
    // error
    });
  ```
  值得注意的是：catch()方法返回的还是一个Promise对象，我们可以在后面继续使用then进行链式调用。


  ### 3.Promise.all()
  Promise.all()方法用于将多个Promise实例包装成一个新的Promise实例，如下所示：
  ```JavaScript
   var p = Promise.all([p1, p2, p3]);
});
  ```
  其中的p1，p2，p3都是Promise对象实例，如果不是，就会先调用下面讲到的Promise.resolve方法，将参数转为Promise实例，再进一步处理。
  p的状态由p1，p2，p3决定，分成下面的两种情况：
  * 只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。
  * 只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。
  
   让我们来看看具体的例子：
   ```JavaScript
    // 生成一个Promise对象的数组
    var promises = [2, 3, 5, 7, 11, 13].map(function (id) {
    return getJSON("/post/" + id + ".json");
    });
    
    Promise.all(promises).then(function (posts) {
    // ...
    }).catch(function(reason){
    // ...
  ```
  在这个例子中，通过数组的map生成了6个Promise对象，然后作为参数传递给了Promise.all() 只有这6个Promise对象最终返回的都是 resolved时， 才会调用Promise.all()后面then（）方法。

  ### 4.Promise.resolve()
  如果我们希望将一个现有对象转化为Promise对象，我们就可以使用Promise.resolve()方法。根据参数的不同可以分为4种情况：
  1. 参数是一个Promise实例<br>
   如果参数是Promise实例，那么Promise.resolve将不做任何修改、原封不动地返回这个实例。
  2. 参数是一个thenable对象<br>
   如果参数是一个thenable对象，即具有then()方法的对象，那么Promise.resolve()就会将该对象立即转化成 Promise对象那个，并立即执行then方法。
  3. 参数不是具有then方法的对象，或根本就不是对象<br>
   如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的Promise对象，状态为Resolved。
  4. 不带有任何参数<br>

## 总结
![总结](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/promise/%E6%80%BB%E7%BB%93.jpg)