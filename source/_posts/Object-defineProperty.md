---
title: vue双向数据绑定原理
date: 2019-10-30 13:57:52
tags:
---

## 首先我们要了解Object.defineProperty()


一、Object.defineProperty()的作用就是直接在一个对象上定义一个新属性，或者修改一个已经存在的属性

1.1 属性描述符
通过Object.defineProperty()为对象定义属性，有两种形式，且不能混合使用，分别为数据描述符，存取描述符，下面分别描述下两者的区别：

数据描述符 --特有的两个属性（value,writable）

```js
let Person = {}
Object.defineProperty(Person, 'name', {
   value: 'jack',
   writable: true // 是否可以改变
})
```

属性名 | 默认值 
-|-
value | undefined 
get | undefined 
set | undefined 
writable | false 
enumerable | false 
configurable | false


二、什么是 getter 和 setter  
getter 是一种获得属性值的方法，setter 是一种设置属性值的方法
getter 负责查询值，它不带任何参数，setter 则负责设置键值，值是以参数的形式传递，在他的函数体中，一切的 return 都是无效的
get/set 访问器不是对象的属性，而是属性的特性，特性只有内部才用，因此在 javaScript 中不能直接访问他们，为了表示特性是内部值用两队中括号括起来表示如[[Value]]
对象的属性又可分为对象属性和访问器属性

## set/get

```js
var person = {
  _name: "",
  get name() {
    return this._name;
  },
  set name(n) {
    this._name = n;
  }
};

// 测试
person.name; // 输出 --> ''
person.name = "xiaoming";
person.name; // 输出 --> xiaoming
```

## 存取描述符 --是由一对 getter、setter 函数功能来描述的属性
get：一个给属性提供getter的方法，如果没有getter则为undefined。该方法返回值被用作属性值。默认为undefined。
set：一个给属性提供setter的方法，如果没有setter则为undefined。该方法将接受唯一参数，并将该参数的新值分配给该属性。默认值为undefined。 
```
var person = function() {
    var _name = ' ';
    var obj = {};
    Object.defineProperty(obj, 'name', {
        configurable: true,
        enumerable: true,
        get: function() {
            return _name;
        },
        set: function(n) {
            _name = n;
        }
    })
    return obj;
}();
person.name = "xiaoming";
person.name // 输出 --> xiaoming
```

## vue 的双项数据绑定原理

vue.js 则是采用数据劫持结合发布者-订阅者模式的方式，通过 Object.defineProperty()来劫持各个属性的 setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。我们先来看 Object.defineProperty()这个方法：

```js
var obj = {};
Object.defineProperty(obj, "name", {
  get: function() {
    console.log("我被获取了");
    return val;
  },
  set: function(newVal) {
    console.log("我被设置了");
  }
});
obj.name = "fei"; //在给obj设置name属性的时候，触发了set这个方法
var val = obj.name; //在得到obj的name属性，会触发get方法
```

vue 是通过数据劫持的方式来做数据绑定的，其中最核心的方法便是通过 Object.defineProperty()来实现对属性的劫持，那么在设置或者获取的时候我们就可以在 get 或者 set 方法里假如其他的触发函数，达到监听数据变动的目的，无疑这个方法是本文中最重要、最基础的内容之一。

## 实现最简单的双向绑定

通过 Object.defineProperty()可以实现数据劫持，是的属性在赋值的时候触发 set 方法

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
</head>
<body>
    <div id="demo"></div>
    <input type="text" id="inp">
    <script>
        var obj  = {};
        var demo = document.querySelector('#demo')
        var inp = document.querySelector('#inp')
        Object.defineProperty(obj, 'name', {
            get: function() {
                return val;
            },
            set: function (newVal) {//当该属性被赋值的时候触发
                inp.value = newVal;
                demo.innerHTML = newVal;
            }
        })
        inp.addEventListener('input', function(e) {
            // 给obj的name属性赋值，进而触发该属性的set方法
            obj.name = e.target.value;
        });
        obj.name = 'changme';//在给obj设置name属性的时候，触发了set这个方法
    </script>
</body>
</html>
```

## vue 代码实现

observer 实现，主要是给每个 vue 的属性用 Object.defineProperty()，代码如下

```js
function defineReactive(obj, key, val) {
  var dep = new Dep();
  Object.defineProperty(obj, key, {
    get: function() {
      //添加订阅者watcher到主题对象Dep
      if (Dep.target) {
        // JS的浏览器单线程特性，保证这个全局变量在同一时间内，只会有同一个监听器使用
        dep.addSub(Dep.target);
      }
      return val;
    },
    set: function(newVal) {
      if (newVal === val) return;
      val = newVal;
      console.log(val);
      // 作为发布者发出通知
      dep.notify(); //通知后dep会循环调用各自的update方法更新视图
    }
  });
}
function observe(obj, vm) {
  Object.keys(obj).forEach(function(key) {
    defineReactive(vm, key, obj[key]);
  });
}
```
未完，待续、、、