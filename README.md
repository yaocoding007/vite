# vite 下一代前端开发与构建工具

## 目录

1. vite是什么、解决了什么问题
2. vite原理
3. vite插件开发
4. vite使用

## 3w TO vite

### vite是什么

Vite是一种新型的前端构建工具，是尤雨溪在开发Vue3.0的时候诞生的。类似于`webpack+webpack-dev-server`。利用浏览器`ESM`特性导入组织代码，在服务端按需编译返回，完全跳过了打包这个概念；而生产环境则是利用`Rollup`作为打包工具，号称是下一代的前端构建工具。

* `极速的服务启动`使用原生的ESM文件，无需打包
* `轻量快速的热重载`无论应用程序大小如何，都始终极快的模块热重载（HMR）
* `丰富的功能`对 TypeScript、JSX、CSS 等支持开箱即用。
* `优化的构建`可选 “多页应用” 或 “库” 模式的预配置 Rollup 构建
* `通用的插件`在开发和构建之间共享 Rollup-superset 插件接口。
* `完全类型化的API`灵活的 API 和完整的 TypeScript 类型。

### 我们为什么需要vite

传统的打包工具如`Webpack`是先解析依赖、打包构建再启动开发服务器，`Dev Server` 必须等待所有模块构建完成，当我们修改了 `bundle`模块中的一个子模块， 整个 `bundle` 文件都会重新打包然后输出。项目应用越大，启动时间越长。

![esbuild](/Users/liluyao/Desktop/vite/images/bundle.png)

### vite是怎么做的

Vite 通过在一开始将应用中的模块区分为 **依赖** 和 **源码** 两类，改进了开发服务器启动时间。

- **依赖** 大多为在开发时不会变动的纯 JavaScript。一些较大的依赖（例如有上百个模块的组件库）处理的代价也很高。依赖也通常会存在多种模块化格式（例如 ESM 或者 CommonJS）。

  Vite 将会使用 [esbuild](https://esbuild.github.io/) [预构建依赖](https://cn.vitejs.dev/guide/dep-pre-bundling.html)（处理CommonJs和UMD兼容性、合并http请求性能优化）。esbuild 使用 Go 编写，并且比以 JavaScript 编写的打包器预构建依赖快 10-100 倍。

- **源码** 通常包含一些并非直接是 JavaScript 的文件，需要转换（例如 JSX，CSS 或者 Vue/Svelte 组件），时常会被编辑。同时，并不是所有的源码都需要同时被加载（例如基于路由拆分的代码模块）。

Vite 以 [原生 ESM](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 方式提供源码。这实际上是让浏览器接管了打包程序的部分工作：Vite 只需要在浏览器请求源码时进行转换并按需提供源码。根据情景动态导入代码，即只在当前屏幕上实际使用时才会被处理。



![esbuild](/Users/liluyao/Desktop/vite/images/esm.png)



## 前置知识

### ESM







### esbuild

这是`Esbuild`首页的图。新一代的打包工具，提供了与`Webpack`、`Rollup`、`Parcel` 等工具相似的资源打包能力，但在时速上达到10～100倍的差距，耗时是`Webpack`2%~3%

![esbuild](/Users/liluyao/Desktop/vite/images/esbuild.png)



#### 为啥这么快

* 大多数前端打包工具都是基于 `JavaScript` 实现的，大家都知道`JavaScript`是解释型语言，边运行边解释。而 `Esbuild` 则选择使用 `Go` 语言编写，该语言可以编译为原生代码,在编译的时候都将语言转为机器语言，在启动的时候直接执行即可，在 `CPU` 密集场景下，`Go` 更具性能优势。
* `JavaScript` 本质上是一门单线程语言，直到引入 `WebWorker` 之后才有可能在浏览器、`Node` 中实现多线程操作。而`GO`天生的多线程优势，可以充分利用 `CPU` 资源

### roolup



## vite插件

- `config`：可以在`Vite`被解析之前修改`Vite`的相关配置。钩子接收原始用户配置`config`和一个描述配置环境的变量`env`

- `configResolved`：解析`Vite`配置后调用，配置确认

- `configureserver`：主要用来配置开发服务器，为`dev-server`添加自定义的中间件

- `transformindexhtml`：主要用来转换`index.html`，钩子接收当前的 `HTML` 字符串和转换上下文

- `handlehotupdate`：执行自定义`HMR`更新，可以通过`ws`往客户端发送自定义的事件

### 常用的钩子

- 服务启动时调用一次
  - `options`: 获取、操纵`Rollup`选项
  - `buildstart`：开始创建

- 在每个传入模块请求时被调用
  - `resolveId`: 创建自定义确认函数，可以用来定位第三方依赖
  - `load`：可以自定义加载器，可用来返回自定义的内容
  - `transform`：在每个传入模块请求时被调用，主要是用来转换单个模块

- 服务关闭时调用一次
  - `buildend`：在服务器关闭时被调用
  - `closeBundle`



```typescript
/**
 * 
 * @param options?: {defaultEnv = 'dev', key = 'APP_ENV'}} 
 * @returns 
 */
export default function CommandSetEnv(options: Options) {
    const { defaultEnv = 'dev', key = 'APP_ENV' } = options;
    const commandArgs = getCommandArgv() || defaultEnv;

    return {
        name: 'vite-plugin-env-command',
        config: () => ({
            define: {
                'process.env': JSON.stringify({
                    [key]: commandArgs
                }),
            }
        })
    }
}
```









