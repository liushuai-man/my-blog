---
title: 快速了解前端中的跨域问题
published: 2026-04-13
description: "在前端开发中，跨域问题是一个高频出现的问题。本文将从跨域产生的原因、解决方案原理、主流方案，到 Node.js、Vue、React 中的实际应用，全方位拆解跨域问题。"
tags: ["跨域", "前端", "HTTP", "XMLHttpRequest"]
category: 前端
draft: false
---

跨域是前端开发中高频出现的问题，尤其在前后端分离项目中，几乎每个开发者都会遇到。本文将严格按照指定框架，从跨域产生的原因、解决方案原理、主流方案，到 Node.js、Vue、React 中的实际应用，全方位拆解跨域问题，新手也能轻松看懂、直接上手。

### 一、跨域为什么会产生

跨域的本质，是 **浏览器的同源策略（Same-Origin Policy）** 导致的 —— 浏览器为了保护用户信息安全，限制了一个页面中的脚本，只能访问与当前页面 “同源” 的资源。

#### 1\. 什么是 “同源”

同源需要满足 **三个完全一致**，缺一不可：

*   协议一致（如 http /https，不能混合）
*   域名一致（如 [www.baidu.com](https://www.baidu.com "www.baidu.com") 和 [blog.baidu.com](https://blog.baidu.com "blog.baidu.com") 不同源）
*   端口一致（如 [localhost:8080](https://localhost:8080 "localhost:8080") 和 [localhost:3000](https://localhost:3000 "localhost:3000") 不同源）

举个直观例子（当前页面地址：`http://localhost:8080`）：

| 目标资源地址 | 是否同源 | 原因 |
| ------------ | -------- | ---- |
| `http://localhost:8080/api` | 是 | 协议、域名、端口完全一致 |
| `https://localhost:8080/api` | 否 | 协议不同（http vs https） |
| `http://localhost:3000/api` | 否 | 端口不同（8080 vs 3000） |
| `http://blog.baidu.com/api` | 否 | 域名不同（localhost vs blog.baidu.com） |

#### 2\. 为什么需要同源策略

同源策略是浏览器的 “安全卫士”，主要防止两类恶意行为：

1.  防止恶意脚本窃取用户信息（如 Cookie、LocalStorage 中的登录信息）；
2.  防止恶意脚本篡改其他网站的内容（如伪造操作、注入恶意代码）。

#### 3\. 跨域的常见场景

前后端分离项目中，跨域几乎是必然的：

*   前端（Vue/React）运行在本地开发服务器（如 [localhost:8080](https://localhost:8080 "localhost:8080")）；
*   后端（Node.js/Java）运行在另一个端口（如 [localhost:3000](https://localhost:3000 "localhost:3000")）；
*   前端请求后端接口时，就会触发跨域限制，浏览器控制台报错（常见：`Access to XMLHttpRequest at 'xxx' from origin 'xxx' has been blocked by CORS policy`）。

### 二、如何解决跨域，以及解决原理

解决跨域的核心思路：**绕开同源策略限制** 或 **让浏览器允许跨域请求**。以下是常见解决方案及核心原理，按 “兼容性从高到低” 排序：

#### 1\. JSONP（最古老的方案）

##### 原理

利用 `script` 标签不受同源策略限制的特性（浏览器加载外部脚本时，不会检查跨域），通过动态创建 `script` 标签，请求后端接口，后端返回一段可执行的 JS 代码（通常是回调函数），前端执行回调函数获取数据。

##### 核心特点

*   仅支持 GET 请求（因为 script 标签只能发起 GET 请求）；
*   安全性低（后端返回的 JS 代码会直接执行，易遭 XSS 攻击）；
*   无完善的错误处理机制（脚本加载失败难以捕获）。

#### 2\. CORS（跨域资源共享，最标准的方案）

##### 原理

后端通过设置 **响应头**，明确告诉浏览器：“允许某个域名的请求访问我”，浏览器收到响应后，会放行该跨域请求。

##### 核心特点

*   支持所有 HTTP 方法（GET、POST、PUT、DELETE 等）；
*   安全性高（可配置允许的域名、请求头、方法）；
*   分为 “简单请求” 和 “复杂请求”（复杂请求会先发起 OPTIONS 预检请求，确认后端允许后再发起真实请求）。

#### 3\. 代理服务器（开发环境首选）

##### 原理

跨域限制是 **浏览器的限制**，服务器之间请求没有跨域限制。因此，在本地开发时，启动一个代理服务器（与前端同源），前端请求代理服务器，代理服务器再转发请求到后端，最后将后端响应返回给前端，间接绕开跨域限制。

##### 核心特点

*   仅用于 **开发环境**（生产环境不适用）；
*   无需修改后端代码，前端配置即可；
*   支持所有 HTTP 方法，无安全风险。

#### 4\. Nginx 反向代理（生产环境首选）

##### 原理

与代理服务器思路类似，通过 Nginx 作为中间层，接收前端所有请求，再转发到后端接口。由于 Nginx 是服务器，不存在跨域限制，同时可以隐藏后端真实地址，提升安全性。

##### 核心特点

*   用于 **生产环境**，性能高、稳定性强；
*   可配置多种规则（如路径匹配、域名转发）；
*   能同时解决跨域和静态资源托管问题。

#### 5\. 其他方案（较少用）

*   `document.domain`：仅适用于 **同主域下的子域名**（如 [a.baidu.com](https://a.baidu.com "a.baidu.com") 和 [b.baidu.com](https://b.baidu.com "b.baidu.com")），通过设置 `document.domain = "baidu.com"` 实现跨域；
*   `postMessage`：主要用于 **跨窗口通信**（如 iframe 之间、不同标签页之间），可传递数据，也能间接解决跨域，但不适用于接口请求。

### 三、现在主流的解决方案

结合当前前端开发趋势，**淘汰 JSONP 等老旧方案**，主流解决方案分为两类（按优先级排序）：

#### 1\. 生产环境：CORS + Nginx 反向代理（首选）

*   核心组合：后端配置 CORS（明确允许前端域名），同时用 Nginx 转发请求，既保证安全，又提升性能；
*   优势：符合标准、安全性高、适配所有场景，是企业级项目的主流选择；
*   适用场景：线上生产环境、前后端分离项目、第三方接口调用。

#### 2\. 开发环境：代理服务器（Vue/React 内置）

*   核心逻辑：利用 Vue CLI、Create React App 内置的代理功能，快速绕开跨域，无需修改后端代码；
*   优势：配置简单、开发效率高，仅用于本地联调，不影响生产环境；
*   适用场景：本地开发、前后端联调、接口测试。

#### 3\. 补充说明

*   不推荐 JSONP：仅在维护老旧第三方接口（仅支持 JSONP）时临时使用；
*   不推荐 `document.domain`/`postMessage`：仅适用于特殊场景（如跨窗口通信），不适用于常规接口请求。

### 四、在 Node.js（主要）、Vue、React 是如何应用的

这部分是重点，提供 **可直接复制使用的代码示例**，兼顾新手友好性，重点讲解 Node.js 后端配置（因为跨域问题的核心解决方是后端），再补充 Vue、React 前端配置。

#### 1\. Node.js 后端（核心，两种常用写法）

Node.js 后端解决跨域，主要通过 **配置 CORS 响应头**，分为 “原生 http 模块” 和 “Express 框架” 两种写法（Express 更常用）。

##### 1.1 Express 框架（推荐，最简单）

先安装 cors 中间件（专门处理跨域）：

```bash
npm install cors
```

核心代码（直接复制到 Express 项目中）：

```javascript
const express = require('express');
const cors = require('cors');
const app = express();

// 方式1：允许所有域名跨域（开发环境可用，生产环境不推荐）
app.use(cors());

// 方式2：允许指定域名跨域（生产环境首选，更安全）
app.use(cors({
  origin: 'http://localhost:8080', // 允许前端 Vue/React 地址跨域
  methods: ['GET', 'POST', 'PUT', 'DELETE'], // 允许的请求方法
  allowedHeaders: ['Content-Type', 'Authorization'], // 允许的请求头
  credentials: true // 允许携带 Cookie（如登录态）
}));

// 测试接口
app.get('/api/test', (req, res) => {
  res.send({ code: 200, message: '跨域请求成功', data: [] });
});

// 监听端口
app.listen(3000, () => {
  console.log('Node.js 服务运行在 http://localhost:3000');
});
```

##### 1.2 原生 http 模块（无框架，理解原理）

无需安装依赖，手动设置响应头：

```javascript
const http = require('http');

const server = http.createServer((req, res) => {
  // 设置 CORS 响应头，允许指定域名跨域
  res.setHeader('Access-Control-Allow-Origin', 'http://localhost:8080');
  res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
  
  // 处理 OPTIONS 预检请求（复杂请求会触发）
  if (req.method === 'OPTIONS') {
    res.writeHead(200);
    res.end();
    return;
  }

  // 测试接口
  if (req.url === '/api/test') {
    res.writeHead(200, { 'Content-Type': 'application/json;charset=utf-8' });
    res.end(JSON.stringify({ code: 200, message: '原生 http 跨域成功' }));
  }
});

server.listen(3000, () => {
  console.log('原生 Node.js 服务运行在 http://localhost:3000');
});
```

#### 2\. Vue 前端（开发环境代理配置）

Vue 项目（Vue 2/Vue 3）使用 Vue CLI 内置的代理，无需修改后端，仅配置前端即可。

##### 配置步骤（vue.config.js）

1.  在项目根目录新建 `vue.config.js` 文件；
2.  复制以下配置（核心是 `devServer.proxy`）：

```javascript
module.exports = {
  devServer: {
    proxy: {
      // 匹配所有以 /api 开头的请求（前端请求路径）
      '/api': {
        target: 'http://localhost:3000', // 后端 Node.js 服务地址
        changeOrigin: true, // 允许跨域（模拟请求来自后端域名）
        pathRewrite: {
          '^/api': '' // 去掉请求路径中的 /api（可选，根据后端接口路径调整）
        }
      }
    }
  }
};
```

**前端请求示例（axios）**

```javascript
import axios from 'axios';

// 直接请求 /api/test，会被代理转发到 http://localhost:3000/test
axios.get('/api/test')
  .then(res => console.log(res.data))
  .catch(err => console.log(err));
```

#### 3\. React 前端（开发环境代理配置）

React 项目（Create React App）有两种代理配置方式，推荐 “配置文件” 方式，简单易懂。

##### 方式 1：package.json 中添加 proxy（简单场景）

直接在 `package.json` 末尾添加一行：

```javascript
{
  "name": "react-app",
  "version": "0.1.0",
  "proxy": "http://localhost:3000" // 后端 Node.js 服务地址
}
```

##### 方式 2：src/setupProxy.js（复杂场景，推荐）

1\. 安装 http-proxy-middleware 依赖：

```javascript
npm install http-proxy-middleware --save-dev
```

2\. 在 src 目录新建 `setupProxy.js` 文件，复制以下配置：

```javascript
const { createProxyMiddleware } = require('http-proxy-middleware');

module.exports = function(app) {
  app.use(
    '/api', // 匹配所有以 /api 开头的请求
    createProxyMiddleware({
      target: 'http://localhost:3000', // 后端服务地址
      changeOrigin: true,
      pathRewrite: { '^/api': '' } // 可选，去掉 /api 前缀
    })
  );
};
```

**前端请求示例（axios）**

```javascript
import axios from 'axios';

axios.get('/api/test')
  .then(res => console.log(res.data))
  .catch(err => console.log(err));
```

#### 补充：生产环境 Vue/React 跨域配置

生产环境中，前端无需配置代理，直接请求后端接口即可 —— 因为生产环境会通过 **Nginx 反向代理** 处理跨域（后端也会配置 CORS），前端只需正常请求接口（如 `http://xxx.com/api/test`），无需额外配置。

### 五、总结

跨域问题的核心是 **浏览器同源策略**，解决跨域的本质是 “绕开限制” 或 “允许跨域”，结合实际开发场景，总结以下核心要点：

1.  跨域产生的原因：浏览器同源策略限制，前后端分离、第三方接口调用是最常见场景；
2.  解决方案原理：要么利用非同源限制的标签（JSONP），要么通过后端 / 中间层允许跨域（CORS、代理、Nginx）；
3.  主流方案：开发环境用 Vue/React 内置代理，生产环境用 CORS + Nginx 反向代理，JSONP 仅用于老旧接口；
4.  多框架应用：Node.js 后端优先用 Express + cors 中间件配置 CORS，Vue/React 前端开发环境配置代理，生产环境依赖 Nginx；
5.  小建议：
    *   开发环境优先用代理，无需修改后端，效率更高；
    *   生产环境必须配置 CORS + Nginx，兼顾安全和性能；
    *   避免使用 JSONP，存在安全风险，且功能有限。

掌握以上内容，就能解决前端开发中 99% 的跨域问题，无论是面试还是实际项目，都能轻松应对。