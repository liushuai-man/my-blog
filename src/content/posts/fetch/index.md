---
title: Fetch
published: 2025-10-26
description: '在 JavaScript 中，Fetch 是一个用于发起异步 HTTP 请求的 API。它基于 Promise 对象，提供了更简洁的 API 来处理异步操作。'
tags: ['JavaScript', 'Fetch']
category: 前端
draft: false
---

## **引入**

在前端开发中，我们少不了要通过JavaScript与服务器交互，获取数据并动态更新页面（例如：提交表单，实时更新是数据），而这种从客户端（如浏览器、App）向服务器发送请求以获取数据、提交数据或执行特定操作的过程，也叫做 **网络请求。**

Ajax（Asynchronous JavaScript and XML）是一种 **前端专属的异步网络请求技术**，本质是 “ **在不刷新页面的情况下，通过 JavaScript 发起异步 HTTP 请求并局部更新页面**”。

传统Ajax的是基于 XMLHttpRequest（XHR）实现的，是前端异步请求的基础，兼容性极佳。

其设计符合当时的技术需求，但面对现代前端复杂的异步场景（如多请求依赖、拦截器、优雅的错误处理），显得笨重且低效，存在诸多痛点如下：

1.  API设计繁琐，使用成本高
2.  异步处理导致“回调地狱”
3.  功能简陋，扩展困难
4.  错误处理不直观

Fetch 和 Axios 继承了 XHR 的核心功能（异步请求），但通过更简洁的 API、Promise 支持、内置功能（如自动 JSON 解析、拦截器）解决了 XHR 的痛点，大幅提升了开发效率，因此成为主流选择。

## **Fetch**

fetch方法的执行流程可分为 **发起请求**\=> **处理响应**\=> **解析数据**\=> **异常处理**四个核心阶段

###### **基本语法：**

```javascript
let promise = fetch(url, [options]);
//url:要访问的URL
//options：可选参数：menthod,header等
//如果没有options,便可看作是一个简单的GET请求，下载url的内容
```

那么，假如有options，且我们需要一个指定的HTTP请求方法，我们又该如何？

###### **常用的HTTP方法：**

**POST** 请求（向服务器提交数据，请求创建新资源）

```javascript
fetch(url, { method: 'POST', // 指定 POST 方法
headers: { 'Content-Type': 'application/json' // 声明请求体格式为 JSON},
body: JSON.stringify({ name: '张三', age: 20 }) // 请求体数据（需序列化）
});
```

**PUT** 请求（向服务器提交数据，请求更新资源（全量更新）)

```javascript
// PUT 请求（向服务器提交数据，请求更新资源（全量更新）)
fetch(url, { method: 'PUT',
headers: { 'Content-Type': 'application/json' },
body: JSON.stringify({ name: '张三', age: 21 }) // 完整替换资源 });
```

DELETE 请求（请求删除服务器上的资源）

```javascript
//DELETE 请求（请求删除服务器上的资源）
fetch(url, { method: 'DELETE' });
```

以下是相对不常用的

```javascript
//PATCH 只更新用户的昵称和邮箱，其他字段不变
fetch(url, { method: 'PATCH', // 指定 PATCH 方法
headers: { 'Content-Type': 'application/json', 'Authorization': 'Bearer token'},
 body: JSON.stringify({ nickname: '新昵称', // 仅需修改的字段 email: 'new@example.com'
 })
})

//HEAD 检查一张图片是否存在（只需要响应头）
 fetch('https://api.example.com/images/cover.jpg',
{ method: 'HEAD' // 指定 HEAD 方法
 })

//OPTIONS 查询服务器允许对 /users 资源使用哪些 HTTP 方法 fetch('https://api.example.com/users',
{ method: 'OPTIONS' // 指定 OPTIONS 方法
 })
```

在调用完Fetch方法之后，浏览器立即启动请求，并返回一个Promise对象用来获取结果。

获取响应通常需要经历两个阶段。

**第一个阶段：服务器发送响应头（response header），fetch 返回的 promise 就使用内建的 Response class 对象来对响应头进行解析。**

在这个阶段，我们可以通过检查 **响应头**来检查HTTP状态来确定请求是否成功（此时还没有响应体 response body）

如果fetch无法建立一个HTTP请求，那么promise就会reject

fetch 的 promise 仅在 **网络层面**失败时才会 reject，而对于 **HTTP 协议层面**的错误状态码，它会默认视为 “成功响应” 并进入 resolve 阶段，不会导致出现error

1.  fetch 触发 reject 的场景（网络层错误）
    1.  设备未联网（如 Wi-Fi 断开、无移动数据）。
    2.  目标网址不存在或无法解析（DNS 解析失败）。
    3.  网络超时（请求发送后长时间无响应）。
    4.  跨域请求被浏览器的 CORS 策略拦截（属于浏览器层面的网络拦截）。
2.  fetch 不触发 reject 的场景（HTTP 状态码错误）
    1.  404（请求的资源不存在）。
    2.  500（服务器内部错误）。
    3.  403（权限不足，禁止访问）。
    4.  401（未授权，需要登录）。

我们可以在response的属性中查看HTTP的状态

```javascript
//案例1-1
const url = 'https://randomuser.me/api/';
 let responsePromise = fetch(url);
 responsePromise .then(response => { console.log(response.status);// 200 console.log(response.ok);// true });
```

- **status**：HTTP状态码
- **ok**：布尔值，如果状态码为200-299，则为true

**第二阶段，为了获取 response body，我们需要使用一个其他的方法调用。**

Response 提供了多种基于 promise 的方法，来以不同的格式访问 body：

- response.text() —— 读取 response，并以文本形式返回 response，
- response.json() —— 将 response 解析为 JSON 格式，
- response.formData() —— 以 FormData 对象（在 下一章 有解释）的形式返回 response，
- response.blob() —— 以 Blob（具有类型的二进制数据）形式返回 response，多用于加载图片
- response.arrayBuffer() —— 以 ArrayBuffer（低级别的二进制数据）形式返回 response，

[案例1-2](http://\前端学习\ES6案例\fetch分享会案例\案例02.html '案例1-2')

//案例1-2 let response = await fetch(url); let json = await response.json(); console.log(json);//将响应体读取为JSON格式 let text =await response.text(); console.log(text);//将响应体读取为文本

**注意：**

只能选择一种读取 body 的方法。

如果我们已经使用了  response.json() 方法来获取 response，那么如果再用  response.text()，则不会生效，因为 body 内容已经被处理过了。

TypeError: Failed to execute 'text' on 'Response': body stream already read

之后我们就拿到了我们想要的数据。而具体的fetch方法的执行流程如下图。

###### **fetch的典型请求方式：**

由两个await调用组成：

```javascript
let response = await fetch(url, options); // 解析 response header
let result = await response.json(); // 将 body 读取为 json
```

以promise形式

```javascript
fetch(url, options)
 .then(response => response.json())
 .then(result => /* process result */)
```

## **Axios（\*\***扩展\***\*）**

Axios 是一个基于 promise 网络请求库，作用于node.js 和浏览器中。 它是 isomorphic 的(即同一套代码可以运行在浏览器和node.js中)。在服务端它使用原生 node.js http 模块, 而在客户端 (浏览端) 则使用XMLHttpRequest。除此以外，Axios还具有以下诸多特点。

- 拦截请求和响应
- 转换请求和响应数据
- 取消请求
- 超时处理
- 查询参数序列化支持嵌套项处理

- 自动将请求体序列化为：
  - JSON (application/json)
  - Multipart / FormData (multipart/form-data)
  - URL encoded form (application/x-www-form-urlencoded)
- 将 HTML Form 转换成 JSON 进行请求

- 自动转换JSON数据
- 获取浏览器和 node.js 的请求进度，并提供额外的信息（速度、剩余时间）
- 为 node.js 设置带宽限制

- 兼容符合规范的 FormData 和 Blob（包括 node.js）
- 客户端支持防御[XSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery 'XSRF')

###### **Axios的通用方法：**

axios({ method: 'get', // 请求方法（默认 get） url: 'https://api.example.com/data', // 请求地址 params: { id: 123 }, // GET 参数（拼接到 URL） timeout: 5000 // 超时时间 }).then(response => console.log(response.data));

###### **Axios的实例方法**

通过 const instance = axios.create(config) 创建实例后，实例支持以下方法，简化不同 HTTP 方法的调用。通过创造不同的实例，可以隔绝不同的API的配置。

| 方法别名                               | 对应 HTTP 方法                        |
| -------------------------------------- | ------------------------------------- |
| instance.request(config)               | 通用请求方法（类似全局axios(config)） |
| axios.get(url\[, config\])             | GET                                   |
| axios.post(url\[, data\[, config\]\])  | POST                                  |
| axios.put(url\[, data\[, config\]\])   | PUT                                   |
| axios.patch(url\[, data\[, config\]\]) | PATCH                                 |
| axios.delete(url\[, config\])          | DELETE                                |
| axios.head(url\[, config\])            | HEAD                                  |
| axios.options(url\[, config\])         | OPTIONS                               |

方法参数说明:

url：必传，请求的 URL 地址（字符串）。

data：可选，用于 POST/PUT/PATCH 等方法，作为请求体数据（会自动根据 Content-Type 序化）。

config：可选，请求配置对象，可覆盖默认配置（如 params、headers、timeout 等）。

Axios的请求流程可概括为五个核心步骤：配置整合 => 请求拦截 => 发送请求 => 响应拦截 => 结果返回

## **Ajax、Fetch、Axios的异同**

| 对比维度          | Ajax                                                          | Fetch                                              | Axios                                                                    |
| ----------------- | ------------------------------------------------------------- | -------------------------------------------------- | ------------------------------------------------------------------------ |
| 核心定义          | 异步 JavaScript 和 XML 的技术统称（基于 XMLHttpRequest 实现） | 浏览器原生的 HTTP 请求 API（基于 Promise）         | 基于 Promise 的 HTTP 客户端库（封装 XMLHttpRequest / Node.js http 模块） |
| 本质              | 一种技术概念（非具体 API）                                    | 具体 API（浏览器内置）                             | 第三方库（需引入）                                                       |
| 语法风格          | 基于回调函数（易产生回调地狱）                                | 基于 Promise（支持async/await）                    | 基于 Promise（支持async/await）                                          |
| 是否支持拦截器    | 不支持（需手动封装）                                          | 不支持（需手动封装）                               | 支持（request/response拦截器）                                           |
| 是否自动转换 JSON | 不自动（需手动JSON.parse()）                                  | 不自动（需调用response.json()）                    | 自动（请求自动序列化，响应自动解析）                                     |
| 超时处理          | 需通过setTimeout+abort()实现                                  | 需通过AbortController实现                          | 内置timeout配置                                                          |
| 错误处理          | 需判断readyState和status                                      | 仅网络错误抛错（HTTP 错误码需手动判断response.ok） | 自动捕获所有错误（网络错误 + HTTP 错误码）                               |
| 浏览器兼容性      | 兼容所有主流浏览器（包括 IE6+）                               | 现代浏览器支持（IE 不支持）                        | 依赖浏览器支持Promise（IE 需 polyfill）                                  |
| 适用场景          | 老旧项目、需兼容低版本浏览器                                  | 简单场景、追求轻量无依赖                           | 复杂场景（需拦截器、统一错误处理等）                                     |
