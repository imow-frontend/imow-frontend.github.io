---
title: for循环创建多个elementUI表单遇到的问题
date: 2019-12-11 15:02:30
author: 胡豪
categories: elementUI
tags: elementUI
---

## 前言

目前有一个需求就是：点击添加按钮创建一个表单，可以保存删除修改等一系列操作。
因为是表单所以涉及到需要表单验证。差了很多资料发现都是点击之后添加一条可输入的input框之类，并没整体循环一整个表单的。

![QQ浏览器截图20191210161509](http://tvax2.sinaimg.cn/large/b535b7d3ly1g9sx2vtpvsj20py0f7dgp.jpg)

上图是一个商品列表，商品列表的每一项的授权按钮，都会弹出一个授权的表单，遇到的问题是，elementUI的表单放在for循环里渲染出来,那么常规的提交表单和清空表单会报错，显示`this.$refs[formName].validate()方法不存在`，组件引入都没有问题;但表单放在for循环外面就没有问题，经过试验发现是form表单放在for循环里出现的问题;

改动如下,就不会出现问题：
```js
<template>
  <div v-for="(item,index) in ProductList" :key="index" class="customCenList">
    <div class="list">
      <img src="~assets/images/rankCar.png">
      <span style="width:200px;">{{ item.name }}</span>
      <span><nuxt-link to="/">
        <span class="downFont">下载样品</span>
      </nuxt-link></span>
      <span style="width: 100px;">{{ item.number }}台</span>
      <span style="width: 140px;">￥{{ item.customizedDeposit }}</span>
      <span style="width: 140px;">￥{{ item.stockDeposit }}</span>
      <div v-if="item.isHead" class="listButton">
        <span @click="showLicense1(item.id)">查看授权客户</span><br>
        <span @click="showLicense2(item.id)">授权</span>
      </div>
    </div>

    <!-- 授权 --> 
    <div v-show="item.id==currentId&&showItem1" class="license1">
      <div class="boxName">
        <span>授权</span><span @click="retract1">收起</span>
      </div> 
      <ElForm ref="myform" :model="myform" :rules="rules" :inline="true">
        <FormItem prop="price" label="购买价格:" style="margin-right:30px;">
          <ElInput v-model="myform.price" size="small" style="width:100px" oninput="value=value.replace(/[^\d.]/g,'')" /> 
        </FormItem>
        <FormItem prop="phone" label="手机号:" style="margin-right:30px;">
          <ElInput v-model="myform.phone" size="small" style="width:200px" />
        </FormItem>
        <FormItem prop="company" label="公司名称:">
          <ElInput v-model="myform.company" size="small" style="width:250px" />
        </FormItem>  
        <div class="bottom">
          <span>注：授权客户直接向中力购买产品，客户手机号应为注册账户使用手机号</span>
          <div class="bottomBtns clear">
            <div class="sure" @click="submitForm('myform',index)">确定</div>
            <div class="cancel" @click="resetForm('myform',index)">取消</div>
          </div>
        </div>
      </ElForm>
    </div>
  </div>
</template>

data() {
  return {
    formDate: [
      {
        id: 0,
        price: "",
        phone: "", 
        company: ""
      },
      {
        id: 0,
        price: "",
        phone: "", 
        company: ""
      }
    ],
    rules: {
      price: [
        { required: true, message: "请输入价格", trigger: "blur" }
      ],
      company: [
        { required: true, message: "请输入公司名称", trigger: "blur" }
      ],
      phone: [
        { required: true, message: "请输入手机号码", trigger: "blur" },
        {
          len: 11,
          pattern: /^1[3-9][0-9]{9}/,
          message: "请输入正确的手机号",
          trigger: "blur"
        }
      ]
    },
  };
},

//重置表单
resetForm(formName,index) {
  this.$refs[formName][index].resetFields();
  this.myform.id = 0;
},
//表单保存
saveForm(formName, index) {
  this.$refs[formName][index].validate((valid) => {
    if (valid) {
      console.log('验证成功！');
    } else {
      console.log('error submit!!');
      return false;
    }
  });
}

```

首先当验证时报错时先看你表单上的ref和保存时refs是否一致。
```js
<el-form ref="myform" :model="formDate" :rules="rules">

this.$refs[formName][index].validate((valid) => {}
```


如果要是循环出多个表单。那么refs输出就是一个数组。所以在验证的时候需要获取到当前表单的下标。
```js
<div class="sure" @click="submitForm('myform',index)">确定</div>

this.$refs[formName][index].validate((valid) => {}
```

[参考文章](https://blog.csdn.net/weixin_40121676/article/details/101269362 "参考文章")