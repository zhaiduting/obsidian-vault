> Vite（法语意为 "快速的"，发音  `/vit/`，发音同 "veet"）是一种新型前端构建工具，能够显著提升前端开发体验。

以上引文来自 https://cn.vite.dev/guide/

```shell
npm create vite@latest
```

![vite](https://lib.zhaiduting.work.gd/uPic/vite.png)
敲入命令后 vite 会询问需要创建的项目名称、包名以及使用的是什么框架（这一步可以选择 Ract 或者 Vue 等框架），之后可以选择 TS 或 JS

也可以采用如下命令直接创建 React 或 Vue 项目

```shell
# 以下创建 react 项目
npm create vite@latest my-react-app -- --template react

# 以下创建 vue 项目
npm create vite@latest my-vue-app -- --template vue
```

之后使用 IDE 打开项目，终端敲入以下命令安装依赖后进入开发模式

```shell
npm i
npm run dev
```
