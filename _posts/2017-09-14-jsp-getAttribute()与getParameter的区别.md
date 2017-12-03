---
layout:     post
title:      getAttribute()与getParameter的区别
subtitle:   服务器端与客户端交互数据
date:       2017-09-14
author:     Liangyuqi
header-img: img/post-bg-9.jpg
catalog: true
tags:
    - jsp
---

> 当两个Web组件之间为转发关系时，转发源会将要共享 request范围内的数据先用setAttribute将数据放入到HttpServletRequest对象中，然后转发目标通过 getAttribute方法来取得要共享的数据。而MVC中用的就是Web组件之间的转发。 
下面整理一下getParameter和getAttribute的区别和各自的使用范围。

1）**HttpServletRequest类有setAttribute()方法，而没有setParameter()方法**

2）**getParameter（）用于用于客户端重定向时**，即当两个Web组件之间为链接关系时，被链接的组件通过getParameter()方法来获得请求参数，

例如假定welcome.jsp和authenticate.jsp之间为链接关系，在authenticate.jsp中通过request.getParameter("username")方法来获得请求参数username:

	<% String username=request.getParameter("username"); %>

3）**getAttribute()用于服务器端重定向时**，即两个Web组件之间为转发关系时，转发目标组件通过getAttribute()方法来和转发源组件共享request，session范围内的数据。

假定 authenticate.jsp和hello.jsp之间为转发关系。authenticate.jsp希望向hello.jsp传递当前的用户名字， 如何传递这一数据呢？先在authenticate.jsp中调用setAttribute()方法：

	<%
	String username=request.getParameter("username");
	request.setAttribute("username"，username);
	%>

在hello.jsp中通过getAttribute()方法获得用户名字:

	<% String username=(String)request.getAttribute("username"); %>
	Hello: <%=username %>

从更深的层次考虑，

**request.getParameter()方法传递的数据**，会从Web客户端传到Web服务器端，**代表HTTP请求数据**。request.getParameter()方法返回**String类型**的数据。

**request.setAttribute()和getAttribute()方法传递的数据只会存在于Web容器内部，在具有转发关系的Web组件之间共享。这两个方法能够设置Object类型的共享数据。**

**request.getParameter()取得是通过容器的实现来取得通过类似post，get等方式传入的数据**。

request.setAttribute()和getAttribute()只是在web容器内部流转，仅仅是请求处理阶段。

getAttribute是返回对象,getParameter返回字符串

 

总的来说：**request.getAttribute()方法返回request，session范围内存在的对象，而request.getParameter()方法是获取http提交过来的数据**。