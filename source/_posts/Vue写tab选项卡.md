---
title: Vue写tab选项卡
date: 2019-12-17 09:22:18
author: 胡豪
categories: 
  - vue
tags:
  - vue 
---

## Vue写tab选项卡
### 固定选项的写法
遇到UI上只有2个或者3个固定选项的写法
```js
//2个的情况只要用一个开关字段来控制显示隐藏即可
//3个及以上的写法，可以价格tabId来控制
<div id="app">
  <!-- tab 选项 -->
  <div class="tab">
    <div class="tag" :class="{ active: tabId==0 }" @click="tabId=0">
      已出账单
    </div>
    <div class="tag" :class="{ active: tabId==1 }" @click="tabId=1">
      未出账单
    </div>
    <div class="tag" :class="{ active: tabId==2 }" @click="tabId=2">
      定金
    </div>  
  </div> 
  <!-- box 内容 -->
  <div class="box" >
      <!-- 制作选项卡内容 === 是判断 -->
      <div v-show="tabId===0">
        内容块
      </div>
      <div v-show="tabId===1">
        内容块
      </div>
      <div v-show="tabId===2">
        内容块
      </div>
  </div>
</div>

data(){
  return { 
    //表格切换 
    toggleTable:true,   //2个tab切换使用
    tabId:0,            //3个及以上tab切换使用
  }
},

```

### 非固定选项的写法
常常我们也会遇到tab选项没有具体固定有几个项，需要我们根据接口数据生成对应的tab项
```js
<div id="app">
  <ul>        
    <li @click="toggle(index ,tab.view)" v-for="(tab,index) in tabs" :class="{active:active===index}">
      {{tab.type}}       
    </li>    
  </ul>   
  <component :is="currentView"></component>
</div>

<script>
Vue.component('child1', { 
  template: "<p>this is child1</p>"
})
Vue.component('child2', { 
  template: "<p>this is child2</p>"
})
new Vue({ 
  el: "#app", 
  data: {   
      active: 0, 
      currentView: 'child1',   
      tabs: [   
          {       
              type: 'tab1',   
              view: 'child1'  
          },     
          {       
              type: 'tab2',    
              view: 'child2'    
          }  
      ]  
  }, 
  methods: {  
      toggle(i, v){    
          this.active = i   
          this.currentView = v  
      } 
  }
})
</script>
```