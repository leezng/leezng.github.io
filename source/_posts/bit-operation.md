---
title: 位运算
date: 2018-02-25 20:54:12
tags: JavaScript
---

## 让indexOf()返回true or false

> 按位非（NOT） ~ a 反转操作数的比特位，即0变成1，1变成0。
> tips: 一个数值与它的按位取非值相加等于-1

```js
const str = 'abc'
// 一般方法:
str.indexOf('ab') !== -1
// 使用位运算:
~str.indexOf('ab') // 获得0与负数结果
// 进一步处理, 得到true|false
!!~~str.indexOf('ab')
```

<!-- more -->

### ES6 String.prototype.includes()

在ES6中提供了 `includes()` 方法用于判断一个字符串是否包含了另一个字符串:

```js
'something'.includes('thing') // true
```

## 随机获取区间[a, b)的整数

> 按位或（OR）  a | b 对于每一个比特位，当两个操作数相应的比特位至少有一个1时，结果为1，否则为0。

```js
// 一般方法：
Math.floor((Math.random() * 10))
// 使用位运算的方法
Math.random() * 10 | 0 // 其实就是做了一次取整，任意数值 | 0 都返回它本身
```

### 关于 `Math.random` 的题外话 [MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math/random)

* 得到一个两数之间的随机数 `[m, n)` : `Math.random() * (max - min) + min`
* 得到一个两数之间的随机整数 `[m, n)` : `Math.floor(Math.random() * (max - min)) + min`
* 得到一个两数之间的随机整数 `[m, n]` : `Math.floor(Math.random() * (max - min + 1)) + min`

## 乘除法

```js
2 * 2 // 4
2 << 1 // 4

4 / 2 // 2
4 >> 1 // 2
```

## Links

* <http://www.jstips.co/zh_cn/javascript/even-simpler-way-of-using-indexof-as-a-contains-clause/>
* <https://juejin.im/entry/57317b2679df540060d5d6c2>
* <http://jerryzou.com/posts/do-you-really-want-use-bit-operators-in-JavaScript/
http://www.ruanyifeng.com/blog/2010/01/12_javascript_syntax_structures_you_should_not_use.html>
* <https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Bitwise_Operators>