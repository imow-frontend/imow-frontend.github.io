---
title: ES6中的箭头函数
date: 2019-11-28 15:35:25
categories:
tags: Javascript
author: 刘涛
---

## 前言



## 什么是箭头函数
ES6标准新增了一种新的函数：Arrow Function（箭头函数）。箭头函数相当于匿名函数，并且简化了函数定义。<br>
箭头函数的语法：
``` javascript 
(parameters) => { statements }
```

我们来举一个常规的例子
``` javascript 
// 使用常规语法定义函数
function funcName(params) {
   return params * 3;
 }
funcName(1);
// 3

// 使用箭头函数定义函数
var funcName = (params) => params * 3
funcName(1);
// 3
```

我们可以清晰得看到，只须一行代码就可以搞定一个函数，简化了函数定义，书写匿名函数的时候更加方便，在代码书写过程中多处可以使用到箭头函数。

### 语法解析
``` javascript
// 通用语法
(parameters) => { statements }
// 只有一个参数时，可以省略小括号
parameters => { statements }
// 没有参数时，简化为
() => {statements }
// 返回值只有一个表达式时，可以省略大括号
parameters => expression

//特殊例子，返回带有大括号的单表达式
x => { foo: x }  // error
x => ({ foo: x }) // 加上小括号，ok

```

## 箭头函数与普通函数的区别

### 箭头函数
``` javascript
  let fun = () => {
    console.log('lalalala');
}
```

### 普通函数
``` javascript
  function fun() {
    console.log('lalla');
}
}
```
箭头函数相当于匿名函数，并且简化了函数定义。箭头函数有两种格式，一种只包含一个表达式，连{ ... }和return都省略掉了。还有一种可以包含多条语句，这时候就不能省略{ ... }和return。



### 1. 箭头函数没有 prototype(原型),所以箭头函数本身没有 this
```javascript
  let a = () =>{};
  console.log(a.prototype); // undefined
```


### 2. 箭头函数的this指向在定义的时候继承自外层第一个普通函数的this。
下面例子中在一个函数中定义箭头函数，然后在另一个函数中执行箭头函数。
```javascript
  let a,
  barObj = { msg: 'bar的this指向' };
  fooObj = { msg: 'foo的this指向' };
  bar.call(barObj); // 将bar的this指向barObj
  foo.call(fooObj); // 将foo的this指向fooObj
    function foo() {
      a(); // 结果：{ msg: 'bar的this指向' }
    }
    function bar() {
     a = () => {
    console.log(this, 'this指向定义的时候外层第一个普通函数'); // 
  }; // 在bar中定义 this继承于bar函数的this指向
}
```

从上面的例子中可以得到两点<br>
**1. 箭头函数的this指向定义时所在的外层第一个普通函数，跟使用位置没有关系。**<br>
**2. 被继承的普通函数的this指向改变，箭头函数的this指向会跟着改变**<br>


### 3. 不能直接修改箭头函数的this指向
上个例子中的foo函数修改一下，尝试直接修改箭头函数的this指向。
``` javascript
  let fnObj = { msg: '尝试直接修改箭头函数的this指向' };
  function foo() {
  a.call(fnObj); // 结果：{ msg: 'bar的this指向' }
}
```
很明显，call显示绑定this指向失败了，包括aaply、bind都一样。
**所以箭头函数不能直接修改它的this指向**
幸运的是，我们可以通过间接的形式来修改箭头函数的指向：
**去修改被继承的普通函数的this指向，然后箭头函数的this指向也会跟着改变，**这在上一个例子中有演示。 
``` javascript
  bar.call(barObj); // 将bar普通函数的this指向barObj 然后内部的箭头函数也会指向barObj
```

### 4. 箭头函数是匿名函数，不能作为构造函数，不能使用`new`
``` javascript
  var B = ()=>{
    value:1;
  }
  var b = new B(); //TypeError: B is not a constructor
```
无论箭头函数的thsi指向哪里，使用new调用箭头函数都会报错，因为箭头函数没有`constructor`

### 5.箭头函数的this指向全局，使用arguments会报未声明的错误
如果箭头函数的this指向`window`(全局对象)使用arguments会报错，未声明`arguments`。
``` javascript
  let b = () => {
  console.log(arguments);
  };
  b(1, 2, 3, 4); // Uncaught ReferenceError: arguments is not defined
```
**箭头函数的this指向普通函数时,它的argumens继承于该普通函数**
上面是第一种情况：箭头函数的this指向全局对象，会报arguments未声明的错误。

第二种情况是：箭头函数的this如果是指向普通函数，它的`arguments`继承于该普通函数。
```javascript

function bar() {
  console.log(arguments); // ['外层第二个普通函数的参数']
  bb('外层第一个普通函数的参数');
  function bb() {
    console.log(arguments); // ["外层第一个普通函数的参数"]
    let a = () => {
      console.log(arguments, 'arguments继承this指向的那个普通函数'); // ["外层第一个普通函数的参数"]
    };
    a('箭头函数的参数'); // this指向bb
  }
}
bar('外层第二个普通函数的参数');
```
**rest参数获取函数的多余参数**
这是ES6的API，用于获取函数不定数量的参数数组，这个API是用来替代`arguments`的，API用法如下：
```javascript
    let a = (first, ...abc) => {
    console.log(first, abc); // 1 [2, 3, 4]
  };
  a(1, 2, 3, 4);
```
上面的例子展示了，获取函数除了第一个确定的参数，以及用一个变量接收其他剩余参数的示例。<br>
也可以直接接收函数的所有参数，rest参数的用法相对于`arguments`的优点：
1. 箭头函数和普通函数都可以使用。
2. 更加灵活，接收参数的数量完全自定义。
3. 可读性更好
4. rest是一个真正的数组，可以使用数组的API。
因为`arguments`是一个类数组的对象，有些人以为它是真正的数组，所以会出现以下场景：
```javascript
   arguments.push(0); // arguments.push is not a function
```
如上，如果我们需要使用数组的API，需要使用扩展符/Array.from来将它转换成真正的数组:
```javascript
   arguments = [...arguments]; 或者 ：arguments = Array.from(arguments);
```

**rest参数有两点需要注意：**
1. rest必须是函数的最后一位参数：
```javascript
   let a = (first, ...rest, three) => {
  console.log(first, rest,three); // 报错：Rest parameter must be last formal parameter
};
a(1, 2, 3, 4);
```
2. 函数的length属性，不包括 rest 参数
```javascript
(function(...a) {}).length  // 0
(function(a, ...b) {}).length  // 1
```

### 6.箭头函数不支持`new.target`
`new.target`是ES6新引入的属性，普通函数如果通过`new`调用，``new.target`会返回该函数的引用。<br>
此属性主要：用于确定构造函数是否为new调用的。
1. 箭头函数的this指向全局对象，在箭头函数中使用箭头函数会报错
``` javascript
  let a = () => {
  console.log(new.target); // 报错：new.target 不允许在这里使用
  };
  a();
```
2. 箭头函数的this指向普通函数，它的new.target就是指向该普通函数的引用。
 ``` javascript
  new bb();
  function bb() {
  let a = () => {
    console.log(new.target); // 指向函数bb：function bb(){...}
  };
  a();
}
```

## 使用箭头函数需要注意什么

### 1. 在对象上定义函数
 ``` javascript
 const test = {
    array: [1, 2, 3],
    sum: () => {
        console.log(this === window); // => true
        return this.array.reduce((result, item) => result + item);
    }
};
test.sum();
// TypeError: Cannot read property 'reduce' of undefined
```
原因就是，箭头函数没有它自己的this值，箭头函数内的this值继承自外围作用域。

对象方法内的this指向调用这个方法的对象，如果使用箭头函数，this和对象方法在调用的时候所处环境的this值一致。因为 test.sum()是在全局环境下进行调用，此时this指向全局。<br>
解决方法也很简单，使用函数表达式或者方法简写（ES6 中已经支持）来定义方法，这样能确保 this 是在运行时是由包含它的上下文决定的。

``` javascript
 const test = {
    array: [1, 2, 3],
    sum() {
        console.log(this === test); // => true
        return this.array.reduce((result, item) => result + item);
    }
};
test.sum();
// 6
```

### 2. 定义原型方法
在对象原型上定义函数也是遵循着一样的规则
``` javascript
 function Person(name) {
    this.name = name;
}

Person.prototype.sayName = () => {
    console.log(this === window); // => true
    return this.name;
};

const cat = new Person('Mew');
cat.sayName(); // => undefined
```
使用传统的函数表达式就能解决问题
``` javascript
 function Person(name) {
    this.name = name;
}

Person.prototype.sayName = function() {
    console.log(this === Person); // => true
    return this.name;
};

const cat = new Person('Mew');
cat.sayName(); // => Mew
```

### 3. 定义事件回调函数
this是JS中非常强大的特点，他让函数可以根据其调用方式动态的改变上下文，然后箭头函数直接在声明时就绑定了this对象，所以不再是动态的。

在客户端，在DOM元素上绑定事件监听函数是非常普遍的行为，在DOM事件被触发时，回调函数中的this指向该DOM，但是，箭头函数在声明的时候就绑定了执行上下文，要动态改变上下文是不可能的，在需要动态上下文的时候它的弊端就凸显出来:
``` javascript
const button = document.getElementById('myButton');
button.addEventListener('click', () => {
    console.log(this === window); // => true
    this.innerHTML = 'Clicked button';
});

```
因为这个回调的箭头函数是在全局上下文中被定义的，所以他的this是window。换句话说就是，箭头函数预定义的上下文是不能被修改的，这样 this.innerHTML 就等价于 window.innerHTML，而后者是没有任何意义的。

使用函数表达式就可以在运行时动态的改变 this：


``` javascript
const button = document.getElementById('myButton');
button.addEventListener('click', function() {
    console.log(this === button); // => true
    this.innerHTML = 'Clicked button';
});

```

### 4. 定义构造函数
如果使用箭头函数会报错。显然，箭头函数是不能用来做构造函数。
``` javascript
const Message = (text) => {
    this.text = text;
};
const helloMessage = new Message('Hello World!');
// Throws "TypeError: Message is not a constructor"
```
理论上来说也是不能这么做的，因为箭头函数在创建时this对象就绑定了，更不会指向对象实例。

### 5.太简短的（难以理解）函数
箭头函数可以让语句写的非常的简洁，但是一个真实的项目，一般由多个开发者共同协作完成，箭头函数有时候并不会让人很好的理解：

``` javascript
const multiply = (a, b) => b === undefined ? b => a * b : a * b;
const double = multiply(2);
double(3); // => 6
multiply(2, 3); // => 6
```
代码看起来很简短，但大多数人第一眼看上去可能无法立即搞清楚它干了什么。

这个函数的作用就是当只有一个参数a时，返回接受一个参数b返回a*b的函数，接收两个参数时直接返回乘积。

为了让这个函数更好的让人理解，我们可以为这个箭头函数加一对花括号，并加上return语句，或者直接使用函数表达式：


``` javascript
function multiply(a, b) {
    if (b === undefined) {
        return function(b) {
            return a * b;
        }
    }
    return a * b;
}

const double = multiply(2);
double(3); // => 6
multiply(2, 3); // => 6
```

## 总结
毫无疑问，箭头函数带来了很多便利。恰当的使用箭头函数可以让我们避免使用早期的.bind()函数或者需要固定上下文的地方并且让代码更加简洁。

箭头函数也有一些不便利的地方。我们在需要动态上下文的地方不能使用箭头函数：定义对象方法、定义原型方法、定义构造函数、定义事件回调函数。在其他情况下，请尽情的使用箭头函数。

