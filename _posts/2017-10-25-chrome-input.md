---
layout:     post
title:      Chrome 下input的默认样式
subtitle:   
date:       2017-08-25
author:     Liangyuqi
header-img: img/post-bg-1.jpg
catalog: true
tags:
    - css
    - 浏览器
---

### 去除默认边框以及padding

	border: none;
	padding:0
 
### 去除聚焦蓝色边框

    outline: none;
 

### 取消form表单自动填充变色

1.给input设置**内置阴影**，至少要比你的input本身大。不过，box-shadow是很慢的，适当大小。而且，如果你的input是用图片做背景的话，是没有办法做这么干的。设置transparent也不可以。

	input:-webkit-autofill,
	    textarea:-webkit-autofill,
	    select:-webkit-autofill {
	        -webkit-box-shadow: 0 0 0 100px white inset; //通过内阴影覆盖默认黄色背景
	        -webkit-text-fill-color: #333;               //去除默认黑色字色
	}
 

2.**关闭自动补全**

	<input type="text"  autocomplete="off">
 

3.**设置背景变换过渡**，可短时间内保持原本背景（支持透明）

 	transition: background-color 5000s ease-in-out 0s; 

### 改变placeholder样式

	input::-webkit-input-placeholder{color:rgba(0,0,0,0.3);}
	input::-moz-input-placeholder{color:rgba(0,0,0,0.3);}
	input::-ms-input-placeholder{color:rgba(0,0,0,0.3);}
	input::-o-input-placeholder{color:rgba(0,0,0,0.3);}
	 