> Vite（法语意为 "快速的"，发音  `/vit/`，发音同 "veet"）是一种新型前端构建工具，能够显著提升前端开发体验。

以上引文出自 https://cn.vite.dev/guide/

### 创建一个 CSR 项目

CSR 是 Client-Side Rendering 的缩写，意思是“客户端渲染”。或者叫前端渲染（Frontend Rendering），首先敲入以下命令

```shell
npm create vite@latest
```

![vite](https://lib.zhaiduting.work.gd/uPic/vite.png)
之后 vite 会询问需要创建的项目名称、包名以及使用的是什么框架（这一步可以选择 Ract 或者 Vue 等框架），然后选择 TS 或 JS

之后在 IDE 中打开项目，使用 IDE 集成的终端窗口敲入以下命令安装依赖，进入开发模式

```shell
npm install
npm run dev
```

### 创建一个 SSR 项目

SSR 是 Server-Side Rendering 的缩写，意思是“服务端渲染”。或者叫后端渲染（Backend Rendering），仍然敲入这条命令

```shell
npm create vite@latest
```

之后 vite 会询问需要创建的项目名称、包名，当询问使用什么框架时，注意了，选择 `Others » Extra Vite Starters`。其他各选项如下图
![vite-react-ssr](https://lib.zhaiduting.work.gd/uPic/vite-react-ssr.png)
最后跟 CSR 项目一样，敲入以下命令进入开发

```shell
npm install
npm run dev
```

#### 选项问题 1

```
✔ Select a template: › ssr-react
? Select a variant:
    Streaming
❯   Non-streaming
```

| 模板              | 说明                                                                                                                                                                                    |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Streaming**     | 使用 React 的 `streaming SSR`，可以边渲染边发送 HTML 给客户端。加载更快，适合大页面和现代应用。依赖 `react-dom/server` 的 `renderToPipeableStream()`（或 `renderToReadableStream()`）。 |
| **Non-streaming** | 使用传统的 `renderToString()` 渲染整个 HTML 后一次性发送给客户端。逻辑更简单，但首屏速度稍慢，适合初学者或简单场景。                                                                    |

初学者体验一下 SSR 架构，建议从 `Non-streaming` 开始

#### 选项问题 2

```
? Select a variant: › - Use arrow-keys. Return to submit.
    JavaScript
    TypeScript
❯   JavaScript + SWC
    TypeScript + SWC
```

**SWC** 全称是 **Speedy Web Compiler**，是用 Rust 语言写的 JavaScript/TypeScript 编译器和转译器。它能把现代 JS/TS 代码转成浏览器兼容版本，类似于 Babel 的功能，但编译速度远远快于 Babel。也可以做代码压缩（minify）和其他代码转换工作。官方网站 https://swc.rs/

此处选择 JavaScript + SWC

#### 链接

SSR 项目的详情可参考文档 https://cn.vitejs.dev/guide/ssr.html
