---
title: slot
date: 2020-04-09 15:55:23
categories:
tags: vue
author:
---

## 什么是 slot

插槽也就是 slot， 是组件的一块 HTML 模板，用于决定将所携带的内容，插入到指定的某个位置，从而使模块分块，具有模块化的特质和更大的重要性，这块模板显示不显示、以及怎样显示是有父组件来决定的，但是插槽显示的位置却由子组件自身决定，slot 写在组件 template 的什么位置，父组件传过来的模板将来就显示在什么位置。

## slot 的分类

### 默认插槽

```javascript
// 父组件
<template>
  <div>
    我是父组件
    <slotOne1>
      <p style="color:red">我是父组件插槽内容</p>
    </slotOne1>
  </div>
</template>
```

```javascript
// 子组件
<template>
  <div class="slotOne1">
    <div>我是slotOne1组件</div>
    <slot></slot>
  </div>
</template>
```

在子组件中写入 slot，slot 所在的位置就是父组件要显示的内容
![默认插槽](https://img.jbzj.com/file_images/article/201811/2018111216205729.png)

### 具名插槽

将放在子组件里的不同 html 标签放在不同的位置
子组件在对应分发的位置的 slot 标签里，添加`<slot name='name名' >内容</slot>`属性，
父组件在要显示的标签里添加 `slot='name名'`属性，比如要在 p 标签中显示子组件对应的 dom，`<p slot='name名'></p>`
然后就会将对应的标签放在对应的位置了。该 slot 标签没有内容，会显示子组件中该 slot 标签内的内容。

```javascript
//父组件
<template>
  <div>
    <child>
      <div slot="title">vue.js</div>
      <div slot="center">
        是一套用于构建用户界面的渐进式JavaScript框架。
        与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。 Vue
        的核心库只关注视图层，方便与第三方库或既有项目整合。
      </div>
    </child>
  </div>
</template>
```

```javascript
//子组件
<template>
  <div class="box">
    <div class="title">
      <slot name="title">这是拥有命名的slot的默认title</slot>
    </div>
    <div class="centen">
      <slot name="center">这是拥有命名的slot的默认内容</slot>
    </div>
    <div class="footer">
      <slot name="footer">父组件中没footer的时候会显示</slot>
    </div>
  </div>
</template>
```

![具名插槽](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/%E6%B5%85%E8%B0%88v-slot/%E5%85%B7%E5%90%8D%E6%8F%92%E6%A7%BD.jpg)

### 作用域插槽

子组件编写：有时让父组件能访问到子组件中的数据是很有用的，我们可以将绑定在 `<slot>`元素上的特性称为插槽 Prop ，当然也可以传递一些 Function。

```javascript
<template>
  <div class="box">
    <slot name="name" :data="data" ></slot>

  </div>
</template>

<script>
export default {
  data() {
    return {
      data: ["zhangsan", "lisi", "wanwu", "zhaoliu", "tianqi", "xiaoba"]
    };
  }
};
</script>
```

父组件编写：被绑定的属性的集合对象，在父元素中会被 v-slot:xxx=”slotProps” 或者 #xxx=”slotProps” 接收，xxx 代表具名插槽的 name ，slotProps 为子组件传递的数据对象，可以重命名。(#是 v-slot 缩写)

```javascript
<template>
  <div>
    <staff>
       <template v-slot:name="slotProps">
        {{slotProps.data}}
      </template>
    </staff>
  </div>
</template>

<script>
import staff from './Staff'
export default {
  components:{staff},
  data () {
    return {}
  }
}
</script>
```

![作用域插槽](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/%E6%B5%85%E8%B0%88v-slot/%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%8F%92%E6%A7%BD.jpg)
