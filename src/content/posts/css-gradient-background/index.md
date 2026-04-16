---
title: CSS实现背景渐变效果
published: 2025-10-12
description: "在 CSS 中，渐变效果主要通过 linear-gradient()、radial-gradient()、conic-gradient() 这几个函数实现，它们可以作为 background-image 的值来使用。"
tags: ["CSS"]
category: 前端
draft: false
---

在 CSS 中，渐变效果主要通过 linear-gradient()、radial-gradient()、conic-gradient() 这几个函数实现，它们可以作为 background-image 的值来使用。以下是常见的渐变实现方式：

#### 1\. 线性渐变（Linear Gradient）

沿着直线方向产生渐变效果，语法：

/\* 基本语法：方向, 颜色1, 颜色2, ... \*/ background: linear-gradient(direction, color-stop1, color-stop2, ...);

示例：

/\* 从顶部到底部的渐变（默认方向） \*/ .gradient1 { background: linear-gradient(red, yellow); } /\* 从左到右的渐变 \*/ .gradient2 { background: linear-gradient(to right, blue, green); }

/\* 对角线渐变（右下方向） \*/ .gradient3 { background: linear-gradient(to bottom right, purple, pink); }

/\* 角度渐变（45度方向） \*/ .gradient4 { background: linear-gradient(45deg, #ff0000, #00ff00); }

/\* 带颜色位置的渐变 \*/ .gradient5 { background: linear-gradient(red 20%, yellow 50%, blue 80%); }

#### 2\. 径向渐变（Radial Gradient）

从一个中心点向外辐射产生渐变，语法：

/\* 基本语法：形状 大小 at 位置, 颜色1, 颜色2, ... \*/ background: radial-gradient(shape size at position, color-stop1, color-stop2, ...);

示例：

/\* 默认径向渐变（椭圆形，从中心开始） \*/ .gradient6 { background: radial-gradient(red, yellow, green); }

/\* 圆形渐变 \*/ .gradient7 { background: radial-gradient(circle, blue, pink); }

/\* 指定中心位置的渐变（距离左上角50px 50px处） \*/ .gradient8 { background: radial-gradient(at 50px 50px, purple, orange); }

/\* 限制渐变大小（最远角结束） \*/ .gradient9 { background: radial-gradient(circle farthest-corner, #333, #666); }

#### 3\. 锥形渐变（Conic Gradient）

围绕中心点旋转形成的渐变（类似色轮），语法：

/\* 基本语法：at 位置, 颜色1, 颜色2, ... \*/ background: conic-gradient(at position, color-stop1, color-stop2, ...);

示例：

/\* 基本锥形渐变 \*/ .gradient10 { background: conic-gradient(red, yellow, green, blue, purple); }

/\* 指定角度的锥形渐变 \*/ .gradient11 { background: conic-gradient(red 0deg, red 90deg, blue 90deg, blue 180deg); }

/\* 从中心开始的锥形渐变 \*/ .gradient12 { background: conic-gradient(at center, #ff0000, #0000ff); }

#### 4\. 重复渐变

使用 repeating-linear-gradient()、repeating-radial-gradient()、repeating-conic-gradient() 可以创建重复的渐变图案：

/\* 重复线性渐变 \*/ .repeat1 { background: repeating-linear-gradient(45deg, red, red 10px, yellow 10px, yellow 20px); }

/\* 重复径向渐变 \*/ .repeat2 { background: repeating-radial-gradient(circle, blue, blue 15px, pink 15px, pink 30px); }

渐变效果可以应用于任何接受 background-image 的元素，并且可以与其他背景属性（如 background-size、background-position）结合使用，实现丰富的视觉效果。