---
title: Array.prototype.sort
date: 2017-07-30 12:41:00
tags: JavaScript
---

## 自定义 compareFunction

`[elements...].sort((a, b) => a - b)`

* `compareFunction(a, b) < 0` 时, b 排在 a 后面
* `compareFunction(a, b) > 0` 时, b 排在 a 前面
* `compareFunction(a, b) = 0` 时, a 与 b 的位置不变

```js
[3, 1, 6, 20, 66].sort((pre, curr) => pre - curr)
// [1, 3, 6, 20, 66]
```

## 一些误用

`compareFunction` 的返回值没有区分为上述 3 种情况, 而是直接返回 `true || false`

### example1

```js
let numbers = [2, 6, 2, 9, 5, 2, 1, 8, 5] // length: 9
let arr = []
for (let i = 0; i < numbers.length; i++) {
  arr.push(numbers[i])
}
arr.sort((a, b) => a > b)
```

<!-- more -->

### example2

```js
let numbers = [2, 6, 2, 9, 5, 2, 1, 8, 5, 6, 1] // length: 11
let arr = []
for (let i = 0; i < numbers.length; i++) {
  arr.push(numbers[i])
}
arr.sort((a, b) => a > b)
```

当数组长度 `<= 10` 的时候, 在 Chrome, Firefox, IE... 上表现一致, 但是在数组长度 ` > 10` 的时候, Chrome 的排序出现了错误。下列是[V8](https://github.com/v8/v8/blob/master/src/js/array.js#L755)在处理数组长度 ` > 10` 的源码，由于 `false >=0` 和 `true >=0` 都是成立的, 所以下列的条件语句就永远进入了 `if`, 导致结果异常。

```js
var c02 = comparefn(v0, v2);
if (c02 >= 0) {
  // v2 <= v0 <= v1.
  var tmp = v0;
  v0 = v2;
  v2 = v1;
  v1 = tmp;
} else {
  // v0 <= v1 && v0 < v2
  var c12 = comparefn(v1, v2);
  if (c12 > 0) {
    // v0 <= v2 < v1
    var tmp = v1;
    v1 = v2;
    v2 = tmp;
  }
}
...
```

## 不稳定?

>备注: ECMAScript 标准并不保证这一行为，而且也不是所有浏览器都会遵守

即便我们对 `Array.prototype.sort` 的使用是正确的(即返回值中包含 `1, 0, -1` ), 不同浏览器也可能存在差异。

### example1

```js
let numbers = [2, 6, 2, 9, 5, 2, 1, 8, 5] // length: 9
let names = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i']
let arr = []
for (let i = 0; i < numbers.length; i++) {
  arr.push({
    number: numbers[i],
    name: names[i]
  })
}
arr.sort((a, b) => a.number - b.number)
```

### example2

```js
let numbers = [2, 6, 2, 9, 5, 2, 1, 8, 5, 6, 1] // length: 11
let names = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k']
let arr = []
for (let i = 0; i < numbers.length; i++) {
  arr.push({
    number: numbers[i],
    name: names[i]
  })
}
arr.sort((a, b) => a.number - b.number)
```

通过运行上述两个例子发现, example1 在不同浏览器上运行结果是一致的, 但 example2 在 Chrome 上出现了不同的情况。

|| Chrome || Firefox ||
| ---- | ---- | ---- | ---- | ---- |
| (index) | number | name | number | name |
| 0 | 1 | "k" | 1 | "g" |
| 1 | 1 | "g" | 1 | "k" |
| 2 | 2 | "c" | 2 | "a" |
| 3 | 2 | "a" | 2 | "c" |
| 4 | 2 | "f" | 2 | "f" |
| 5 | 5 | "i" | 5 | "e" |
| 6 | 5 | "e" | 5 | "i" |
| 7 | 6 | "b" | 6 | "b" |
| 8 | 6 | "j" | 6 | "j" |
| 9 | 7 | "h" | 7 | "h" |
| 10 | 8 | "d" | 8 | "d" |

因为[V8](https://github.com/v8/v8/blob/master/src/js/array.js#L755)在处理数组长度 ` <= 10` 的排序时使用插入排序, 而在 ` > 10` 的排序时使用了快速排序, 这是一种不稳定的排序算法。

>Firefox 一直使用归并排序

```js
function QuickSort(a, from, to) {
  var third_index = 0;
  while (true) {
    // Insertion sort is faster for short arrays.
    if (to - from <= 10) {
      InsertionSort(a, from, to);
      return;
    }
    if (to - from > 1000) {
      third_index = GetThirdIndex(a, from, to);
    } else {
      third_index = from + ((to - from) >> 1);
    }
    ...
  }
  ...
}
```

## 参考资料

* <https://github.com/v8/v8/blob/master/src/js/array.js#L755>
* <http://efe.baidu.com/blog/talk-about-sort-in-front-end/>
* <https://ruiming.me/archives/4>