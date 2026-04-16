---
title: mate标签
published: 2025-09-28
description: "Meta 标签是放在 HTML 文档  区域中的代码，它们不直接显示在页面上，但为浏览器、搜索引擎和其他网络服务提供了关于网页的元数据（metadata）。"
tags: ["HTML"]
category: 前端
draft: false
---

Meta 标签是放在 HTML 文档  区域中的代码，它们不直接显示在页面上，但为浏览器、搜索引擎和其他网络服务提供了关于网页的元数据（metadata）。

以下是常用且重要的 meta 标签及其作用，可以分为几大类：

#### **一、字符编码声明**

这是最重要的 meta 标签之一，必须放在最前面，用于确保页面文字正确显示，避免乱码。

html

<meta charset="UTF-8">

*   **作用**：定义文档的字符编码为 UTF-8。UTF-8 包含了绝大多数人类语言的字符，是现代网页的标准编码。

* * *

#### **二、视图窗口（Viewport）控制**

针对移动端优化必不可少的标签，由 Apple 率先引入，现已成为行业标准。

html

<meta name="viewport" content="width=device-width, initial-scale=1.0">

*   **作用**：控制视口的尺寸和缩放比例。
    *   width=device-width：指示视口的宽度等于设备的屏幕宽度。
    *   initial-scale=1.0：设置页面首次加载时的初始缩放比例为 1（即不缩放）。
    *   其他常用值：maximum-scale=1.0, user-scalable=no（禁止用户缩放，但出于可访问性考虑，不建议禁用）。

* * *

#### **三、搜索引擎优化（SEO）与页面信息**

这些标签帮助搜索引擎理解页面内容，并决定如何在搜索结果中显示你的网页。

1.  **页面描述（Description）**

html

<meta name="description" content="这是一个关于常用HTML meta标签及其作用的详细说明页面。">

*   **作用**：提供一段关于本网页内容的简洁描述。搜索引擎常会在搜索结果标题下方显示这段文字，是吸引用户点击的关键。

1.  **关键词（Keywords）** - **（重要性已大幅降低）**

html

<meta name="keywords" content="meta标签, HTML, SEO, 视图窗口, 字符编码">

*   **作用**：为搜索引擎提供一组与页面内容相关的关键词。由于过去被滥用，现在主流搜索引擎（如 Google）已基本忽略它，但一些其他搜索引擎可能仍会参考。

1.  **作者（Author）**

html

<meta name="author" content="你的名字">

*   **作用**：声明网页的作者。

1.  **版权（Copyright）**

html

<meta name="copyright" content="公司名称">

*   **作用**：声明网站的版权信息。

1.  **搜索引擎索引方式（Robots）**

html

<meta name="robots" content="index, follow">

*   **作用**：指导搜索引擎爬虫如何对待该页面。
    *   index / noindex：是否允许搜索引擎索引本页。
    *   follow / nofollow：是否允许爬虫跟踪本页中的链接。
    *   noarchive：禁止搜索引擎显示页面的缓存版本。
*   **注意**：你可以在 robots.txt 文件中进行站点级的爬虫控制，而 meta robots 标签是针对页面级的更精确控制。