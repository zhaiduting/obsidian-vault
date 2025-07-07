官方推荐的命令如下

```shell
npx create-expo-app@latest
```

可通过添加 [`--template` 选项](https://docs.expo.dev/more/create-expo#--template) 选择一个不同的模版。

测试发现，以下 3 条命令都可以创建 RN 项目

```shell
npx create-expo-app@latest
npm create expo@latest
npm create expo-app@latest
```

感觉 node 世界创建各种 app 项目的命令五花八门，没有统一的写法，这不方便记忆。
注意：`npm create foo@latest` == `npx create-foo@latest`
因此，与 `npx create-expo-app@latest` 等价的另一种写法是 `npm create expo-app@latest`
