# 动机

现在的网站都在演变成为web apps:

- 页面上的JavaScript越来越多。
- 你可以在现代的浏览器上做更多的事情。
- 整个页面重新加载的情况更少了 → 同一页面上的代码量更大了。

这意味着：客户端的代码量变得越来越庞大，庞大的代码量意味着我们需要适当地组织代码，而模块系统则提供了把代码分割成不同模块的功能。

## 模块系统风格

关于怎么定义依赖和导出值有多种标准：

- `<script>` 标签方式（无模块系统）
- CommonJs
- AMD 及其变种
- ES6 模块
- 其它……

### `<script>` - 标签方式

下面是不使用模块系统的前提下来管理模块代码库的方式。

```html
<script src="module1.js"></script>
<script src="module2.js"></script>
<script src="libaryA.js"></script>
<script src="module3.js"></script>
```

模块把接口导出到全局对象上,如 `window` 对象，也就是说模块可以在全局对象上获取到依赖的接口。

普遍的问题

- 全局对象上命名冲突
- 加载的顺序很重要
- 开发者需手动处理模块/库的依赖问题
- 大项目中列表越来越长而难以维护

### CommonJs：同步 require

这种方式使用同步的 `require` 来加载依赖并返回导出的接口。一个模块可以通过往`exports`对象上添加属性或者设置`module.exports`的值来确定导出哪些接口。

```js
require("module");
require("../file.js");
exports.doStuff = function() {};
module.exports = someValue;
```

服务器端的[node.js][node.js]用的就是这个方法。

优点:

- 服务器端的模块可以被复用
- 已经有很多模块供使用了（npm）
- 简单且易于使用

缺点:

- 阻塞式的调用不能适用于网络请求，因为网络请求是异步的
- 不能并行请求多个模块

实现:

- [node.js][node.js] - 服务端 
- [browserify][browserify]
- [modules-webmake][modules-webmake]，打包为一个bundle
- [wreq][wreq] - 客户端

### AMD：异步 require

[异步模块定义](https://github.com/amdjs/amdjs-api/wiki/AMD)

因为 CommonJs 的同步 `require` 方案不适合如浏览器等环境，所以有了异步的定义和导出模块方案

```js
require(["module", "../file"], function(module, file) { /* ... */ });
define("mymodule", ["dep1", "dep2"], function(d1, d2) {
  return someExportedValue;
});
```

优点

- 适合网络请求异步需求
- 并行加载多模块

缺点

- 代码复杂，更难写也更难读
- 似乎是一种绕路的笨办法

实现

- require.js - 客户端
- curl - 客户端

可阅读更多关于[CommonJS][CommonJs]和[AMD][AMD]的内容

### ES6 模块

EcmaScript6给JavaScript加了一些语言特性，其中就包括了模块系统。

```javascript
import "jquery";
export function doStuff() {}
module "localModule" {}
```

优点

- 静态分析变得更简单了
- 作为ES标准，这个实现未来肯定会成为主流

缺点

- 浏览器原生支持还需要一段时间
- 目前只有很少的一些模块可用

### 最合适的解决方案

让开发人员选择合适的模块系统，让代码能跑起来，也能使得添加自定义模块更简单易行。

## 传输

模块需要在客户端执行，因此它们需要从服务器传输到浏览器上。

有两个极端的方法来传输模块:

- 每传输一个模块发起一个请求
- 所有的模块都放在一个请求里
    
这两种方法都有人在用，但是都不是最优解：

- 每传输一个模块发起一个请求
    - 优点：只会请求目前需要的模块
    - 缺点：很多请求意味着会有很多额外的消耗
    - 缺点：请求延迟会使得程序启动变慢
    
- 所有的模块放在一个请求里
    - 优点：更少的请求意味着更低的延迟
    - 缺点：无论目前需要与否，所有的模块都一次性传输过来了
        
### 分块传输

我们需要一个更灵活的传输方式，在大多数情况下，如果能在极端中间找到一个平衡会是最好的选择。

→ 编译所有的模块的时候可以把模块分割成很多小模块

这样，将得到多个更小的请求。模块的每个块将是按需加载，初始请求中不包含完整的代码库。

"拆分点"是可选的，并且可以由开发者自定义。

→ 可以组成一个大的代码库

注：此[想法来自Google's GWT][Google's GWT]。

可阅读更多关于[代码拆分][Code Spliting]内容。

## 为什么只处理JavaScript？

为什么模块系统只能处理JavaScript？还有很多其他的静态资源需要处理：

- 样式
- 图片
- web 字体
- html 模板
- 等等

还有：

- coffeescript -> javascript
- less -> css
- jade -> 生成模板的 javascript
- i18n 文件 -> 别的资源
- 等等

这些都应该很容易处理才对：

```js
require("./style.css");
```

```js
require("./style.less");
require("./template.jade");
require("./image.png");
```

更多可参考[使用loaders][Using Loaders]和[loaders][Loaders]内容。

## 静态分析

在编译所有的模块的时候，静态分析会试图去找到依赖。

通常来说在没有表达式的情况下只能找到一些简单的东西，但是像 `require("./template/" + templateName + ".jade")` 这样的表示方法是很常见的形式。 不同的库会用不同的形式表示，有一些看起来很奇怪...

### 策略

一个智能的解析器会使得大多数代码能跑起来。即便程序员写了些很奇怪的代码，它还是能找到最兼容的办法。

[node.js]: http://nodejs.org
[browserify]: https://github.com/substack/node-browserify
[modules-webmake]: https://github.com/medikoo/modules-webmake
[wreq]: https://github.com/substack/wreq
[AMD]: https://github.com/amdjs/amdjs-api/wiki/AMD
[require.js]: http://requirejs.org/
[curl]: https://github.com/cujojs/curl
[GWT]: https://developers.google.com/web-toolkit/doc/latest/DevGuideCodeSplitting
[CommonJs]: ../API/Commonjs.md
[Code Spliting]: ../GUIDES/Code Splitting.md
[Loaders]: ../API/Loaders.md
[Using Loaders]: Using Loaders.md
[Google's GWT]: https://developers.google.com/web-toolkit/doc/latest/DevGuideCodeSplitting