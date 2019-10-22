---
title: JavaScript中深拷贝与浅拷贝的理解
date: 2019-10-08 09:08:15
tags: javascript
author: 刘涛
---

## 前言
之所以会出现了深拷贝和浅拷贝，究其根本是因为JS种的变量包含了不同类型的数据值：基本类型和引用类型；同时其中变量的存储方式也不用Object属于堆内存的储存方式；并且JS的值的传递方式如值传递和址传递的会出现不同，所以才会出现这一系列的问题，因此才会使用深拷贝和浅拷贝来解决这些问题。


## 一、理解堆栈，基本数据类型和引用数据类型
  **1.堆栈**
    栈（stack）：系统自动分配的内存空间，内存会由系统自动释放，用来存放函数的参数值，局部变量的值等，特点是先进后出。<br>
    堆堆（heap）：系统动态分配的内存，内存大小不一，内存不会自动释放。一般由程序员分配释放，主要负责像Obejct这种变量类型的存储。<br>
  **2.基本数据类型**
    概念：存放在栈内存中的简单数据段，数据大小确定，内存空间大小确定
    6种基本数据类型：Undefined、Null、Boolean、Number、String、Symbol<br>
  **3.引用数据类型**
    概念：存放在堆内存中的数据，如对象、数组、函数等。名存在栈内存，值存在堆内存，栈内存会提供一个引用的地址指向堆内存中的值
  

## 二、JavaScript浅拷贝
浅拷贝：只复制一层对象属性，可以理解为，只复制对象的基本属性类型，而基本属性类型是存放在栈内存中的，可以直接修改访问的，所以当浅拷贝时，拷贝的对象只是拷贝了原对象的属性，而值都是指向同一个栈内存中的数据，当对象属性值发生修改时，原对象也会被修改。如下示例：
```JavaScript
 let arr1 = {
        color:'red',
        name:'apple',
        weight:'100g',
                detail:{
                big:'true',
                eating:'no'
                }
      };
 let arr2;
 arr2 = arr1;
 arr2.color = 'black';
 console.log(arr2);  
 console.warn(arr1);
  ```
输出值：
![浅拷贝](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/js%E7%9A%84%E6%B7%B1%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B5%85%E6%8B%B7%E8%B4%9D/%E6%B5%85%E6%8B%B7%E8%B4%9D.png)


## 三、JavaScript深拷贝
深拷贝：它不仅将原对象的各个属性逐个复制出去，而且将原对象各个属性所包含的对象也依次采用深复制的方法递归复制到新对象上。这就不会存在上面 obj 和 shallowObj 的 arr 属性指向同一个对象的问题。这是我从某官网看来的解释，我感觉挺绕的，结合对象来看，深拷贝存在于对象这种引用类型中，而引用类型的数据是存在堆内存上的，变量保存的是一个指针，这个指针指向另一个位置。当需要访问引用类型（如对象，数组等）的值时，首先从栈中获得该对象的地址指针，然后再从堆内存中取得所需的数据。深拷贝可以看做是在堆内存中新开辟了一个内存空间，复制后的对象的属性所指的指针会指向新开辟的内存空间的地址。所以复制后的对象对属性值进行修改不会影响原对象的属性值。



## 四、实现深拷贝的方式
1.借用JSON对象的 parse 和 stringify(接上面的例子)
```JavaScript
 let arr3 = JSON.parse(JSON.stringify(arr1));
arr3.color="balck";
console.log(arr3);
console.warn(arr1);
  ```
  输出结果

![JSON对象的深拷贝](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/js%E7%9A%84%E6%B7%B1%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B5%85%E6%8B%B7%E8%B4%9D/json%E6%B7%B1%E6%8B%B7%E8%B4%9D.png)

2.通过for循环递归调用
```JavaScript
 function deepClone(obj){
           //判断obj是否是数组
        let objClone = Array.isArray(obj)?[]:{};  
        if(obj && typeof obj==="object"){
            for(key in obj){
                if(obj.hasOwnProperty(key)){
                    //判断ojb子元素是否为对象，如果是，递归复制
                    if(obj[key]&&typeof obj[key] ==="object"){
                        objClone[key] = deepClone(obj[key]);
                    }else{
                        //如果不是，简单复制
                        objClone[key] = obj[key];
                    }
                }
            }
        }
        return objClone;
    } 
    var arr4 = deepClone(arr1);
    arr4.name='bananer';
    console.log(arr4);
    console.warn(arr1);
  ```
    输出结果

![for循环的深拷贝](https://images-1300364015.cos.ap-shanghai.myqcloud.com/blogs/js%E7%9A%84%E6%B7%B1%E6%8B%B7%E8%B4%9D%E4%B8%8E%E6%B5%85%E6%8B%B7%E8%B4%9D/for%E5%BE%AA%E7%8E%AF%E9%80%92%E5%BD%92%E6%B7%B1%E6%8B%B7%E8%B4%9D.jpg)

3.封装深拷贝函数
```JavaScript
 function deepClone(obj) {
    let objClone = Array.isArray(obj) ? [] : {};
    if(obj && typeof obj === "object") {
        for(key in obj) {
            if(obj.hasOwnProperty(key)) {
                 // 判断 obj 是否是对象,如果是，递归复制
                 if(obj[key] && typeof obj[key] === "object") {
                      objClone[key] = deepClone(obj[key]);
                 }else{
                      // 如果不是
                      objClone[key] = obj[key];
                 }
            }
        }
    }      
    return objClone
}         

let a = [1,2,3,4],
     b = deepClone(a);
a[0] = 5;
console.log(a,b)
  ```




















