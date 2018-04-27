---
layout:     post
title:      jq／fetch／axios／vue-resource／fly
subtitle:   对比一下主流的http库
date:       2018-4-20
author:     Liangyuqi
header-img: img/post-bg-19.jpg
catalog: true
tags:
    - javascript
---

### 一、jquery ajax

    $.ajax({
        type: 'POST',
        url: url,
        data: data,
        dataType: dataType,
        success: function() {},
        error: function() {}
    })

它是对原生XHR的封装，还支持JSONP，非常方便；真的是用过的都说好。但是随着react，vue等前端框架的兴起，jquery早已不复当年之勇。很多情况下我们只需要使用ajax，但是却需要引入整个jquery，这非常的不合理，于是便有了fetch的解决方案。

### 二、fetch
fetch号称是ajax的替代品，它的API是基于Promise设计的，旧版本的浏览器不支持Promise，需要使用polyfill es6-promise

> 举个例子对比一下

原生XHR:

    var xhr;
    if (window.XMLHttpRequest) {　 // Mozilla, Safari...
      　xhr = new XMLHttpRequest();
    } else if (window.ActiveXObject) { // IE
      　try {
        　xhr = new ActiveXObject('Msxml2.XMLHTTP');
     　 } catch (e) {
      　  try {
        　  xhr = new ActiveXObject('Microsoft.XMLHTTP');
       　 } catch (e) {}
     　 }
    }
    if (xhr) {
      　xhr.onreadystatechange = onReadyStateChange;
     　 xhr.open('POST', '/api', true);
     　 // 设置 Content-Type 为 application/x-www-form-urlencoded
     　 // 以表单的形式传递数据
      　xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
     　 xhr.send('username=admin&password=root');
    }
    
    // onreadystatechange 方法
    function onReadyStateChange() {
     　 // 该函数会被调用四次
      　console.log(xhr.readyState);
      　if (xhr.readyState === 4) {
       　　 // everything is good, the response is received
        　　if (xhr.status === 200) {
          　　　console.log(xhr.responseText);
        　　} else {
          　　　console.log('There was a problem with the request.');
       　　 }
      　} else {
        　　// still not ready
        　　console.log('still not ready...');
      　}
    }

fetch:

    fetch(url)
        .then(response => {
            if (response.ok) {
                response.json()
            }
        })
        .then(data => console.log(data))
        .catch(err => console.log(err))
        
        

可以总结一下**fetch的优点**：

- 接口更简单、简洁，更加语义化
- 基于promise，更加好的流程化控制，可以不断then把参数传递，外加 async/await，异步变同步的代码书写风格
 
        async function test() {
            let response = await fetch(url);
            let data = await response.json();
            console.log(data)
        }

- 利于同构，isomorphic-fetch是对whatwg-fetch和node-fetch的一种封装，你一份代码就可以在两种环境下跑起来了
- 新的web api很多内置支持fetch，比如 service worker

**fetch的缺点**

- 兼容性（引入[es6-promise][1] 或者 [promise][2]）
- 不支持progress进度事件（可以借助response.body.getRender方法来实现）
- 默认不带cookie（要发送 cookies，必须设置 credentials 选项）
- 某些错误的http状态下如400、500等不会reject，相反它会被resolve
- 不支持timeout超时处理
- 不支持jsonp，当然可以引入 [fetch-jsonp][3] 来支持
- 由于fetch是比较底层的API，所以需要我们手动将参数拼接成'name=test'的格式，而jquery ajax已经封装好了。所以fetch并不是开箱即用的

        // jquery ajax
        $.post(url, {name: 'test'})
        
        // fetch
        fetch(url, {
            method: 'POST',
            body: Object.keys({name: 'test'}).map((key) => {
                return encodeURIComponent(key) + '=' + encodeURIComponent(params[key]);
            }).join('&')
        })
        
### 三、vue-resource

vue-resource是Vue.js的一款插件，它可以通过XMLHttpRequest或JSONP发起请求并处理响应。也就是说，$.ajax能做的事情，vue-resource插件一样也能做到，而且vue-resource的API更为简洁。另外，vue-resource还提供了非常有用的inteceptor功能，使用inteceptor可以在请求前和请求后附加一些行为，比如使用inteceptor在ajax请求时显示loading界面。

基于全局Vue对象使用http

    Vue.http.get('/someUrl', [options]).then(successCallback, errorCallback);
    Vue.http.post('/someUrl', [body], [options]).then(successCallback, errorCallback);

在一个Vue实例内使用$http

    this.$http.get('/someUrl', [options]).then(successCallback, errorCallback);
    this.$http.post('/someUrl', [body], [options]).then(successCallback, errorCallback);

then方法的回调函数也有两种写法，第一种是传统的函数写法，第二种是更为简洁的ES 6的Lambda写法：
    
    // 传统写法
    this.$http.get('/someUrl', [options]).then(function(response){
        // 响应成功回调
    }, function(response){
        // 响应错误回调
    });

    // Lambda写法
    this.$http.get('/someUrl', [options]).then((response) => {
        // 响应成功回调
    }, (response) => {
        // 响应错误回调
    });
    
### 四、axios

vue自2.0开始，vue-resource不再作为官方推荐的ajax方案，转而推荐使用axios

它也是对原生XHR的封装。有以下几大特性：

- 从浏览器中创建 XMLHttpRequests
- 从 node.js 创建 http 请求
- 支持 Promise API
- 拦截请求和响应 （就是有interceptor拦截器）
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 XSRF

ps：介绍一下interceptor（拦截器），它可以在请求发送前和发送请求后做一些处理。

![][4]

简单使用：

    axios({
        method: 'GET',
        url: url,
    })
    .then(res => {console.log(res)})
    .catch(err => {console.log(err)})
    
并发请求：

    function getUserAccount() {
      return axios.get('/user/12345');
    }
    
    function getUserPermissions() {
      return axios.get('/user/12345/permissions');
    }
    
    axios.all([getUserAccount(), getUserPermissions()])
      .then(axios.spread(function (acct, perms) {
        // Both requests are now complete
      }));

使用流程：

    //首先我们来创建一个Axios实例
    
    var axiosIns = axios.create({
      baseURL: 'https://some-domain.com/api/',
      timeout: 1000,
      headers: {'X-product': 'h5'}
    });
    
    //设置request拦截器
    axiosIns.interceptors.request.use((request) => {
    //在这里处理request，可以对所有请求统一处理请求头等
    })
    
    //设置response拦截器
    axiosIns.interceptors.response.use((response) => {
           //在这里处理response，这是全局的，对所有使用axios的请求起作用
     });
    
    //可以将上边的创建实例和设置的拦截器封装成函数，之后直接调用就Ok
    
    //实例方法
    //以下是可用的实例方法。指定的配置将与实例的配置合并
    axios#request(config)
    axios#get(url[, config])
    axios#delete(url[, config])
    axios#head(url[, config])
    axios#post(url[, data[, config]])
    axios#put(url[, data[, config]])
    axios#patch(url[, data[, config]])
    
    //请求配置
    
    {
      // `url` 是用于请求的服务器 URL
      url: '/user',
    
      // `method` 是创建请求时使用的方法
      method: 'get', // 默认是 get
    
      // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
      // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
      baseURL: 'https://some-domain.com/api/',
    
      // `transformRequest` 允许在向服务器发送前，修改请求数据
      // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
      // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
    
      transformRequest: [function (data) {
        // 对 data 进行任意转换处理
        return data;
      }],
    
      // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
    
      transformResponse: [function (data) {
        // 对 data 进行任意转换处理
        return data;
      }],
    
      // `headers` 是即将被发送的自定义请求头
      headers: {'X-Requested-With': 'XMLHttpRequest'},
    
      // `params` 是即将与请求一起发送的 URL 参数
      // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
    
      params: {
        ID: 12345
      },
    
      // `paramsSerializer` 是一个负责 `params` 序列化的函数
      // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
    
      paramsSerializer: function(params) {
        return Qs.stringify(params, {arrayFormat: 'brackets'})
      },
    
      // `data` 是作为请求主体被发送的数据
      // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
      // 在没有设置 `transformRequest` 时，必须是以下类型之一：
      // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
      // - 浏览器专属：FormData, File, Blob
      // - Node 专属： Stream
    
      data: {
        firstName: 'Fred'
      },
    
      // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
      // 如果请求话费了超过 `timeout` 的时间，请求将被中断
      timeout: 1000,
    
      // `withCredentials` 表示跨域请求时是否需要使用凭证
      withCredentials: false, // 默认的
    
      // `adapter` 允许自定义处理请求，以使测试更轻松
      // 返回一个 promise 并应用一个有效的响应 (查阅 [response docs](#response-api)).
      adapter: function (config) {
        /* ... */
      },
    
      // `auth` 表示应该使用 HTTP 基础验证，并提供凭据
      // 这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization`头
      auth: {
        username: 'janedoe',
        password: 's00pers3cret'
      },
    
      // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
      responseType: 'json', // 默认的
    
      // `xsrfCookieName` 是用作 xsrf token 的值的cookie的名称
      xsrfCookieName: 'XSRF-TOKEN', // default
    
      // `xsrfHeaderName` 是承载 xsrf token 的值的 HTTP 头的名称
      xsrfHeaderName: 'X-XSRF-TOKEN', // 默认的
    
      // `onUploadProgress` 允许为上传处理进度事件
      onUploadProgress: function (progressEvent) {
        // 对原生进度事件的处理
      },
    
      // `onDownloadProgress` 允许为下载处理进度事件
      onDownloadProgress: function (progressEvent) {
        // 对原生进度事件的处理
      },
    
      // `maxContentLength` 定义允许的响应内容的最大尺寸
      maxContentLength: 2000,
    
      // `validateStatus` 定义对于给定的HTTP 响应状态码是 resolve 或 reject  promise 。如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，promise 将被 resolve; 否则，promise 将被 rejecte
      validateStatus: function (status) {
        return status >= 200 && status < 300; // 默认的
      },
    
      // `maxRedirects` 定义在 node.js 中 follow 的最大重定向数目
      // 如果设置为0，将不会 follow 任何重定向
      maxRedirects: 5, // 默认的
    
      // `httpAgent` 和 `httpsAgent` 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。允许像这样配置选项：
      // `keepAlive` 默认没有启用
      httpAgent: new http.Agent({ keepAlive: true }),
      httpsAgent: new https.Agent({ keepAlive: true }),
    
      // 'proxy' 定义代理服务器的主机名称和端口
      // `auth` 表示 HTTP 基础验证应当用于连接代理，并提供凭据
      // 这将会设置一个 `Proxy-Authorization` 头，覆写掉已有的通过使用 `header` 设置的自定义 `Proxy-Authorization` 头。
      proxy: {
        host: '127.0.0.1',
        port: 9000,
        auth: : {
          username: 'mikeymike',
          password: 'rapunz3l'
        }
      },
    
      // `cancelToken` 指定用于取消请求的 cancel token
      // （查看后面的 Cancellation 这节了解更多）
      cancelToken: new CancelToken(function (cancel) {
      })
    }


详情见：[axios全攻略][5]

### 五、fly.js

Fly.js 通过在不同 JavaScript 运行时通过在底层切换不同的 **Http Engine来实现多环境支持**，但同时对用户层提供**统一、标准的Promise API**。不仅如此，Fly.js还支持请求/响应拦截器、自动转化JSON、请求转发等功能，详情请参考：[https://github.com/wendux/fly][6]

之前leader的作品，大概研读了实现源码，http engine配合全局ajax拦截， 我觉得还是很有想象力的， 附上[fly.js开发文档][7]


  [1]: https://github.com/stefanpenner/es6-promise
  [2]: https://github.com/then/promise
  [3]: https://github.com/camsong/fetch-jsonp
  [4]: http://photo.lustforlife.cn/51.png
  [5]: https://ykloveyxk.github.io/2017/02/25/axios%E5%85%A8%E6%94%BB%E7%95%A5/#more
  [6]: https://github.com/wendux/fly
  [7]: https://wendux.github.io/dist/#/doc/flyio/compare