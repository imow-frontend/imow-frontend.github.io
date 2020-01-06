---
title: Vue之mixin
date: 2020-01-03 09:36:11
author: 胡豪
categories: 
  - vue
tags:
  - vue 
---

## 常见场景
  何时需要使用到mixin呢?

  通常遇到有两个非常相似的组件，他们的基本功能是一样的，但他们之间又存在着足够的差异性，此时的你就像是来到了一个分岔路口：我是把它拆分成两个不同的组件呢？还是使用一个组件，然后通过props传值？对于实现功能来说，这两者都可以做到，但是不够完美。拆成两个组件的写法，遇到功能变动就要在两个文件中去修改，无疑是增加了更新的风险，违反DRY原则。而一个组件，太多的props传值会使得简单的逻辑变得复杂，后期维护时使用组件必须理解一大段上下文，降低了工作效率;这时候使用Mixin就显得很合适；

  Vue 中的Mixin对编写函数式风格的代码很有用，因为函数式编程就是通过减少移动的部分让代码更好理解（引自 Michael Feathers ）。Mixin允许你封装一块在应用的其他组件中都可以使用的函数。如果使用姿势得当，他们不会改变函数作用域外部的任何东西，因此哪怕执行多次，只要是同样的输入你总是能得到一样的值，真的很强大！

  要注意的是 组件 /= mixin，新手可能会混为一谈
  单纯组件引用：
  父组件 + 子组件 >>> 父组件 + 子组件（父子组件相对而言是独立的）

  mixins：
  父组件 + 原本子组件的逻辑提取出来（mixin.js） >>> new父组件（mixin中的方法和属性也就并入到该组件中，可以直接使用。）钩子函数会两个都被调用，mixin中的钩子首先执行。


## 简单用法

定义一个 js 文件(mixin.js)  
我们可以在这里提取逻辑并创建可以被重用的项：
```
export default {
 data() {
  return {
   name: '我是mixin'
  }
 },
 created() {
  console.log(this.name);
 },
 mounted() {},   
 methods: {} 
}
```

一般为了清晰，我会在components文件夹内新建一个mixins文件夹，在这里面放mixin的js
![图片1](http://tvax1.sinaimg.cn/large/b535b7d3ly1gamjtob0oxj21370n5jtz.jpg)

在组件中使用
```
import mixin from '@/components/mixins/mixin'; // 引入mixin文件
export default {
 mixins: [mixin]
}
```

## 选项合并
当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

一、数据冲突
数据对象在内部会进行递归合并，并在发生冲突时以组件数据优先。
```
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc'
    }
  }
}

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def'
    }
  },
  created: function () {
    console.log(this.$data)
    // => { message: "goodbye", foo: "abc", bar: "def" }
  }
})
```
组件中的值会覆盖混入的数据值


二、同名钩子函数将合并为一个数组，因此都将被调用。另外，混入对象的钩子将在组件自身钩子之前调用。
```
var mixin = {
  created: function () {
    console.log('混入对象的钩子被调用')
  }
}

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('组件钩子被调用')
  }
})

// => "混入对象的钩子被调用"
// => "组件钩子被调用"
```

值为对象的选项，例如 methods、components 和 directives，将被合并为同一个对象。两个对象键名冲突时，取组件对象的键值对。
```
var mixin = {
  methods: {
    foo: function () {
      console.log('foo')
    },
    conflicting: function () {
      console.log('from mixin')
    }
  }
}

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar')
    },
    conflicting: function () {
      console.log('from self')
    }
  }
})

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "from self"
```
通俗地讲，混入先运行，组件权重大，冲突看组件

## 总结
混入提供给我们了一种实现方式，用组件还是混入，开头也已经提过，项目中还需具体问题具体分析，不可盲目