---
title: javascript中的面向对象开发
date: 2020-02-17 14:06:43
author: 胡豪
categories: 
- JavaScript 
tags:
- js 
---

# js面向对象开发

## 什么是对象

"无序属性的集合，其属性可以包括基本值、对象或者函数"，对象是一组没有特定顺序的的值。
对象的没个属性或方法都有一个俄名字，每个名字都映射到一个值。



## 什么是面向对象编程？

面向对象编程的基本思路完全不同。其背后的思想是：你将所需要做的功能抽象成一个“对象”，然后一遍遍地调用这个对象来完成你想要的功能。

每次调用对象时，你必须创建对象，确保对象的存在，然后为了使用对象所带的功能你需要设置它的属性，这些功能称为“方法（methods）”。



## 为什么我们需要在 JavaScript 中实现 OOP？




## 面向对象的特点

- 封装

对于一些功能相同或者相似的代码，我们可以放到一个函数中去，多次用到此功能时，我们只需要调用即可，无需多次重写。

创造对象的几种模式：单例模式，工厂模式，构造函数模式,原型模式等。

- 继承

子类可以继承父类的属性和方法

- 多态(重载和重写)

重载：重载就是函数方法名相同，参数不同

重写：子类可以改写父类的属性和方法。

Js中存在多态，但是Js中不存在函数的重载，因为函数名相同的话，后面的一定会把前面的覆盖，但Js中存在重写，所以Js中的多态主要是指重写。
但是也存在一种既不是重写却类似重载的Js多态方式,我们可以通过判断函数的参数的不同来实现不同的功能来模拟重载，就是我们调用函数时根据传入不同的参数实现不同的功能，如下：
```js
function sum(num){
  if(typeof num === "undefined"){
    return 0;
  }return num;
}
var n1 = sum(10);
console.log(n1);

var n2 = sum();
console.log(n2);
```
当传入空参数的时候，num为undefined所以会返回0，如果不是空参数的时候，就返回num。







## 创建对象的几种模式



一般创建对象是用以下两种方式

new object创建对象：
```js
var Person = new Object();
Person.name = "张三";
Person.age = "18";
Person.job = "123";
```
或者，对象字面量的方式：
```js
var Person = {
  name:"lisi",
  age:"18",
  job:"123"
}
```





### 单例模式

一个人写代码

```js
var a = 1;
function getNum(){
  return 1;
}
```

两个人，一起写同一个js。
假设，一天小明发现自己写的getNum方法出问题了，原来是小华写的js中也有个getNum的函数，代码合并后把他的覆盖掉了，于是便找小华理论去，经过一番妥协后，两人都把自己的代码改了改。

```js
var xiaoming = {
  num:1,
  getNum:function(){
    return 1;
  }
}

var xiaohua = {
  num:2,
  getNum: function(){
    return 2;
  }
}

console.log(xiaoming.num);
console.log(xiaoming.getNum());
console.log(xiaohua.num);
console.log(xiaohua.getNum());
```

这就是我们所谓的单例模式(命名空间)

我们把描述同一个事物的方法或者属性放到同一个对象里，不同事物之间的方法或者属性名相同相互也不会发生冲突。

#### 单例模式注意点：

当在一个命名空间A中调用别的命名空间B中的函数的时候，调用方法为 B.functionName();

当在一个命名空间A中调用自己空间中的函数的时候，调用方法为 this.functionName();

#### 单例模式的优劣

优点：

使用单例模式，我们可以实现简单的模块化开发

```js
var obj = {
  getCss:function(){
      //code
  },
  setCss:function(){
      //code
  }
}
```

我们可以把自己写好的工具方法放到一个单独的js文件中，然后直接引入即可。

避免了全局变量名的冲突

需要注意的是，我们在引入各个模块的时候，需要注意引入的顺序，引入顺序是按照各模块之间的相互依赖进行前后排列的；

缺点：

单例模式只是一定程度上避免了变量名的冲突，但并不能彻底解决此问题，而且在不同的对象下，我们可能会有很多功能相同的代码，最终造成大量的冗余代码。
单例模式让每个对象有了自己独立的命名空间，但是并不能批量生产的问题，每一个新的对象都要重新写一份一模一样的代码（如下面代码所示）。

```js
var person = {
  name:'小明',
  age:24,
  showName:function(){
    console.log('我的名字是：'+this.name)
  }
};
var person = {
  name:'小华',
  age:25,
  showName:function(){
    console.log('我的名字是：'+this.name)
  }
};
```




-----------------------------------------------------------------------------------------------------------


### 工厂模式

1、什么是工厂模式？

把实现同一件事情的相同代码放到同一个函数中，想实现这个功能只要执行这个函数即可，这就是工厂模式，也叫做“函数的封装"，这也是”低耦合，高内聚“，从而达到减少页面冗余代码，提高代码重复利用率的作用。

工厂模式其实就是把需要一个个的编写的对象,放在一个函数中统一的进行创建，说白了就是普通函数的封装。

工厂模式总共3步骤：
1）进厂 --- 创建一个空对象
2）加工 --- 加工对象：给对象添加属性和方法；
3）输出 --- 返回对象：return 对象；

```js
function CreatePerson(name,age){
  var obj={};//1.创建一个空对象
  //2.加工对象
  obj.name=name;
  obj.age=age;
  obj.showName=function(){
    console.log('我的名字是：'+this.name)
  };
  return obj;//3.输出对象；
}
var person1 = CreatePerson('小明',23)
var person2 = CreatePerson('小华',23)
person1.showName(); //我的名字是：小明
person2.showName(); //我的名字是：小华
```
 
#### 工厂模式的优缺点

既然叫工厂模式，它就和我们周围的工厂一样，我们只需要把原材料放进去，就能得到我们需要的产品了。

工厂模式也解决了单例模式的批量生产的问题，避免了单例模式中的大量冗余代码，进行系统的封装，提高代码的重复利用率

不过工厂模式跟我们js内置类的调用方法不同




-----------------------------------------------------------------------------------------------------------


### 构造函数模式
可以创建一个自定义的类，并且可以new出实例
构造函数做的就是类和实例打交道。

```js
//构造函数：首字母大写(约定俗成)；
function CreatePerson(name,age){ //创建一个自定义的类
    //构造函数中的this，都是new出来的实例
    //构造函数中存放的都是私有的属性和方法；
    this.name=name;
    this.age=age;
    this.showName=function(){
      console.log('我的名字是：'+this.name)
    }
}
//实例1
var person1 = new CreatePerson('小明',25);
person1.showName(); // 我的名字是：,小明
//实例2
var person2 = new CreatePerson('小华',24);
```
注意，如果不写new，这就是一个普通函数，它返回undefined。但是，如果写了new，它就变成了一个构造函数，它绑定的this指向新创建的对象，并默认返回this，也就是说，不需要在最后写return this;。


new操作符的作用如下：

1.创建一个空对象  
2.由this变量引用该对象  
3.该对象继承该函数的原型  
4.把属性和方法加入到this引用的对象中  
5.新创建的对象由this引用，最后隐式地返回this。  
过程如下：
```js
let obj = {};//创建一个空对象
obj.__proto__ = Person.prototype;//该对象继承该函数的原型
Person.call(obj);//隐式地返回this
```




工厂模式和构造函数模式的区别：

1. 在调用的时候不同：
工厂模式：调用的时候，只是普通函数的调用createPerson();
构造函数模式：new CreatePerson();

2. 在函数体内不同：
工厂模式有三步：1）创建对象 2）加工对象 3）返回对象；
构造函数模式只有1步： 只有加工对象； 因为系统默认会为其创建对象和返回对象；

3. 构造函数默认给我们返回了一个对象，如果我们非要自己手动返回的话：
  (1)手动返回的是字符串类型：对以前实例上的属性和方法没有影响；
  (2)手动返回的是引用数据类型:以前实例身上的属性和方法就被覆盖了；实例无法调用属性和方法；

构造函数的方法都是私有方法，每个实例调用的都是自己私有的方法，同样也会有许多重复的代码。

我们可以使用原型模式来解决每个实例中都有相同方法的函数的问题




-----------------------------------------------------------------------------------------------------------



原型模式
```js
function CreatePerson(name,age){
    this.name=name;
    this.age=age;
}
// 我们把公有的方法放到函数的原型链上
CreatePerson.prototype.showName = function(){
  console.log('我的名字是：'+this.name)
}  
var person1 = new CreatePerson('小明',25) 
var person2 = new CreatePerson('小华',24)
person1.showName() //小明
```

 
 

原型模式的关键：

1）每个函数数据类型（普通函数，类）上，都有一个属性，叫prototype。
2）prototype这个对象上，天生自带一个属性，叫constructor:指向当前这个类；
3）每个对象数据类型（普通对象，prototype，实例）上都有一个属性，叫做__proto__:指向当前实例所属类的原型；
通过例子我们来看这几句话是什么意思

```js
function CreatePerson(name,age){
  this.name=name;
  this.age=age
}
CreatePerson.prototype.showName=function(){
  console.log('我的名字是：'+this.name)
}
var person1 = new CreatePerson('小明',25);
console.dir(person1)
```

 
在chrome浏览器控制台中显示---->

它们中有一条链关联着 person1.__proto__ === CreatePerson.prototype



接着来看

```js
function Foo(){
  this.a=1;
}
Foo.prototype.a=2;
Foo.prototype.b=3;
var f1 = new Foo;  //没有参数的话括号可以省略
console.log(f1.a) //1
console.log(f1.b) // 3
``` 

  
   
  
![图1](/picture/1.png)





接着来说，Foo.prototype是个对象,那么它的__proto__指向哪里呢？

每个对象数据类型（普通对象，prototype，实例）上都有一个属性，叫做__proto__:指向当前实例所属类的原型 

此外，我们应该知道每一个对象都是function Object这个构造函数的实例

```js
var obj = Object();
obj.__proto__==Object.prototype    //true
```



所以我们可以接着还原这个原型图
![图2](/picture/2.png)





还没完,我们发现对象都是函数(构造器)创造出来的，那么函数是谁创造的呢？
在js中,function都是由function Function这个构造器创造的，每一个函数都是Function的实例

```js
function fun(){};
fun.__proto__==Function.prototype    //true
```

现在基本上我们就能得出了完整的原型图了
![图2](/picture/3.png)



 
补充：

多说一点,判断数据类型的方法时，我们知道有个instanceof的方法
比如

A instanceof B
instanceof判断的规则就是:

沿着A的__proto__这条线查找的同时沿着B的prototype这条线来找，如果两条线能找到同一个引用(对象)，那么就返回true。如果找到终点还未重合，则返回false。

再来看我们之前的那个例子
```js
function Foo(){
    this.a=1;
}
Foo.prototype.a=2;
Foo.prototype.b=3;
var f1 = new Foo;  //没有参数的话括号可以省略
console.log(f1.a) //1
console.log(f1.b) //3
``` 
当我们查找f1.a时，因为f1中有这个属性,所以我们得出 f1.a=1;
当我们查找f1.b时，f1中没有这个属性,我们便顺着f1.__proto__这条链去它的构造器的prototype上找,所以我们得出了 f1.b = 3;
当我们查找一个对象的属性时,先在这个对象的私有空间内查找，如果没找到,就顺着对象的__proto__这条链去它的构造器的ptototype上查找,如果还没找到,接着沿__proto__向上查找,直到找到Object.prototype还没有的话,这个值就为undefined,这就是所谓的原型链


## 总结
面向对象开发对我们平时开发有什么好处？

随着代码库的增长，你需要改变你组织思路的方法，并开始用面向对象的方式思考。与一系列相互纠缠需要通过一系列的参数传递才能运行的函数相比，对象的范围很容易控制和追踪。


基于函数编程的问题在于，一旦有一个函数出现问题，就会导致整个链上的流程失败。而对于对象而言，一个方法出问题不会（也不应该）影响到其他类。


补充：  
JS CLASS类