---
title: 深拷贝与浅拷贝
published: 2025-10-18
description: '在 JavaScript 中，浅拷贝（Shallow Copy）和深拷贝（Deep Copy）用于描述复制对象时的不同行为，核心区别在于是否复制对象的深层嵌套结构。'
tags: ['JavaScript', '深拷贝', '浅拷贝']
category: 前端
draft: false
---

在 JavaScript 中，浅拷贝（Shallow Copy）和深拷贝（Deep Copy）用于描述复制对象时的不同行为，核心区别在于是否复制对象的深层嵌套结构。

1\. 浅拷贝（Shallow Copy）

定义：只复制对象的表层属性，对于嵌套的对象 / 数组，只复制其引用（内存地址），不复制实际内容。特点：修改拷贝后对象的嵌套属性，会影响原对象。

常见浅拷贝方法：

```javascript
// 1. 对象浅拷贝：Object.assign()

const obj = { a: 1, b: { c: 2 } };
const shallowCopyObj = Object.assign({}, obj);

// 2. 对象浅拷贝：扩展运算符
const shallowCopyObj2 = { ...obj };

// 3. 数组浅拷贝：slice()
const arr = [1, [2, 3]];
const shallowCopyArr = arr.slice();

// 4. 数组浅拷贝：扩展运算符
const shallowCopyArr2 = [...arr];
```

浅拷贝的问题：

```javascript
const original = { a: 1, b: { c: 2 } };
const copy = { ...original };
copy.b.c = 99;
// 修改拷贝对象的嵌套属性
console.log(original.b.c); // 输出 99（原对象被影响）
```

2\. 深拷贝（Deep Copy）

定义：完全复制对象的所有层级结构，包括嵌套的对象 / 数组，拷贝后两者完全独立，修改互不影响。特点：拷贝后的对象与原对象彻底分离，适合处理复杂的嵌套数据。

常见深拷贝方法：

```javascript
// 1. JSON 序列化（简单场景适用）
const original = { a: 1, b: { c: 2 }, arr: [3, 4] };
const deepCopy = JSON.parse(JSON.stringify(original));

// 2. 递归实现深拷贝（通用方案）
function deepClone(obj) {
  // 处理非对象类型和 null
  if (obj === null || typeof obj !== 'object') {
    return obj;
  }

  // 处理数组
  if (Array.isArray(obj)) {
    return obj.map((item) => deepClone(item));
  }
  // 处理对象 const copy = {};
  for (const key in obj) {
    if (obj.hasOwnProperty(key)) {
      copy[key] = deepClone(obj[key]);
    }
  }
  return copy;
}
const original2 = { x: { y: { z: 10 } } };
const deepCopy2 = deepClone(original2);
```

深拷贝的优势：

```javascript
deepCopy2.x.y.z = 999;
console.log(original2.x.y.z); // 输出 10（原对象不受影响）
```

3\. 两者对比与适用场景

| 类型   | 复制层级     | 性能     | 适用场景                     |
| ------ | ------------ | -------- | ---------------------------- |
| 浅拷贝 | 仅表层属性   | 效率高   | 简单对象、无需修改嵌套属性时 |
| 深拷贝 | 所有层级属性 | 效率较低 | 复杂嵌套对象、需完全独立时   |

注意事项：

JSON 序列化的局限性：无法复制函数、正则、Symbol、循环引用等特殊值，适合纯 JSON 数据。深拷贝性能：递归深拷贝可能影响性能，复杂场景可使用 lodash 的 \_.cloneDeep() 等成熟库。基本类型无需拷贝：字符串、数字、布尔等基本类型赋值时会自动复制值，不存在引用问题。

选择浅拷贝还是深拷贝，取决于是否需要修改嵌套属性以及对性能的要求。
