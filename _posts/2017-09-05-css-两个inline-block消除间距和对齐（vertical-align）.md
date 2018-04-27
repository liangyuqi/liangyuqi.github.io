---
layout:     post
title:      两个inline-block消除间距和对齐（vertical-align）
subtitle:   神奇的两个inline-block
date:       2017-09-05
author:     Liangyuqi
header-img: img/post-bg-4.jpg
catalog: true
tags:
    - css
---
> 故事的主人公很简单，两个inline-block元素。代码如下，为了看起来简单明了，写得很简陋。效果图如右。发现有两个问题。

## 前言

       1：两个元素水平有空隙，简单的初始化margin：0好像并没有起想象中的作用，为什么呢

       2：两个元素垂直也没有对齐，等高的的行内块元素不应该阿:

![](http://photo.lustforlife.cn/9.png)
![](http://photo.lustforlife.cn/10.png)
![](http://photo.lustforlife.cn/11.png)

## 消除两个inline-block元素水平间距
最终效果：
![](http://photo.lustforlife.cn/12.png)

demo：

	<div class="space">
	    <a href="##">惆怅</a>
	    <a href="##">淡定</a>
	    <a href="##">热血</a>
	</div>
	
#### 1. 去空格

元素间留白间距出现的原因就是标签段之间的空格，因此，去掉HTML中的空格，自然间距就木有了。考虑到代码可读性，显然连成一行的写法是不可取的，我们可以：

	<div class="space">
	    <a href="##">
	    惆怅</a><a href="##">
	    淡定</a><a href="##">
	    热血</a>
	</div>
或者是：

	<div class="space">
	    <a href="##">惆怅</a
	    ><a href="##">淡定</a
	    ><a href="##">热血</a>
	</div>
或者是借助HTML注释：

	<div class="space">
	    <a href="##">惆怅</a><!--
	    --><a href="##">淡定</a><!--
	    --><a href="##">热血</a>
	</div>
等。

#### 2. 使用margin负值

	.space a {
	    display: inline-block;
	    margin-right: -3px;
	}
例如，对于12像素大小的上下文，Arial字体的margin负值为-3像素，Tahoma和Verdana就是-4像素，而Geneva为-6像素。

由于外部环境的不确定性，以及最后一个元素多出的父margin值等问题，这个方法不适合大规模使用。

#### 3. 让闭合标签吃胶囊

	<div class="space">
	    <a href="##">惆怅
	    <a href="##">淡定
	    <a href="##">热血</a>
	</div>
注意，为了向下兼容IE6/IE7等喝蒙牛长大的浏览器，最后一个列表的标签的结束（闭合）标签不能丢。

在HTML5中，我们直接：

	<div class=space>
	    <a href="##">惆怅
	    <a href="##">淡定
	    <a href="##">热血
	</div>
好吧，虽然感觉上有点怪怪的，但是，这是OK的。

#### 4. 使用font-size:0

类似下面的代码：

	.space {
	    font-size: 0;
	}
	.space a {
	    font-size: 12px;
	}
这个方法，基本上可以解决大部分浏览器下inline-block元素之间的间距(IE7等浏览器有时候会有1像素的间距)。不过有个浏览器，就是Chrome, 其默认有最小字体大小限制，因为，考虑到兼容性，我们还需要添加：
类似下面的代码：

	.space {
	    font-size: 0;
	    -webkit-text-size-adjust:none;
	}
#### 5. 使用letter-spacing

类似下面的代码：

	.space {
	    letter-spacing: -3px;
	}
	.space a {
	    letter-spacing: 0;
	}
#### 6. 使用word-spacing

类似下面代码：

	.space {
	    word-spacing: -6px;
	}
	.space a {
	    word-spacing: 0;
	}

## 消除两个inline-block元素垂直间距
#### 1. 元素在父元素垂直居中：

* 设置父元素line-hight等于父元素高度

* 设置父元素的after伪类，宽度为0，高度为100%的行内块和元素垂直居中，再设置vertical-align: middle;

* 父元素table-cell，再给父子元素设置vertical-align: middle等（[CSS垂直居中的11种实现方式](http://www.cnblogs.com/zhouhuan/p/vertical_center.html)）

#### 2. 什么是基线？

对于inline-block元素来说，他的基线取决于元素本身的特性，

<font color=red>在该元素中没有行内子元素（注意是DOM树的子元素，块子元素中有行元素这个行元素算）的时候或者overflow不为visible，该inline-block的基线为margin-bottom的下边界。

否则，以该元素中最后一个行内子元素的基线为该元素的基线。</font>

**字母x的下边缘(线)就是我们的基线baseline**。（以下是几种默认的基线对齐形式）

![](http://photo.lustforlife.cn/13.png)![](http://photo.lustforlife.cn/14.png)
![](http://photo.lustforlife.cn/15.png)

#### 2. 元素是怎么垂直对齐的？
**vertical-align设置行内元素的基线相对于该元素所在行的基线的垂直对齐方式。**

如a设置middle，b默认。则a的middle和b的bsaeline对齐

**vertical-align：middle指的是baseline + 1/2 "x-height"高度。我们可以近似脑补成字母x交叉点那个位置。**

	vertical-align设置行内元素的基线相对于该元素所在行的基线的垂直对齐方式。
	
	如a设置middle，b默认。则a的middle和b的bsaeline对齐
	
	vertical-align：middle指的是baseline + 1/2 "x-height"高度。我们可以近似脑补成字母x交叉点那个位置。
	vertical-align:baseline(，默认元素的基线与父元素的基线对齐)
	vertical-align:sub(降低元素的基线到父元素合适的下标位置)
	vertical-align:super(升高元素的基线到父元素合适的上标位置)
	vertical-align:bottom(把对齐的子元素的底端与行框底端对齐)
	vertical-align:text-bottom(把元素的底端与父元素内容区域的底端对齐，当line-height设置时与bottom显示不同)
	vertical-align:top(把对齐的子元素的顶端与行框顶端对齐)
	vertical-align:text-top(把元素的顶端与父元素内容区域的顶端对齐)
	vertical-align:middle(元素的中垂点与父元素的基线加1/2父元素中字母X的高度对齐)
	
	vertical-align:(+-n)px(元素相对于基线上下偏移npx)
	vertical-align:x%(相对于元素的line-height值)
	vertical-align:inherit(从父元素继承vertical-align属性的值)
	
举个例子，正常div，高度自适应，并且有一张图片。
![](http://photo.lustforlife.cn/16.png)

会发现有一端留白，这段空白间隙就是vertical-align和line-height携手搞的鬼。

实际上块状元素内部还有一个（更有可能两个-前后）看不见摸不着没有宽度没有实体的空白节点

为了明显一点这个宽度为0的空格元素我们用inline-block来代替

可以看到图片的下边缘就和字母x下边缘对齐（见下图）

**但是字符还是有高度的，由line-height决定（默认normal，1.2倍font-size）**

![](http://photo.lustforlife.cn/17.png)

**1. 让vertical-align失效**

将图片设置成display或者浮动、绝对定位等（如果布局允许）

**2. 使用其他vertical-align值**

告别baseline, 取用其他属性值，比方说bottom/middle/top都是可以的。

**3. 直接修改父元素line-height值**

空白部分其实是是文字计算后的行高底部和字母x下边缘的距离，如把line-height设置为0

**4. line-height为相对单位，font-size间接控制**

原理同上，如把font-size设置为0


③ 基本现象衍生：垂直居中

**图片后面（前面）有个类似空格字符的节点，然后就能响应line-height形成高度，此时，图片再来个vertical-align:middle，就可以和这个被行高撑高的「幽灵空白节点」(近似)垂直对齐了。**

不过上面的效果并不是完全的垂直居中，因为**middle中线位置(字符x的中心)并不是字符内容的绝对居中位置**

![](http://photo.lustforlife.cn/18.png)

	div { line-height: 240px; font-size: 0; }
	img { vertical-align: middle; }
 **font-size:0, 因此此时content area高度是0，各种乱七八糟的线都在高度为0的这条线上，绝对中心线和中线重合。自然全垂直居中：**

