---
layout:     post
title:      npm vs yarn 
subtitle:   
date:       2018-6-20

author:     Liangyuqi
header-img: img/post-bg-2.jpg
catalog: true
tags:
    - node
---

### 一、npm

npm升级到5会有一些变化
npm 5 的一些大的变化：

   根据官方文档，这个package-lock.json 是在 `npm install`时候生成一份文件，用以记录当前状态下实际安装的各个npm package的具体来源和版本号。

   它有什么用呢？因为npm是一个用于管理package之间依赖关系的管理器，它允许开发者在pacakge.json中间标出自己项目对npm各库包的依赖。你可以选择以如下方式来标明自己所需要库包的版本

   这里举个例子：

	"dependencies": {
	 "@types/node": "^8.0.33",
	},

   这里面的 向上标号^是定义了向**后（新）兼容依赖**，指如果 types/node的版本是超过8.0.33，并在大版本号（8）上相同，就允许下载最新版本的 types/node库包，例如实际上可能运行npm install时候下载的具体版本是8.0.35。

   大多数情况这种向新兼容依赖下载最新库包的时候都没有问题，可是因为npm是开源世界，各库包的版本语义可能并不相同，有的库包开发者并不遵守严格这一原则：相同大版本号的同一个库包，其接口符合兼容要求。这时候用户就很头疼了：在完全相同的一个nodejs的代码库，在不同时间或者不同npm下载源之下，下到的各依赖库包版本可能有所不同，因此其依赖库包行为特征也不同有时候甚至完全不兼容。

   因此npm最新的版本就开始提供自动生成package-lock.json功能，为的是让开发者知道只要你保存了源文件，到一个新的机器上、或者新的下载源，只要按照这个**package-lock.json所标示的具体版本下载依赖库包，就能确保所有库包与你上次安装的完全一样**。
   
   npm5之前的版本其实也有这样的功能， 但需要开发者手动执行 npm shrinkwrap 命令。这个命令将会生成一个npm-shrinkwrap.json锁定文件，在执行 npm install 的时候，该锁定文件会先被读取。
   
   总结一下：原来package.json文件只能锁定大版本，也就是版本号的第一位，并不能锁定后面的小版本，你每次npm install都是拉取的该大版本下的最新的版本，为了稳定性考虑我们几乎是不敢随意升级依赖包的，这将导致多出来很多工作量，测试/适配等，所以package-lock.json文件出来了，当你每次安装一个依赖的时候就锁定在你安装的这个版本。
  
   > 如果要修改版本呢，在以前可能就是直接改package.json里面的版本，然后再npm install了，但是5版本后就不支持这样做了，因为版本已经锁定在package-lock.json里了，所以我们只能npm install xxx@x.x.x  这样去更新我们的依赖，然后package-lock.json也能随之更新。
   
###二、yarn
   
   Facebook、Google、Exponent 和 Tilde 联合推出了一个新的 JS 包管理工具 — Yarn，yarn 是为了弥补 npm 的一些缺陷而出现的,它只是一个新的 CLI 工具，拉取的 packages 依然来自 npm 仓库。仓库本身不会变，所以获取或者发布模块的时候和原来一样。
   
   1. **yarn.lock 文件**：为了防止拉取到不同的版本，Yarn 有一个默认锁定文件 (lock file) 记录了被确切安装上的模块的版本号。每次只要新增了一个模块，Yarn 就会创建（或更新）yarn.lock 这个文件。这么做就保证了，每一次拉取同一个项目依赖时，使用的都是一样的模块版本。
   
   2. **并行安装**：无论 npm 还是 Yarn 在执行包的安装时，都会执行一系列任务。npm 是按照队列执行每个 package，也就是说必须要等到当前 package 成功安装之后，才能继续后面的安装。而 Yarn 是同步执行所有任务，提高了性能。
   3. **更简洁的输出**：
npm 的输出信息比较冗长。在执行 npm install <package> 的时候，命令行里会不断地打印出所有被安装上的依赖。相比之下，Yarn 简洁太多：默认情况下，结合了 emoji （Windows 上 emoji 不可见）直观且直接地打印出必要的信息，也提供了一些命令供开发者查询额外的安装信息。
![](http://photo.lustforlife.cn/52.png)
  
  4. **cli区别**：
      1. yarn global:npm 的全局操作命令要加上 -g 或者 --global 参数，Yarn 的全局命令则需要加上 global。和 npm 类似，项目特定的依赖，就不需要全局安装了。当执行 yarn add、yarn bin、yarn ls 和 yarn remove 时添加 global 前缀才是有全局作用。除了 yarn add 之外，其他三个命令和 npm 的一样。
      2. yarn install:npm install 命令安装的是 package.json 中的依赖，如果开发者在 package.json 中添加了新的依赖，npm install 也一样安装。然而，yarn install 会优先安装 yarn.lock 中记录的依赖，没有这样的锁定文件时，才会去安装 package.json 中的依赖。
      3. yarn add [–dev]:和 npm install 类似，yarn add 命令允许你添加并安装依赖。通过这个命令添加的依赖都会被自动加到 package.json 中，和我们在 npm 命令中使用 --save 参数一样。Yarn 的-dev 则等同于 npm 的 --save-dev。