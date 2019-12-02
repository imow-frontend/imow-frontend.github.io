---
title: 浅谈v-slot
date: 2019-11-14 13:27:30
author: 刘涛
categories: 
  - vue
tags: 
  - vue
---

## 前言
Vue 代码中的 slot 是什么，简单来说就是插槽。`<slot>`元素作为组件模板之中的内容分发插槽，传入内容后 `<slot>` 元素自身将被替换。

看了上面这句官方解释，可能一样不知道 slot 指的是什么，那么就来看看在 Vue 中，什么时候你需要用到 slot 。

举例：一个组件的展示层你需要做到大体结构固定，但其内的部分结构可变，样式表现不固定。例如 Button 中是否显示 icon ，或者 Modal 框的中间内容展示区域的变化等，要通过子组件自己实现是不可能的。组件并不直接支持 HTML DOM 结构的传递，此时就可以通过使用 slot 作为 HTML 结构的传递入口来解决问题。


## 什么是插槽
* 插槽（Slot）是Vue提出来的一个概念，正如名字一样，插槽用于决定将所携带的内容，插入到指定的某个位置，从而使模板分块，具有模块化的特质和更大的重用性。
* 插槽显不显示、怎样显示是由父组件来控制的，而插槽在哪里显示就由子组件来进行控制

## 插槽的作用
让用户可以拓展组件，去更好地复用组件和对其做定制化处理。
举一些例子，比如布局组件、表格列、下拉选项

## 怎么使用插槽

### 默认插槽
**子组件编写**：在组件中添加 `<slot>` 元素，来确定渲染的位置。
```html
// 子组件
  <template>
    <div class="content">
      <!-- 默认插槽 -->
      <header class="text">
        <!-- slot 的后备内容：为一个插槽设置具体后备（默认）内容是很有用的，
        当父组件不添加任何插槽内容时，默认渲染该后备内容的值。 -->
        <slot>默认值</slot>
      </header>
    </div>
  </template>
```

**父组件编写**：任何没有被包裹在带有 v-slot 的 `<template> `中的内容都会被视为默认插槽的内容。当子组件只有默认插槽时， `<v-slot>` 标签可以直接用在组件上，也就是独占默认插槽的写法
``` html
// 父组件
<template>
  <div class="container">
    <!-- 默认插槽-->
    <child>
      任意内容
      <template>内容</template>
      中间内容 
      <!-- <template v-slot:default>但如果你定义了 default 之后，其他内容就不会出现了，
      原理同上，不能重复定义</template> -->
    </child>
    
    <!-- 独占默认插槽的缩写 -->
    <child v-slot="slotProps">
      当只有默认插槽时，此为独占默认插槽的缩写<br>
      如果组件中有其他具名插槽，这么写会报错<br>
      slotProps 取的是，子组件标签 slot 上属性数据的集合
    </child>
  </div>
</template>
```

**渲染结果**：
![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/%E6%B5%85%E8%B0%88v-slot/%E9%BB%98%E8%AE%A4%E6%8F%92%E6%A7%BD.png)

### 具名插槽
**子组件编写**：当需要使用多个插槽时，为 `<slot>` 元素添加 name 属性，来区分不同的插槽，当不填写 name 时，默认为 default 默认插槽。
``` html
 // 子组件
  <template>
    <div class="content">
      <!-- 具名插槽 -->
      <main class="text">
        <slot name="main"></slot>
      </main>
      <footer class="text">
        <slot name="footer"></slot>
      </footer>
    </div>
  </template>
```

**父组件编写**：`<template>` 标签中添加 v-slot:xxx 或者 #xxx 属性的内容， # 代表插槽的缩写。

``` html
// 父组件
<template>
  <div class="container">
    <!-- 具名插槽使用 -->
    <child>
      <template v-slot:main>
        <a href="https://www.zcygov.cn" target="_blank">导航</a>
      </template>
      <template #footer>页脚（具名插槽的缩写#）</template>
      <template #footer>
        <!--  v-slot 重复定义同样的 name 后只会加载最后一个定义的插槽内容 -->
        v-slot只会添加在一个 template 上面
      </template>
    </child>
  </div>
</template>
```

**渲染结果**：
![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/%E6%B5%85%E8%B0%88v-slot/%E5%85%B7%E5%90%8D%E6%8F%92%E6%A7%BD.png)


### 作用域插槽
**子组件编写**：有时让父组件能访问到子组件中的数据是很有用的，我们可以将绑定在 `<slot>`元素上的特性称为**插槽 Prop** ，当然也可以传递一些 Function。
```html
 // 子组件
  <template>
    <div class="content">
      <!-- 作用域插槽 -->
      <footer class="text">
        <slot name="footer" :user="user" :testClick="testClick">
          {{user.name}}
        </slot>
      </footer>
    </div>
  </template>
  
  <script>
  export default {
    name: 'child',
    data () {
      return {
        user: {
          title: '测试title',
          name: '测试name'
        }
      };
    },
    methods:{
      testClick(){
      	// 子组件通用方法，可传递给父组件复用
        alert('123');
      }
    }
  };
  </script>
  ```

  **父组件编写**：被绑定的属性的集合对象，在父元素中会被 v-slot:xxx="slotProps" 或者 #xxx="slotProps" 接收，xxx 代表具名插槽的 name ，slotProps 为子组件传递的数据对象，可以重命名。
```html
// 父组件
<template>
  <div class="container">
    <!-- 作用域插槽，以及解构插槽 Prop 的写法 -->
    <child>
      <template #footer="slotProps">
        {{slotProps.user.title}}
        <button @click="slotProps.testClick">点我</button>
      </template>
      <template #footer="{user,testClick}">
        {{user.title}}<br>
        此为解构插槽prop<br>
        <!-- 子组件中的通用方法，可传递给父组件复用 -->
        <button @click="testClick">点我</button>
      </template>
    </child>
  </div>
</template>

<script>
import Child from './component/child.vue';
export default {
  name: 'demo',
  components: {
    Child
  },
};
</script>
```

**渲染结果**：
![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/%E6%B5%85%E8%B0%88v-slot/%E4%BD%9C%E7%94%A8%E5%9F%9F%E6%8F%92%E6%A7%BD.png)

### 其它拓展

* 解构插槽 prop 可以重命名，例如：`v-slot="{ user: person }"` 将 user 对象重命名为 person 使用。

* 解构插槽 prop 可以赋值默认值，例如：`v-slot="{ user = { name: 'Guest' } }"` 给属性添加自定义后备内容。

* 动态插槽命名，例如：`v-slot:[dynamicSlotName]` ，支持命名变量定义。

### 注意事项
* `v-slot` 只能用在 `template` 上面，或者是独占默认插槽的写法。

* 父组件引用时 ，重复定义了 `v-slot` 的 `name` 后只会加载最后一个定义的插槽内容。

* 当子组件只有默认插槽时，才可以使用独占默认插槽的缩写语法，只要出现多个插槽，必须使用完整的基于 `template` 的语法。


## slot 以及 slot-scope 的用法

### 子组件编写
```html
  // 子组件
  <template>
    <div class="content">
      <!-- 默认插槽 -->
      <header class="text">
        <slot>默认值</slot>
      </header>
      
      <!-- 具名插槽 -->
      <main class="text">
        <slot name="main"></slot>
      </main>
      
      <!-- 作用域插槽 -->
      <footer class="text">
        <slot name="footer" :user="user" :testClick="testClick">
          {{user.name}}
        </slot>
      </footer>
    </div>
  </template>
  
  <script>
  export default {
    name: 'child',
    data () {
      return {
        user: {
          title: '测试title',
          name: '测试name'
        }
      };
    }
  };
  </script>
```

### 父组件编写
* 具名插槽：直接使用 `slot` 属性，传值为子组件插槽的 name 属性。
* 作用域插槽：通过 `slot-scope` 属性来接受子组件传入的属性集合，其他用法一致。

``` html
// 父组件
 <template>
   <div class="container">
     <child>
       <!-- 默认插槽 -->
       <div>默认插槽</div> 
      
       <!-- 具名插槽 -->
       <div slot="main">具名插槽</div>
       <div slot="main">具名插槽2</div>
      
       <!-- 作用域插槽 -->
       <div slot="footer" slot-scope="slotProps">
         {{slotProps.user.title}}
       </div>
     </child>
   </div>
 </template>

 <script>
 import Child from './component/child.vue';
 export default {
   name: 'demo',
   components: {
     Child
   },
 };
 </script>
```

**渲染结果**：
![](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/%E6%B5%85%E8%B0%88v-slot/slot-scope%20%E7%9A%84%E7%94%A8%E6%B3%95.png)

### 注意事项
* 不同于 `v-slot` 的是，slot 中同名可以重复定义多次。
* `slot` 可以直接定义在子组件上。
* v3.0 版本后不可使用 `slot` ，建议直接使用 `v-slot` 。


## 总结
* 插槽的 `<slot>` 的可复用特性，可以用来写一些组件结构固定，内容可替换的组件，例如表格，列表，按钮，弹窗等内容。
* 插槽可以传递属性值或者 function 的特性，可以在子组件中写一些通用的函数，例如通用的报错提示等，传递给父组件复用。






















