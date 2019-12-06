---
title: 常用CSS
date: 2019-12-06 13:59:49
author: 胡豪
categories:
  - CSS
tags:
  - CSS
---

###常用CSS汇总
```css
//div里放入input的css
.inputBox input{
  outline: none;
  -webkit-appearance: none;
  border-radius: 0;
  border:0;
  text-indent:10px;
}

//css三角形
.progress .progress-value:after {
    content: "";
    border-top: 7px solid #393a3d;
    border-left: 7px solid transparent;
    border-right: 7px solid transparent;
    position: absolute;
    bottom: -7px;
    left: 35%;
}

Overflow参数： 
visible : 　不剪切内容也不添加滚动条。假如显式声明此默认值，对象将被剪切为包含对象的window或frame的大小。并且clip属性设置将失效
auto : 　此为body对象和textarea的默认值。在需要时剪切内容并添加滚动条
hidden : 　不显示超过对象尺寸的内容
scroll : 　总是显示滚动条


适配手机屏幕代码
<metaname="viewport"content="width=device-width,initial-scale=1">


//手机/微信 开发必备 插件 网站
http://www.swiper.com.cn/

table 中td占位多行多列的方法
colspan 单元格占多少列
rowspan 单元格占多少行


input number 去掉后面的那个 上下箭头
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

input[type="number"] {
  -moz-appearance: textfield;
}

//文本超出省略...
{
  text-overflow:ellipsis;
  overflow:hidden;
  white-space: nowrap;
}

/*这个是多行超出隐藏的*/
{
  text-overflow: ellipsis;
  -webkit-line-clamp:3;
  display: -webkit-box;     
}


/*  png、jpg制作 font-class 步骤（效果不是很好）*/
  网站：http://www.pngtosvg.com/   将 jpg、png转换为 svg
  网站：http://iconfont.cn/   批量上传到阿里的svg库，然后转换为字体文件下载
  css 直接 应用


/* 使用png转font-class方法 */
  1、使用软件inkscape软件，首先  “文件”“打开”“png文件”----》“另存为”“inkscape SVG”类型文件
  2、“文件”“导入”“刚才保存的svg图片”--> “路径”“提取位图轮廓”“更新确定”“保存”
  3、依然使用网站 http://iconfont.cn/ 批量上传到阿里的svg库，然后转换为文字文件下载
  4、打开font里面的demo，根据demo直接使用

```