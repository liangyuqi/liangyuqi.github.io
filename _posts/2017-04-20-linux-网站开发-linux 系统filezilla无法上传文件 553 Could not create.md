---
layout:     post
title:      linux 系统filezilla无法上传文件 553 Could not create
subtitle:   响应：553 Could not create file.  错误：严重文件传输错误
date:       2017-04-20
author:     Liangyuqi
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - linux
    - 网站开发
---

>做网站过程中遇见了很多问题，解决了但是解决方法过一段时间就会遗忘，整理出来以便以后查看。



![](http://images2015.cnblogs.com/blog/1017580/201704/1017580-20170420184855727-1086719316.png)

解决方案：

 **一.必须将站点的文件上传到 /htdocs 目录（windows不用）**

 **二.将属性中的权限修改为777**

附上filezilla初次正确使用方法：

启动FileZilla软件，新建站点：单击 文件 > 站点管理器  > 新站点 。

- 新站点，名字可任意填写，如填写为：新站点。
- 主机，填入主机的IP地址，如：121.XX.XX.XX 。
- 端口，填写21 。
- 协议，选择 FTP-文件传输协议 。
- 加密，选择只使用普通FTP 。
- 登录类型。选择 正常 。
- 用户，填写主机的用户名（主机FTP用户名）。
- 密码，填写主机的FTP密码。