---
title: 前端页面跳转的六个方法
published: 2025-11-09
description: "在前端开发中，页面跳转是常见的操作，有多种方法可以实现。本文将介绍六种常用的页面跳转方法。"
tags: ["HTML"]
category: 前端
draft: false
---

1.a标签
-----

```html
<a href="https://www.baidu">跳转</a>
<!-- target属性可以设置打开方式 -->
```

2.直接用js实现
---------

```javascript
function imitateClick(url){
	  let aEle = document.createElement("a");
	  aEle.setAttribute("href", url);
	  aEle.setAttribute("target", "_blank");
	  aEle.setAttribute("id", "previewJumpEle");
	  // 防止重复添加
	  if (!document.getElementById("previewJumpEle")) {
	    document.body.appendChild(aEle);
	  }
	  // 模拟点击
	  aEle.click();
	  (aEle.remove && aEle.remove()) || (aEle.removeNode && aEle.removeNode(true));
};
imitateClick('https://www.baidu.com');
```

js中直接做无感跳转，但是此方法有个弊端：经过验证，有的浏览器可能会拦截

3.js中window对象location属性跳转
-------------------------

```javascript
window.location.href = 'https://www.baidu.com';

top.window.location.href = 'https://www.baidu.com';
```

window.location.href是覆盖当前页面跳转，在iframe嵌套内页跳转只能改变iframe的src。top.window.location.href是覆盖顶层地址跳转，在iframe嵌套内页跳转可以覆盖顶层地址打开新页面，且浏览器无拦截。

4.js中window对象的open方法跳转
----------------------

```javascript
//打开新窗口
window.open('https://www.baidu.com');
//打开一个空白窗口（类似弹窗）
window.open('https://m.book118.com','','left=200,top=200,width=200,height=100');
```

window.open与top.window.open效果是一样的，都是新窗口打开页面；

window.open指定窗口特征，可当弹窗使用。

window.open在iframe中使用会被浏览器拦截。

5.重定向
-----

```javascript
window.location.replace('https://www.baidu.com');
top.window.location.replace('https://www.baidu.com');
```

window.location.replace()在iframe中使用只是重定向src地址。

top.window.location.replace()使用效果和top.window.location.href效果一样，可以在iframe中使用，且浏览器无拦截。

6.meta
------

```javascript
<meta http-equiv="refresh" content="5;url=http://www.w3school.com.cn">
```

content="5;url=http://www.w3school.com.cn"中的5代表5秒，url代表跳转地址。