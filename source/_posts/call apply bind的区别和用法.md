---
layout: call apply bind
title: call apply bind的区别和用法
date: 2019-08-22 08:49:23
tags:
---
## 前言

在JavaScript中，call、apply和bind是Function对象自带的三个方法，它们最主要的作用是改变 this 的指向。在平时的工作中，除了在写一些基础类，或者公用库方法的时候会用到它们，其他时候 call 和 apply 的应用场景并不多。


## call和applay的共同点

它们的共同点是，都能够改变函数执行时的上下文，将一个对象的方法交给另一个对象来执行，并且是立即执行的。

为何要改变执行上下文？举一个生活中的小例子：平时没时间做饭的我，周末想给孩子炖个腌笃鲜尝尝。但是没有适合的锅，而我又不想出去买。所以就问邻居借了一个锅来用，这样既达到了目的，又节省了开支，一举两得。

改变执行上下文也是一样的，A 对象有一个方法，而 B 对象因为某种原因，也需要用到同样的方法，那么这时候我们是单独为 B 对象扩展一个方法呢，还是借用一下 A 对象的方法呢？当然是借用 A 对象的啦，既完成了需求，又减少了内存的占用。

另外，它们的写法也很类似，**调用 call 和 apply 的对象，必须是一个函数 Function。** 接下来，就会说到具体的写法，那也是它们区别的主要体现。


## call和apply的区别

它们的区别，主要体现在参数的写法上。先来看一下它们各自的具体写法。

### call的写法

 ``` javacript
 Function.call(obj,[param1[,param2[,…[,paramN]]]])
```

需要注意以下几点：

1. 调用 call 的对象，必须是个函数 Function。<br>
2. call 的第一个参数，是一个对象。 Function 的调用者，将会指向这个对象。如果不传，则默认为全局对象 window。<br>
3. 第二个参数开始，可以接收任意个参数。每个参数会映射到相应位置的 Function 的参数上。但是如果将所有的参数作为数组传入，它们会作为一个整体映射到 Function 对应的第一个参数上，之后参数都为空

 ``` javacript
 function func (a,b,c) {}

func.call(obj, 1,2,3)
// func 接收到的参数实际上是 1,2,3

func.call(obj, [1,2,3])
// func 接收到的参数实际上是 [1,2,3],undefined,undefined
```

### applay的写法

 ``` javacript
 Function.apply(obj[,argArray])
```

需要注意一下几点
1. 它的调用者必须是函数 Function，并且只接收两个参数，第一个参数的规则与 call 一致。<br>
2. 第二个参数，必须是数组或者类数组，它们会被转换成类数组，传入 Function 中，并且会被映射到 Function 对应的参数上。这也是 call 和 apply 之间，很重要的一个区别。

 ``` javacript
func.apply(obj, [1,2,3])
// func 接收到的参数实际上是 1,2,3

func.apply(obj, {
    0: 1,
    1: 2,
    2: 3,
    length: 3
})
// func 接收到的参数实际上是 1,2,3
```


## bind的使用
bind()函数回创建一个新的函数(称为绑定函数)

* bind是ES5新增的一个方法
* 传参和call或apply类似
* 不会执行对应的函数，call或apply会自动执行对应的函数
* 返回对函数的引用
  

### bind的写法

 ``` javacript
 fun.bind(thisArg[, arg1[, arg2[, ...]]])
```

bind 方法 与 apply 和 call 比较类似，也能改变函数体内的 this 指向。不同的是，**bind 方法的返回值是函数，并且需要稍后调用，才会执行。**而 apply 和 call 则是立即调用。

举例说明：
 ``` javacript
 function add (a, b) {
    return a + b;
}

function sub (a, b) {
    return a - b;
}

add.bind(sub, 5, 3); // 这时，并不会返回 8
add.bind(sub, 5, 3)(); // 调用后，返回 8
```
如果 bind 的第一个参数是 null 或者 undefined，this 就指向全局对象 window。


## 应用场景：继承
 ``` javacript
 function Animal(name,weight){
   this.name = name;
   this.weight = weight;
}
function Cat(){
    // 在call中将this作为thisArgs参数传递
    // Animal方法中的this就指向了Cat中的this
    // 所以Animal中的this指向的就是cat对象
    // 在Animal中定义了name和weight属性，就相当于在cat中定义了这些属性
    // cat对象便拥有了Animal中定义的属性，从而达到了继承的目的
    Animal.call(this,'cat','50');
    //Animal.apply(this,['cat','50']);
    this.say = function(){
       console.log("I am " + this.name+",my weight is " + this.weight);
    }
}
//当通过new运算符产生了cat时，Cat中的this就指向了cat对象
var cat = new Cat();
cat.say();
//输出=> I am cat,my weight is 50
```

## 原型扩展
在原型函数上扩展和自定义方法，从而不污染原生函数。例如：我们在 Array 上扩展一个 forEach
``` javacript
function test(){
    // 检测arguments是否为Array的实例
    console.log(
        arguments instanceof Array, //false
        Array.isArray(arguments)  //false
    );
    // 判断arguments是否有forEach方法
    console.log(arguments.forEach); 
    // undefined
    // 将数组中的forEach应用到arguments上
    Array.prototype.forEach.call(arguments,function(item){
        console.log(item); // 1 2 3 4
    });
}
test(1,2,3,4);
```

## 总结
call 和 apply 的主要作用，是改变对象的执行上下文，并且是立即执行的。它们在参数上的写法略有区别。

bind 也能改变对象的执行上下文，它与 call 和 apply 不同的是，返回值是一个函数，并且需要稍后再调用一下，才会执行。

关于 call 和 apply 的便捷记忆法：
```
猫吃鱼，狗吃肉，奥特曼打小怪兽。

有天狗想吃鱼了

猫.吃鱼.call(狗，鱼)

狗就吃到鱼了

猫成精了，想打怪兽

奥特曼.打小怪兽.call(猫，小怪兽)

猫也可以打小怪兽了
```