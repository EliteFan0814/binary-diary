---
date: 2023-03-03
author: elitefan
source: https://leerob.substack.com/p/why-im-optimistic-about-javascripts
---

[以下代码翻译自此文章](https://leerob.substack.com/p/why-im-optimistic-about-javascripts)

# 为什么我对 JavaScript 的未来持乐观态度

> 一次学习，到处编写—— 这是 JavaScript 的目标

> 嗨，我是[Vercel](https://vercel.com/home)的开发体验副总裁：[Lee](https://leerob.io/)。我写一些我对网络开发持乐观态度的
> 事情。我曾经在 Revue
> （[现在它没了](https://techcrunch.com/2022/12/14/twitter-shuts-down-revue-its-newsletter-platform/)）运营我的博客，现
> 在我来到了 Substack。如果你在我发布时想及时获取通知，欢迎订阅。

我对 JavaScript 的未来持乐观态度。

开发者们想编写在任何地方都能运行的 JavaScript，例如浏览器、服务器、或者其他边缘计算环境。

尽管存在各种怪异行为和缺陷，但由于其存在先天性的增长优势（内置于浏览器中），庞大的工具和库生态系统以及 Typescript 的持续
增长和采用，JavaScript 的采用率持续上升。越来越多的开发者能够学习 API（比如请求和响应）并在任何地方重用相同的知识。

拥有一套商议好的通用 API（即标准）和支持相同接口的平台（例如跨浏览器支持）意味着 web 开发人员现在可以**一次学习，到处编
写**。

这篇文章将概述最近浏览器、服务器、边缘计算平台的进步。

## JavaScript：在浏览器中

今天的 Web 开发人员花在编写特定于浏览器供应商的 JavaScript 或特定于浏览器供应商的 CSS 选择器的时间比以往任何时候都少。

```javascript
function isIE11() {
  return !!window.MSInputMethodContext && !!document.documentMode
}
```

我们已经摆脱了维持一个元素正常[纵横比](https://developer.mozilla.org/en-US/docs/Web/CSS/aspect-ratio)都要使用 CSS 奇淫巧
计的世界。

```css
@supports not (aspect-ratio: 16/9) {
  .aspectRatio {
    overflow: hidden;
    padding-bottom: 56.25%;
    height: 0;
  }
}
```

以下两种合并的趋势使这成为可能：

- **IE 浏览器的死亡：** 现在
  [IE11](https://www.bleepingcomputer.com/news/microsoft/microsoft-edge-update-will-disable-internet-explorer-in-february/)
  已经退出历史舞台，Web 开发人员可以编写更少的特定 CSS 前缀，从而减少书写用于适配的样式表和 CSS hack。

- **浏览器引擎统一：** 如今三大浏览器引擎（Chromium/Chrome, Gecko/Firefox, and Webkit/Safari）对我们所见过的
  JavaScript、CSS 和 Web API 拥有最好的跨浏览器支持。必须给[Interop 项目](https://web.dev/interop-2022/)点个赞！

现在，JavaScript 在浏览器引擎中当然并不完美，也不可能永远如此。但这是最好的，我对此很乐观。如果不花费一周的时间深入挖掘
IE 错误，将累计节省数千（或数百万）个开发人员的时间。

这有一个例子说明了这种统一性是怎样使开发者受益的。想象一下你是一个尝试开发一个可重用图片组件的框架作者，来帮助数以千计的
开发者在使用图片时能有更好的表现。这在不久以前的 2020 年，你需要围绕网络 平台开展工作。

在所有浏览器支持的情况下，很难在不造成[布局偏移](https://web.dev/cls/)的情况下，实现加载一张图片，并正确的维持纵横比，不
因图像的大小/质量而降低初始页面的加载性能。这导致开发者要么忽略这个问题，要么框架编写了抽象组件来生成类似下面的代码：

```html
<span>
  <-- needed to maintain aspect ratio
  <span>
    <-- needed to maintain aspect ratio, CSS padding hacks
    <img src="" style="" /> <-- inline styles to prevent layout shift <noscript>...</noscript> <-- JS needed for
    IntersectionObserver
  </span>
</span>
```

2022 年情况不同了，跨浏览器支持：`aspect-ratio`，[width/height attributes](https://web.dev/optimize-cls/)用来防止布局偏
移，原生[image lazy-loading](https://developer.mozilla.org/en-US/docs/Web/Performance/Lazy_loading)，纯基于 CSS/SVG 的模
糊图像占位符。上面的代码可以删除包装元素并且不需要 JavaScript 运行时也能正常工作。

```html
<img
  alt="A kitten"
  decoding="async"
  height="200"
  loading="lazy"
  src="https://placekitten.com/200/200"
  style="aspect-ratio: auto 1 / 1"
  width="200"
/>
```

## JavaScript：在服务器上

同构 JavaScript，或者说可以在客户端和服务端都能运行的代码，对许多 web 开发者来说已经成为一种理想状态。学习一次，到处编写
，对吧？直到最近，Node.js 和 web 平台依旧不协调。

考虑通过 HTTP 获取数据。在浏览器上，我们已经有
了[Web Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)。Node.js18 之前，并没有内建的解决方案用于
获取数据，使用 node-fetch 或 undici 等包进行请求数据，它们具有相似但略微不同的 API，区分还很不明显。

这种在不同平台缺乏的一致性意味着像 Next.js 这样用于书写同构 JavaScript 的工具需要添加各种 polyfill 才能使开发者在客户端
和服务端使用相同的 fetch api。使用 Node.js 18 之后，这些工具现在就可以移除额外的为了抹去不同平台差异性的 JavaScript
polyfill 代码，这最终会让 JavaScript 的各种用于解决差异性的依赖变得更少。

我对服务器上的 JavaScript（和 TypeScript）持乐观态度。并不仅仅只是 fetch API 这一点。Request, Response 等 100 多个 API
现在都能在浏览器和 Node.js 中使用。浏览器供应商和构建服务器基础设施的公司现在比以往任何时候关系都更
加[紧密](https://wintercg.org/) ，以目的是为了提供一套可以在任何地方运行的标准 API，包括边缘计算平台。

## JavaScript：在边缘计算中

边缘计算是运行 JavaScript 经常被误解的最新目标，它在三者（浏览器、服务器、边缘计算）中的标准化程度最低。

将边缘计算视为最高级别的抽象，把你所有的时间都用于构建业务逻辑。

![转向边缘计算平台意味着要权衡更多的抽象层, 但可以专注于业务逻辑。](https://substackcdn.com/image/fetch/w_1456,c_limit,f_webp,q_auto:good,fl_progressive:steep/https%3A%2F%2Fbucketeer-e05bbc84-baa3-437e-9518-adb32be77984.s3.amazonaws.com%2Fpublic%2Fimages%2F5b450d64-66d2-4072-bdc0-4eb21d2cbdd5_3840x2160.png)

边缘计算并不是一个全新的东西，而是对现有 Node.js 世界的深思熟虑和有意权衡。

你想要编写 JavaScript，但是边缘计算的基础设施需要的是（相当巨大的）Node.js API 的一个轻量化子集。通过权衡在浏览器中运行
的 Node.js API 的子集，你可以获得一个始终如一的快速冷启动和更合算的计算工作负载。听起来美滋滋。

让我们来看个例子。在这个情况下，我将使用
[Vercel 的边缘函数](https://vercel.com/blog/edge-functions-generally-available)。它也可以是其他例如 Cloudflare 或 Deno
等边缘计算平台。对我来说，这段代码最好的部分实际上相当无聊，它看起来像 Node.js。

```javascript
export const config = {
  runtime: 'edge'
}

// Web standard Request API
export default function handler(req: Request) {
  // Web standard URL API
  const { searchParams } = new URL(req.url)
  const name = searchParams.get('name')

  // Web standard Fetch API
  const req = await fetch('https://...', { body: { name } })
  const data = await req.json()

  // Web standard Response (.json is new)
  // https://github.com/whatwg/fetch/issues/1389
  return Response.json(data);
}
```

关键在于：这不仅仅与基础设施有关。它是关于采用这些相同的 Web API 并帮助成千上万的新进开发人员一次学习，到处编写的框架。

这段代码在 Next.js. 或 SvelteKit. Remix. Fresh.中都能使用，或者在下一个新的基于同样标准 API 的框架中使用。

**成为一名 Web 开发人员真是太棒了。**
