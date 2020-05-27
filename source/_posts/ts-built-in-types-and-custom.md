---
title: TS内置类型与自定义
date: 2020-01-27 22:11:28
tags: TypeScript
---

记录一些TypeScript的内置类型，及其自定义使用时的自由组合。

<!-- more -->

## Partial

将某个类型里的属性全部变为可选

>从源码可以看到 keyof T 拿到 T 所有属性名, 然后 in 进行遍历, 将值赋给 P, 最后 T[P] 取得相应属性的值。结合中间的 ?，将所有属性变为可选。

```ts
// lib
type Partial<T> = {
    [P in keyof T]?: T[P];
};

// example
interface T1 {
    key: string;
    value: string;
}

type T2 = Partial<T1> // { key?, value? }
```

一个简单的具体应用例子，一个状态的值是一个对象 `{ key: '', val: '' }`，每次更新这个状态时，可能只更新其中一个字段或者都更新，因此，使用 `Partial` 进行操作。

```ts
interface ItemType {
  key: string;
  val: string;
}

interface TriggerConfigsType {
  [id: number]: Partial<ItemType>;
}
```

## Required

将某个类型里的属性全部变为必选(与 `Partial` 相反)

>其中 -? 是代表移除 ? 这个 modifier 的标识。对应的还有个 +?， 与 -? 相反，用来把属性变成可选项的，+ 可省略。

```ts
// lib
type Required<T> = {
    [P in keyof T]-?: T[P];
};

// example
interface T1 {
    key?: string;
    value?: string;
}

type T2 = Required<T1> // { key, value }
```

## Readonly

将某个类型里的属性全部变为只读

```ts
// lib
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// example
interface T1 {
    key: string;
    value: string;
}

type T2 = Readonly<T1> // { readonly key, readonly value }
```

## Pick

这个类型则可以将某个类型中的子属性挑出来，变成包含这个类型部分属性的子类型。

```ts
// lib
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};

// example
interface T1 {
    key: string;
    value: string;
}

type T2 = Pick<T1, 'key'> // { key }
```

## Exclude

Exclude 将类型A中属于类型B的移除掉。

```ts
// lib
type Exclude<T, U> = T extends U ? never : T;

// example
type T1 = Exclude<'a' | 'b' | 'c' | 'd', 'a' | 'c' | 'f'>;  // 'b' | 'd'
```

## Extract

从 T 中提取出 U

```ts
// lib
type Extract<T, U> = T extends U ? T : never;

// example
type T1 = Extract<'a' | 'b' | 'c' | 'd', 'a' | 'c' | 'f'>;  // -> 'a' | 'c'
```

## ReturnType

获取函数的返回类型。

```ts
// lib
type ReturnType<T extends (...args: any[]) => any> = T extends (...args: any[]) => infer R ? R : any;

// example
function toString(num: number): string {
  return num.toString();
}

type T1 = ReturnType<typeof toString>; // string
```

## NonNullable

过滤类型中的 null 与 undefined

```ts
// lib
type NonNullable<T> = T extends null | undefined ? never : T;

// example
type T1 = string | number | null;
type T2 = NonNullable<T1>; // string | number;
```

## 自定义

### Omit

更高级的Pick，除了类型U，其他都pick出来，组成新的类型

```ts
// lib
type Omit<T, U> = Pick<T, Exclude<keyof T, U>>;

// example
interface Project {
    name: string;
    id: number;
    users: string[];
}

type SimpleProject = Omit<Project, 'users'> // { name, id }
```
### PowerPartial

深层级的Partial，可支持多层对象的属性
>https://github.com/eggjs/egg/blob/master/index.d.ts

```ts
// lib
type PowerPartial<T> = {
  [U in keyof T]?: T[U] extends object ? PowerPartial<T[U]> : T[U];
};
```

### PromiseReturnType

获取Promise的返回值类型

```ts
// lib
type PromiseReturnType<T extends Promise<any>> = T extends Promise<infer R>  ? R  : any;

// example
type MyPromise = Promise<string>

type T = PromiseReturnType<MyPromise>
```

### Overwrite

```ts
// lib
type Overwrite<T, U> = {
  [K in keyof T]: K extends keyof U ? U[K] : T[K]
}

// example
type Props = { name: string; age: number; visible: boolean };
type NewProps = { age: string; other: string };

type ReplacedProps = Overwrite<Props, NewProps>; // { name: string; age: string; visible: boolean; }
```

### 开源库

- https://github.com/sindresorhus/type-fest
- https://github.com/piotrwitek/utility-types

### 参考

- https://juejin.im/post/5c2f87ce5188252593122c98#heading-21
- https://wanghx.cn/blog/github/issue13.html
- https://github.com/whxaxes/blog/issues/14
