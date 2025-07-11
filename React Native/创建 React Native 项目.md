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

### 运行项目

手机打开无线调试开关，终端敲入以下命令以验证手机是否连接成功。

```shell
~ > adb devices
List of devices attached
adb-1558369949000TE-dPaZrc._adb-tls-connect._tcp device
```

然后运行项目

```shell
npm run android
```

编译成功后将会在手机弹出一个申请安装 app 的弹窗，点击同意，安装好的 app 界面类似下图

![Adbshot_20250711_091403](https://lib.zhaiduting.work.gd/uPic/Adbshot_20250711_091403.png)
