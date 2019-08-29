---
layout: post
title: javascript中的Map
date: 2019-08-26 10:53:41
categories: 
- JavaScript 
tags:
- js 
---


# 探究javascript Map
什么是Map，此处说的Map，非js中的map( )方法,下文会详细解释。  
Map 对象保存键值对。任何值(对象或者原始值) 都可以作为一个键或一个值。  
ECMAScript6原生实现了Map类，即我们所说的字典，字典和集合很像，不过集合是以值值得形式存储元素，字典则是以键值的形式存储元素。字典也叫映射。
## 语法

```js
new Map([iterable])
```
iterable (可迭代传参)
Iterable 可以是一个数组或者其他 iterable 对象，其元素为键值对(两个元素的数组，例如: [[ 1, 'one' ],[ 2, 'two' ]])。 每个键值对都会添加到新的 Map。null 会被当做 undefined。

## 描述

一个Map对象在迭代时会根据对象中元素的插入顺序来进行 — 一个  for...of 循环在每次迭代后会返回一个形式为[key，value]的数组。

键的比较是基于 "SameValueZero" 算法：NaN 是与 NaN 相等的（虽然 NaN !== NaN），剩下所有其它的值是根据 === 运算符的结果判断是否相等。 

Objects 和 Maps 类似的是，它们都允许你按键存取一个值、删除键、检测一个键是否绑定了值。因此（并且也没有其他内建的替代方式了）过去我们一直都把对象当成 Maps 使用。不过 Maps 和 Objects 有一些重要的区别，在下列情况里使用 Map 会是更好的选择：

* 一个Object的键只能是字符串或者 Symbols，但一个 Map 的键可以是任意值，包括函数、对象、基本类型。
* Map 中的键值是有序的，而添加到对象中的键则不是。因此，当对它进行遍历时，Map 对象是按插入的顺序返回键值。
* 你可以通过 size 属性直接获取一个 Map 的键值对个数，而 Object 的键值对个数只能手动计算。
* Map 可直接进行迭代，而 Object 的迭代需要先获取它的键数组，然后再进行迭代。
* Object 都有自己的原型，原型链上的键名有可能和你自己在对象上的设置的键名产生冲突。虽然 ES5 开始可以用 map = Object.create(null) 来创建一个没有原型的对象，但是这种用法不太常见。
* Map 在涉及频繁增删键值对的场景下会有些性能优势。

最大的差别其实就是：object的键的类型是 字符串；map的键的类型是 可以是任意类型;  
object获取键值使用Object.keys（返回数组）；Map获取键值使用 map变量.keys() (返回迭代器)。


## 属性

* Map.length 

* get Map[@@species]

* Map.prototype

## Map 实例

### *属性
Map.prototype.constructor  
返回一个函数，它创建了实例的原型。默认是Map函数。

Map.prototype.size  
返回Map对象的键/值对的数量。

### *方法

Map.prototype.clear()
移除Map对象的所有键/值对 。

Map.prototype.delete(key)
如果 Map 对象中存在该元素，则移除它并返回 true；否则如果该元素不存在则返回 false

Map.prototype.entries()
返回一个新的 Iterator 对象，它按插入顺序包含了Map对象中每个元素的 [key, value] 数组。

Map.prototype.forEach(callbackFn[, thisArg])
按插入顺序，为 Map对象里的每一键值对调用一次callbackFn函数。如果为forEach提供了thisArg，它将在每次回调中作为this值。

Map.prototype.get(key)
返回键对应的值，如果不存在，则返回undefined。

Map.prototype.has(key)
返回一个布尔值，表示Map实例是否包含键对应的值。

Map.prototype.keys()
返回一个新的 Iterator对象， 它按插入顺序包含了Map对象中每个元素的键 。

Map.prototype.set(key, value)
设置Map对象中键的值。返回该Map对象。

Map.prototype.values()
返回一个新的Iterator对象，它按插入顺序包含了Map对象中每个元素的值 。

Map.prototype[@@iterator]()
返回一个新的Iterator对象，它按插入顺序包含了Map对象中每个元素的 [key, value] 数组。

## 实例

### 使用 Map 对象
```js
var myMap = new Map();
 
var keyObj = {},
    keyFunc = function () {},
    keyString = "a string";
 
// 添加键
myMap.set(keyString, "和键'a string'关联的值");
myMap.set(keyObj, "和键keyObj关联的值");
myMap.set(keyFunc, "和键keyFunc关联的值");
 
myMap.size; // 3
 
// 读取值
myMap.get(keyString);    // "和键'a string'关联的值"
myMap.get(keyObj);       // "和键keyObj关联的值"
myMap.get(keyFunc);      // "和键keyFunc关联的值"
 
myMap.get("a string");   // "和键'a string'关联的值"
                         // 因为keyString === 'a string'
myMap.get({});           // undefined, 因为keyObj !== {}
myMap.get(function() {}) // undefined, 因为keyFunc !== function () {}
```
### 使用 for..of 方法迭代 Map
```js
var myMap = new Map();
myMap.set(0, "zero");
myMap.set(1, "one");
for (var [key, value] of myMap) {
  console.log(key + " = " + value);
}
// 将会显示两个log。一个是"0 = zero"另一个是"1 = one"

for (var key of myMap.keys()) {
  console.log(key);
}
// 将会显示两个log。 一个是 "0" 另一个是 "1"

for (var value of myMap.values()) {
  console.log(value);
}
// 将会显示两个log。 一个是 "zero" 另一个是 "one"

for (var [key, value] of myMap.entries()) {
  console.log(key + " = " + value);
}
// 将会显示两个log。 一个是 "0 = zero" 另一个是 "1 = one"
```
### 使用 forEach() 方法迭代 Map
```js
myMap.forEach(function(value, key) {
  console.log(key + " = " + value);
})
// 将会显示两个logs。 一个是 "0 = zero" 另一个是 "1 = one"
```
### Map 与数组的关系
```js
var kvArray = [["key1", "value1"], ["key2", "value2"]];

// 使用常规的Map构造函数可以将一个二维键值对数组转换成一个Map对象
var myMap = new Map(kvArray);

myMap.get("key1"); // 返回值为 "value1"

// 使用Array.from函数可以将一个Map对象转换成一个二维键值对数组
console.log(Array.from(myMap)); // 输出和kvArray相同的数组

// 或者在键或者值的迭代器上使用Array.from，进而得到只含有键或者值的数组
console.log(Array.from(myMap.keys())); // 输出 ["key1", "key2"]
```
### 复制或合并 Maps
```js
var original = new Map([
  [1, 'one']
]);

var clone = new Map(original);

console.log(clone.get(1)); // one
console.log(original === clone); // false. Useful for shallow comparison
```
请记住，数据本身未被克隆。

Map对象间可以进行合并，但是会保持键的唯一性。
```js
var first = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

var second = new Map([
  [1, 'uno'],
  [2, 'dos']
]);

// 合并两个Map对象时，如果有重复的键值，则后面的会覆盖前面的。
// 展开运算符本质上是将Map对象转换成数组。
var merged = new Map([...first, ...second]);

console.log(merged.get(1)); // uno
console.log(merged.get(2)); // dos
console.log(merged.get(3)); // three
```
Map对象也能与数组合并：
```js
var first = new Map([
  [1, 'one'],
  [2, 'two'],
  [3, 'three'],
]);

var second = new Map([
  [1, 'uno'],
  [2, 'dos']
]);

// Map对象同数组进行合并时，如果有重复的键值，则后面的会覆盖前面的。
var merged = new Map([...first, ...second, [1, 'eins']]);

console.log(merged.get(1)); // eins
console.log(merged.get(2)); // dos
console.log(merged.get(3)); // three
```


# 以上讲的是Map，那不得不说map( )是什么

ECMAScript5中我们有一个数组方法 map( ),区别于ECMAScript6 中 Map类  
map() 方法创建一个新数组，其结果是该数组中的每个元素都调用一个提供的函数后返回的结果。

## 语法
``` js
var new_array = array.map(function callback(currentValue,index,arr), thisValue)
```
## 参数
callback 生成新数组元素的函数，使用三个参数：  
* currentValue callback 数组中正在处理的当前元素。  
* index可选 callback 数组中正在处理的当前元素的索引。  
* array可选 callback  map 方法被调用的数组。

thisArg可选 执行 callback 函数时使用的this 值。

## 示例
```js
var array1 = [1,3,6];
const map1 = array1.map(x => x *2);
console.log(map1);   
// > Array [2, 6, 12]
```

```js
var array1 = [1, 3,6];

const map1 = array1.map(x => {
    if (x == 3) {
      return x * 2;
    }
});
 
console.log(map1);

// > Array [undefined, 6, undefined]
```
出现undefined ：只是增加了一个条件，即x的值为3时才乘以2，之所以会出现undefined，是因为map()方法创建了一个新数组，但新数组并不是在遍历完array1后才被赋值的，而是每遍历一次就得到一个值。所以，下面这样修改后就正确了
```js
var array1 = [1, 3,6];

const map1 = array1.map(x => {
    if (x == 3) {
      return x * 2;
    }
    return x
});
 
console.log(map1);

// > Array [2, 6, 12]
```
可以使用 map 重新格式化数组中的对象
```js
var oldArray = [{key: 1, value: 10}, 
                {key: 2, value: 20}, 
                {key: 3, value: 30}];

var nowArr = oldArray.map(function(obj) { 
   var rObj = {};
   rObj[obj.key] = obj.value;
   return rObj;
});

// nowArr 数组为： [{1: 10}, {2: 20}, {3: 30}], 

// oldArray 数组未被修改: 
// [{key: 1, value: 10}, 
//  {key: 2, value: 20}, 
//  {key: 3, value: 30}]
```

# 用ES5模拟实现ES6中的Map类

与Set类一样，我们用Object的实例而不是数组存储元素,我们实现以下方法：
1.set(key,value):向字典中添加新元素。

2.remove(key):使用键名从字典中移除相应的元素。

3.has(key):如果某个键值存在于字典中，返回true，否则返回false。

4.get(key):通过键名找到指定的值并返回。

5.clear():清空字典。

6.size():返回字典中元素个数。

7.values():将字典所有值以数组形式返回。

8.getItems():返回items变量，代表字典本身。


```js
function Map() {
    var items = {};
    this.has = function(key){    //has方法判断某个键值是否在字典中
        return key in items;
    },
    this.set = function(key,value){    //set方法为字典添加新元素或更新已有元素
        items[key] = value;
    },
    this.remove = function(key){
        if (this.has(key)) {
            delete items[key];
            return true;
        }
        return false;
    },
    this.get = function(key){
        return this.has(key)?items[key]:undefined;
    },
    this.values = function(){
        var values = [];
         //for...in会遍历出对象原型上的属性，所以要this.hasOwnProperty()方法选出对象自身的属性。
        for(var k in items){   
            if (this.hasOwnProperty(k)) {
                values.push(items[k]);
            }
        }
        return values;
    },
    this.clear = function(){
        items = {};
    },
    this.size = function(){
        return Object.Keys(items).length;
    },
    this.getItems = function(){
        return items;
    }
}
```