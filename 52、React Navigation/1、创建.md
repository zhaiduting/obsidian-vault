https://reactnavigation.org/
https://reactnavigation.org/docs/getting-started

### 基础模块的安装

根据项目是否依赖于 Expo，可以从以下两条命令中选择一条去执行。

```shell
# expo 项目使用以下命令
npx expo install react-native-screens react-native-safe-area-context

# 原生项目使用以下命令
npm install react-native-screens react-native-safe-area-context
```

问题是 Expo 项目的 App 组件是由 Expo Router 自动生成的，无法修改。于是放弃，==改用以下命令使用指定的模版创建一个新项目==

```shell
npx create-expo-app@latest --template react-navigation/template
```

### 堆栈式导航及元素

```shell
npm install @react-navigation/native-stack
npm install @react-navigation/elements
```
