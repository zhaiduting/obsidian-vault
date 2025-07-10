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

### adb_wifi 含义

假设 `adb devices` 的输出如下：

```
List of devices attached
192.168.50.217:39607    device
adb-1558369949000TE-dPaZrc._adb-tls-connect._tcp    device
```

`grep _adb-tls-connect`: 这一步会从 `adb devices` 的输出中，过滤出只包含 `_adb-tls-connect` 字符串的行。这些通常是无线调试设备，结果将是：

```
adb-1558369949000TE-dPaZrc._adb-tls-connect._tcp    device
```

请注意，第一行 (`192.168.50.217:39607 device`) 不包含 `_adb-tls-connect`，所以它被过滤掉了。

`cut -f1`: 这个命令会接收 `grep` 的输出，并提取每行的第一个字段（f1 表示 field 1），以 Tab 键作为默认分隔符。 经过这一步后的结果将是：

```
adb-1558369949000TE-dPaZrc._adb-tls-connect._tcp
```

### 最终结果

因此，您别名中的 `$(...)` 部分将替换成 `adb-1558369949000TE-dPaZrc._adb-tls-connect._tcp`

当执行 `adb_wifi shell` 时，实际运行的命令是：
`adb -s adb-1558369949000TE-dPaZrc._adb-tls-connect._tcp shell`

### 使用场景

当你经常用两部手机做调试时，第一部使用 usb 连接，第二部用 wifi 连接。

```
adb reboot # 重启 usb 手机
adb_wifi reboot # 重启 wifi 手机

adb shell # 进入 usb 手机终端
adb_wifi shell #进入 wifi 手机的终端
```
