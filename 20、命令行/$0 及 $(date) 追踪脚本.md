假设脚本文件 myscript.sh 包含了以下命令

```shell
echo "$0 at $(date)" >> "$0.log"
```

如果终端执行了这个脚本，用户主目录下就会生成一个 myscript.sh.log 的文件，并追加类似下面这样的内容

```
myscript.sh at 2025年 3月11日 星期二 11时51分33秒 CST
```
