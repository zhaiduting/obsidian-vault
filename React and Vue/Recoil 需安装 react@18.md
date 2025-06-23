项目运行时浏览器控制台报错 'import_react.default.SECRET_INTERNALS_DO_NOT_USE_OR_YOU_WILL_BE_FIRED' as it is undefined.

这是因为 **recoil v0.7 与 react v19 版本不兼容导致的**，执行以下命令安装 react v18 后重新运行项目即可解决这个问题

```shell
npm i react@18 react-dom@18
npm run dev
```

⚠️ Recoil 项目已停止更新，可改用 [Jotai](https://jotai.org/) 替代
