---
title: "\U0001F4A3 为什么我不用 Typescript"
tags: [blog]
---

## 前言

我算是久仰 Typescript 的大名了，因而之前就想学习，但是一直没有抽出时间来看看它。直到最近有一天我在知乎上被邀请回答了 [一个问题](https://www.zhihu.com/question/456084081/answer/1851756971) —— 一个我以为的中学生问怎么样提升他的开源仓库。我点进去，先是被惊艳到了；然后发现，他用的是 Typescript。我顿时感觉我似乎落后了，于是鼓起劲，开始学起了 Typescript。

但是我学了下，再用了下，发现它没有像被吹的那么神。虽说是 Javascript 的超集，也的确有些地方挺好的，但是还是不足够改变我使用 Javascript 编程。就像虽然有 Deno，但是我还是用 Node.js 一样。

所以，我就写这篇文章，说下我个人感觉 Typescript 的缺点、为何它的优点无法打动我用它替代 Javascript，以及跟推荐我使用 Typescript 的大家讲一下我不用 Typescript 的逻辑。

各位想骂我心里骂骂就好了，我今天过个生日也不容易。

## 缺陷

### 1. 语法丑陋，代码臃肿

我写两段相同的代码，大家感受下：

```javascript
// js
const multiply = (i, j) => i * j
```

```typescript
// ts
function multiply(i: number, j: number) {
    return i + j
}
```

你看这类型注释，把好好的一段代码弄得这么乱……反正我看这样的 Typescript，花的反应时间一定比看上面的 Javascript 代码长。——不过也有可能是我比较熟悉 Javascript 吧。

复杂一点的东西也是一个道理（Apollo GraphQL 的代码）：

```javascript
// js
import React from 'react';
import ApolloClient from 'apollo-client';

let apolloContext;

export function getApolloContext() {
  if (!apolloContext) {
    apolloContext = React.createContext({});
  }
  return apolloContext;
}

export function resetApolloContext() {
  apolloContext = React.createContext({});
}
```

铁定要比这个好得多：

```typescript
// ts
import React from 'react';
import ApolloClient from 'apollo-client';

export interface ApolloContextValue {
  client?: ApolloClient<object>;
  renderPromises?: Record<any, any>;
}

let apolloContext: React.Context<ApolloContextValue>;

export function getApolloContext() {
  if (!apolloContext) {
    apolloContext = React.createContext<ApolloContextValue>({});
  }
  return apolloContext;
}

export function resetApolloContext() {
  apolloContext = React.createContext<ApolloContextValue>({});
}
```

甚至有人提了个 [issue](https://github.com/apollographql/react-apollo/issues/3016) 就是抱怨 Type 让它变得难用。

这么看，实在是为了这个假静态类型语言牺牲太多了，毕竟代码可读性还是很重要的。——之所以说它是假静态语言，是因为在真正的静态类型语言中，如 C 和 C++，不同的变量类型在内存中的存储方式不同，而在 Typescript 中不是这样。

比如，缺了这个可读性，debug 会变得更难。你是不是没有注意到我上面 `multiply` 的 Typescript 代码其实有 bug——应该是 `*` 而不是 `+`。

### 2. 麻烦

浏览器不能直接执行 Typescript，所以 Typescript 必须要被编译成 Javascript 才能执行，要花一段时间；项目越大，花的时间越长，所以 `Deno` 才要停用它。并且，使用 Typescript 要安装新的依赖，虽然的确不麻烦，但是不用 Typescript，就不用再多装一个依赖了是不是。

其实还有一点，但是放不上台面来讲，因为这是我自己的问题。

我一直不大喜欢给添花样的东西，比如 `pug`、`typescript`、`deno` 等；虽然 `scss` 啥的我觉得还是不错的——没有它我就写不出 [@knowscount/vue-lib](https://github.com/KnowsCount/vue-lib) 这个仓库。

### 3. 文件体积会变大

随随便便就能猜到，我写那么多额外的类型注释、代码变得那么臃肿肯定会让 Typescript 文件比用 Javascript 编写的文件更大。作为一个用 “tab 会让文件体积更小” 作为论据的 tab 党，我当然讨厌 Typescript 啦哈哈哈哈。

我理解在编译过后都是一样的，但是反正……我还是不爽。而且正是由于 TypeScript 会被编译到JavaScript 中，所以才会出现无论你的类型设计得多么仔细，还是有可能让不同的值类型潜入 JavaScript 变量中的问题。这是不可避免的，因为 JavaScript 仍然是没有类型的。

### 4. 报错使我老花

单纯吐槽一句，为什么它的报错那么丑，我就拼错了一个单词他给我又臭又长报一大段，还没有颜色。。

## 为何无法打动我

在讲为什么 Typescript 的优点无法打动我之前，我先来讲一讲 Typescript 有哪些优点吧：

1. 大厂的产品
2. 大厂在用
3. 可以用未来的特性
4. 降低出 bug 的可能性
4. 面对对象编程（OOP）

对于它是微软的产品，我不能多说啥，毕竟我用 Visual Studio Code 用得很香；但是大厂在用这个论点，就不一样了。

有个逻辑谬误叫做「reductio ad absurdum」，也就是「归谬法」。什么意思呢：

> 大厂用 Typescript，所以我要用 Typescript。<br />
> 大厂几百万改个 logo，我就借几百万改个 logo，因为大厂是大厂，肯定做得对。

这就很荒谬。

的确，大公司会采用 Typescript，必定有他的道理。但是，同样的论证思路也可以用于 `Flow`、`Angular`、`Vue`、`Ember`、`jQuery`、`Bootstrap` 等等等等，几乎所有流行的库都是如此，那么它们一定都适合你吗？

关于它可以让你提前接触到未来的特性……大哥，`babel` 不香吗？

最后就是 OOP 以及降低出 bug 的可能性（Typesafe）。OOP 是 Typescript 的核心部分，而现在 OOP 已经不吃香了……例如 Ilya Suzdalnitski 就说过它是「万亿美元的灾难」。

![v2-9cc25cb80ddc2df7ab06f0184e433790_1440w.jpg](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0511a27d56854783b1f313d0659afcfc~tplv-k3u1fbpfcp-watermark.image)

至于为什么这么说，无非就两点——面向对象代码难以重构，也难以进行单元测试。重构时的抓狂不提，单元测试的重要性，大家都清楚吧。

而在 Javascript 这种非 OOP 语言里头，函数可以独立于对象存在。不用为了包含这些函数而去发明一些奇怪的概念真是一种解脱。

总之，TypeScript 的所谓优点（更好的错误处理、类型推理）都不是最佳方案。你还是得写测试，还是得好好命名你的函数和变量。个人觉得单单像 Typescript 一样添加一个接口或类型不能解决任何这些问题。
