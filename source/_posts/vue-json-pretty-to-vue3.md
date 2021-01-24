---
title: VueJsonPretty 版本变更记录(Vue3)
date: 2021-01-24 21:32:00
tags: Vue
---

## 关于 Vue Json Pretty

[Vue Json Pretty](https://github.com/leezng/vue-json-pretty)第一个版本发布于 2017 年，基于`Vue`2 与官方提供的`VueCLI`工具搭建了基础项目结构，趁着`Vue3`正式发布的机缘，我也对项目进行了一次比较大的技术栈升级。

## 从`Vue2`到`Vue3`

关于`Vue2`与`Vue3`的区别与升级指南，社区上已有不少文章，本文就不再对此进行过多叙述。

- 从`OptionApi`切换到 `CompositionApi`。这个 API 是`Vue3`API 中一个比较精彩与新变化的部分，对于熟悉`React`技术栈的开发者来说，应该再熟悉不过了，没错，就是`hooks`。关于`hooks` 在 `React` 社区已经有比较多的实践了，Vue 还比较少，思路大同小异。下面截取了项目中使用到的几个简单小例子（第一代码块），并与旧版本（第二代码块）进行对比。相对而言，我还是比较喜欢新的 API 模式的，尤其是在旧 API 可能会存在`mixin`的时候。想象一下我们可以把一段段逻辑都聚合成一个个的`use`（例如`useRequest`），就像把面条装在了各个方便面盒子里，在需要的时候按需取用。

```ts
const state = reactive({
  visible: false,
});

const tree = ref<HTMLElement>();

const dataType = computed(() => getDataType(props.node.content));

watchEffect(() => {
  if (propsErrorMessage.value) {
    emitError(propsErrorMessage.value);
  }
});
```

```
data () {
  return {
    visible: this.currentDeep <= this.deep,
  }
},
dataType () {
  return this.getDataType(this.data)
},
watch: {
  propsError (message) {
    if (message) {
      throw new Error(`[vue-json-pretty] ${message}`)
    }
  }
},
```

## 从`.vue`到`.tsx`

`Vue3`使用了`Typescript`进行改造，自然对`Typescript`的支持是较为友好的，`ts` 是一种一旦使用就会放不下的东西，配合编辑器的提示，用起来非常顺手。通过使用 `@vue/babel-plugin-jsx` 即可轻松为项目配置 `.tsx` 的写法，以下截取了一小部分代码片段，这种更接近 `js` 的写法，在代码逻辑与组件拼装上是更为自由的，尤其是在出现很多 `if else` 的时候。同时，为了输出项目的 `types`，我建立了另一个不同的`tsconfig.json`，并在项目构建的时候调用`tsc`命令进行输出。

```tsx
import { defineComponent, computed, PropType } from 'vue';

export default defineComponent({
  props: {
    ...xxx,
    isMultiple: Boolean,
  },

  render() {
    const { uiType, model, $emit } = this;

    return (
      <label
        class={[`vjs-check-controller`, model ? 'is-checked' : '']}
        onClick={e => e.stopPropagation()}
      >
        <span class={`vjs-check-controller__inner is-${uiType}`} />
      </label>
    );
  },
});
```

## 虚拟滚动

相对于上一个版本使用递归组件的方式，我将代码的 `DOM` 结构进行了“拍平”。虚拟列表是近些年比较流行的一种实现方式，项目核心逻辑从对一个 `JSON` 不断进行递归渲染，变成通过 `js` 对 `JSON` 数据进行解析（jsonFlatten 方法），得到一个扁平化的数组。所以，本项目经过这次改造，也顺利提供了大数据的能力支持。

## 其他变更

- devDependencies 的升级（babel/webpack/eslint/...），借助 `npm-check`
- 代码格式化，引入 `prettier`
- 加入测试，引入 `cypress`
- CI 流程优化，废除原 `travis-ci`，使用 `github Actions`

## 最后

好了，总结就先记录这么多，更多思路可以查看项目工程[Vue Json Pretty](https://github.com/leezng/vue-json-pretty)。

欢迎大家使用交流与共建~
