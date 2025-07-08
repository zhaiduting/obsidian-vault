最初我只是在 Android Studio 中使用手机 Run app 时才了解到有 adb 这么一个东西，后来才知道这东西除了可以调试 app 外，还有更多用法。

### 先准备一个别名

编辑 `$HOME/.zshrc` 添加以下别名

```shell
alias adb_wifi='adb -s $(adb devices | grep _adb-tls-connect | cut -f1)'
```

### 进入手机终端

```shell
adb_wifi shell
```

### 传输文件
