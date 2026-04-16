---
title: 获取DOM元素节点的常用方法
published: 2025-09-26
description: "在学习到 DOM 阶段时,得知几种常用的获取 DOM 元素节点的方法,为方便记忆,进行总结。"
tags: ["DOM", "常用方法"]
category: 前端
draft: false
---

#在学习到DOM阶段时,得知了几种常用的获取DOM元素节点的方法,为方便记忆,进行总结#

1.通过ID获取(唯一元素)
--------------

返回匹配指定 ID 的第一个元素（不存在则返回 null）

const element = document.getElementById('id名称');

**特点**：ID 在文档中应唯一，效率高.

2.通过类名获取(多个元素)
--------------

// 返回匹配指定类名的所有元素（HTMLCollection 集合）

const elements = document.getElementsByClassName('类名');

// 或在指定父元素下查找

const elements = 父元素.getElementsByClassName('类名');

**特点** : 返回动态集合 (DOM变化实时反馈)

3.通过标签名获取(多个元素)
---------------

// 返回匹配指定标签名的所有元素（HTMLCollection 集合）

const elements = document.getElementsByTagName('标签名');

// 或在指定父元素下查找

const elements = 父元素.getElementsByTagName('标签名');

**特点 :** 同样返回动态集合，标签名不分大小写

4.通过CSS选择器获取
------------

#### （1）获取第一个匹配元素

// 返回匹配选择器的第一个元素（不存在则返回 null）

const element = document.querySelector('选择器');

// 或在指定父元素下查找

const element = 父元素.querySelector('选择器');

#### （2）获取所有元素

// 返回匹配选择器的所有元素（NodeList 集合）

const elements = document.querySelectorAll('选择器');

// 或在指定父元素下查找

const elements = 父元素.querySelectorAll('选择器');

**特点**：返回静态集合　（DOM变化不会实时反映　支持复杂的选择器）

#### （3）特殊元素获取

// 获取 <html> 元素

document.documentElement

// 获取 <body> 元素

document.body

// 获取所有表单元素

document.forms   HTMLCollection 集合

// 获取所有图片元素

document.images    HTMLCollection 集合

总结
--

| 方法 | 返回类型 | 动态性 | 适用场景 |
| ---- | -------- | ------ | -------- |
| getElementById | 单个元素 | - | 通过唯一 ID 获取 |
| getElementsByClassName / getElementsByTagName | HTMLCollection | 动态（实时更新） | 按类名 / 标签名批量获取，需实时反映 DOM 变化 |
| querySelector | 单个元素 | - | 复杂选择器匹配第一个元素 |
| querySelectorAll | NodeList | 静态（不实时更新） | 复杂选择器批量获取，性能稳定 |

querySelector 和 querySelectorAll 因支持 CSS 选择器语法，使用最灵活；而 ID 选择器（getElementById）效率最高。