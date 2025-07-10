假设 $HOME/.zshrc 文件中添加了以下别名

```shell
alias adb_wifi='adb -s $(adb devices | grep _adb-tls-connect | cut -f1)'
```

那就可以直接通过 adb_wifi 别名执行一些相关的命令，例如

```shell
# 进入手机终端
adb_wifi shell
```

如果你始终只连接一台设备，完全不需要 adb_wifi 这个别名，直接使用 `adb` 更简单。例如

```shell
adb shell
```

如果你是重度使用者，**尤其是无线调试**用户，`adb_wifi`（或等价封装）就可以节省你每天大量重复劳动。
