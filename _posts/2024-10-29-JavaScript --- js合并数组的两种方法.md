---
title: "JavaScript --- js合并数组的两种方法"
date: 2024-10-29
---

## 1、使用concat()方法：

```js
let array1 = [1, 2, 3];
let array2 = [4, 5, 6];
let mergedArray = array1.concat(array2);
```

## 2、使用扩展运算符...（ES6语法）：

```js
let array1 = [1, 2, 3];
let array2 = [4, 5, 6];
let mergedArray = [...array1, ...array2];
```