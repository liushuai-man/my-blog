---
title: 浅拷贝/深拷贝--理解、实现
published: 2025-11-22
description: "在 JavaScript 中，浅拷贝和深拷贝是两种不同的操作，用于复制对象的属性值。"
tags: ["JavaScript"]
category: 前端
draft: false
---

在了解浅拷贝和深拷贝之前，先区分**赋值**与**拷贝**的区别

**基本数据类型**（string、number、boolean、null、undefined、Symbol）：值直接存储在**栈内存**，值本身不会受引用的影响。

**引用类型**（对象（{}）、数组（\[\]）、函数、Map/Set）：栈内存存**引用地址**，实际数据存在**堆内存**。

赋值与拷贝
-----

在 JavaScript 中，当你想把一个变量的值 “传给” 另一个变量时，**本质是在选择 “让两个变量共享数据” 还是 “让两个变量持有独立数据”** —— 这正是赋值和拷贝的核心分歧

当原变量是**基本数据类型**时：

*     赋值 ≈拷贝，因为基本类型不可变，修改新变量只会创建新值，不会影响原变量

当原变量是**引用数据类型**时：

*   赋值 = 共享数据（两个变量指向同一份内存，改一个必影响另一个）；
*   拷贝 = 生成独立数据（新变量有自己的内存，改一个不影响另一个），且拷贝分「浅拷贝」（仅外层独立）和「深拷贝」（全层级独立）

而当我们想让两个**引用数据类型**变量有独立的数据，并且修改互不受影响，我们就需要用到**拷贝（copy）**

**浅拷贝与深拷贝**
-----------

依据拷贝深度的不同，拷贝又分为**浅拷贝**和**深拷贝**

*   **浅拷贝**：只拷贝「表层数据」（外层引用类型容器），嵌套的引用类型（如对象里的对象、数组里的数组）仍共享原数据（指向同一份堆内存）；
*   **深拷贝**：递归拷贝「所有层级数据」（外层 + 所有嵌套的引用类型），生成完全独立的新数据（所有层级都有自己的堆内存）。

可以用一个通俗的比喻理解：

*   浅拷贝 = 复制了一个文件夹，文件夹里的子文件夹（嵌套数据）还是原来的，修改子文件夹里的文件会影响原文件夹；
*   深拷贝 = 复制了一个文件夹，并且把文件夹里所有子文件夹、子文件都完整复制了一份，修改任何内容都和原文件夹无关。

浅拷贝并非 “完全不拷贝嵌套数据”，而是 **只拷贝嵌套数据的 “引用地址”**，不创建新的嵌套数据副本 —— 这个边界很重要，避免你误解 “浅拷贝到底拷贝了什么”

所以，我们可以看出，当我们需要拷贝的**引用数据类型**变量没有**嵌套结构**时，用**浅拷贝**即可，而当此变量有嵌套结构，我们又不想让他因为修改而受影响时，我们就需要**深拷贝。**

那么，我们应该如何实现浅拷贝和深拷贝呢？

浅拷贝和深拷贝的实现
----------

#### 浅拷贝的实现：

1.  **扩展运算符`（最简洁）`**
    

```javascript
//数组
const arr = [1, 2, 3, "a"];
const shallowCopy = [...arr]; // 新数组，独立内存

shallowCopy.push(4);
console.log(arr); // [1,2,3,"a"]（无影响）

//对象
const obj = { name: "张三", age: 25, [Symbol("id")]: 100 };
const shallowCopy = {...obj}; // 新对象，独立内存

shallowCopy.age = 26;
console.log(obj.age); // 25（无影响）
console.log(shallowCopy[Symbol("id")]); // 100（Symbol 属性被拷贝）
```

**2.`Object.assign(target, source)`**

```javascript
// 拷贝对象
const obj = { a: 1, b: 2 };
const shallowCopy = Object.assign({}, obj); // 目标对象为空，生成新对象
shallowCopy.a = 100;
console.log(obj.a); // 1（无影响）

// 拷贝数组（数组本质是特殊对象）
const arr = [1, 2, 3];
const arrCopy = Object.assign([], arr);
arrCopy[0] = 100;
console.log(arr[0]); // 1（无影响）
```

*   注意：若目标对象非空（如 `Object.assign({ c: 3 }, obj)`），会合并属性（不覆盖目标对象原有属性）。

3.**arr.slice(数组专用，无参数时拷贝全数组)**

```javascript
const arr = [1, 2, 3];
const shallowCopy = arr.slice(); // 等价于 arr.slice(0)
shallowCopy.push(4);
console.log(arr); // [1,2,3]（无影响）
```

4. **`Array.from(arr)（`数组 / 类数组专用`）`**

```javascript
// 拷贝数组
const arr = [1, 2, 3];
const shallowCopy = Array.from(arr);
shallowCopy.push(4);
console.log(arr); // [1,2,3]（无影响）

// 拷贝类数组
const likeArr = { 0: 1, 1: 2, length: 2 };
const arrCopy = Array.from(likeArr);
console.log(arrCopy); // [1,2]（新数组）
```

**5.特殊场景：`Object.create(obj)`（基于原型的浅拷贝）**

```javascript
const obj = { a: 1 };
const shallowCopy = Object.create(obj); // shallowCopy.__proto__ === obj

shallowCopy.a = 2; // 给新对象自身添加属性（不修改原对象）
console.log(obj.a); // 1（无影响）
console.log(shallowCopy.a); // 2（自身属性）
```

#### 深拷贝的实现

深拷贝核心：递归拷贝所有层级（外层 + 嵌套引用类型），生成完全独立的新数据，修改新数据不影响原数据。

**1\. 生产环境首选：Lodash `_.cloneDeep()`（最稳定）**

*   步骤：
    1.  安装 Lodash：`npm i lodash` 或 `yarn add lodash`；
    2.  导入并使用；

```javascript
import _ from 'lodash'; // ES Module
// 或 const _ = require('lodash'); // CommonJS

// 嵌套结构+循环引用（极端场景）
const obj = {
  name: "张三",
  user: { age: 25 },
  hobbies: ["篮球", "游戏"],
  date: new Date(),
  reg: /abc/
};
obj.self = obj; // 循环引用（自己引用自己）

const deepCopy = _.cloneDeep(obj); // 深拷贝，完全独立

// 修改新数据
deepCopy.user.age = 30;
deepCopy.hobbies.push("读书");
deepCopy.date.setFullYear(2025);

console.log(obj.user.age); // 25（原数据不变）
console.log(obj.hobbies); // ["篮球", "游戏"]（原数据不变）
console.log(obj.date.getFullYear()); // 原日期（不受影响）
console.log(deepCopy.self === deepCopy); // true（循环引用正确处理）
```

**2\. 简单场景备选：`JSON.parse(JSON.stringify())`（无依赖）**

局限（关键！）：

*   不支持：函数、Symbol、undefined、循环引用（会报错）；
*   特殊处理：Date 转成字符串、RegExp 转成空对象、NaN/Infinity 转成 null；

```javascript
// 支持的场景（普通嵌套对象）
const obj = {
  name: "张三",
  user: { age: 25 },
  hobbies: ["篮球", "游戏"]
};
const deepCopy = JSON.parse(JSON.stringify(obj));

deepCopy.user.age = 30;
console.log(obj.user.age); // 25（无影响）

// 不支持的场景（函数+循环引用）
const badObj = {
  fn: () => console.log("hello"), // 函数
  self: null // 准备循环引用
};
badObj.self = badObj;

// JSON.parse(JSON.stringify(badObj)); // 报错：Converting circular structure to JSON
```

**3\. 了解原理：自定义递归深拷贝函数（不推荐生产用）**