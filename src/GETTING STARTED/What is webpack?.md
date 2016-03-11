# webpack 是什么?

**webpack** 是一个**模块打包器**。

webpack 处理带有依赖关系的模块，生成一系列表示这些模块的静态资源。

![](http://webpack.github.io/assets/what-is-webpack.png)

## 为什么又来一个模块打包工具？

现有的模块打包工具不适合大型项目（大型的SPA）的开发。当然最重要的还是因为缺少[代码分割][Code Spliting]功能，以及静态资源需要通过模块化来无缝衔接。

尝试过扩展已有模块打包器，但无法达成下面所有的目标。

## 目标

- 把依赖树拆成可按需加载的块
- 让初始化加载时间尽可能地少
- 每个静态资源都是一个模块
- 能把第三方库继承到项目里来成为一个模块
- 能定制模块打包器的每个部分
- 适合大项目

## Webpack有哪些特别的地方？

### [代码拆分][Code Spliting]

在Webpack的依赖树里有两种类型的依赖：同步依赖和异步依赖。异步依赖会成为一个代码分割点，并且组成一个新的代码块。在代码块组成的树被优化之后，每个代码块都会在一个单独的文件里。

阅读更多关于[代码拆分][Code Spliting]的信息。

### [Loaders][Loaders]

Webpack原生是只能处理JavaScript的，而加载器的作用把其它的代码转换成JavaScript代码，这样一来所有种类的代码都能组成一个模块。

更多关于[使用loaders][Using loaders]和[loaders](Loaders)的内容

### 智能解析

webpack 有一个基本支持所有第三方库的智能解析器，甚至还支持带有表达式的依赖表述法，如 `require("./templates/" + name + ".jade")`。支持最常用的 [CommonJs][CommonJs] 和 [AMD][AMD] 这两种模块风格。

更多内容可参考[含有表达式的依赖表述][context]、[CommonJs][CommonJs] 和 [AMD][AMD]。

### [插件系统][plugins]

Webpack有丰富的插件系统，大多数内部的功能都是基于这个插件系统。这也使得我们可以定制Webpack，把它打造成能满足我们的需求的工具，并且把自己做的插件开源出去

更多参考[插件][plugins]。

[Code Spliting]: ../GUIDES/Code Splitting.md
[Loaders]: ../API/Loaders.md
[Using loaders]: Using%20Loaders.md
[CommonJs]: ../API/CommonJs.md
[AMD]: ../API/AMD.md
[context]: ../API/Context.md
[plugins]: ../API/Plugins.md
