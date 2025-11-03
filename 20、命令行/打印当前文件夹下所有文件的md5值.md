要使用一条命令打印**当前文件夹**下所有**非目录文件**的 MD5 校验和，最简洁和常用的写法是结合 `find` 和 `md5sum` 命令。

### 🥇 推荐命令（包括子文件夹）

这条命令会递归地查找当前目录下所有文件，并打印它们的 MD5 值和完整路径：

```sh
find . -type f -exec md5sum {} +
```

### 📋 命令解析

| **部分**              | **作用**                                                                           |
| ------------------- | -------------------------------------------------------------------------------- |
| `find .`            | 在当前目录（`.`）开始查找。                                                                  |
| `-type f`           | 仅查找**普通文件**（`f`ile），忽略目录、链接等。                                                    |
| `-exec md5sum {} +` | 对找到的每个文件执行 `md5sum` 命令。`{}` 会被替换为文件名，`+` 表示尽可能地将多个文件名作为参数传递给一次 `md5sum` 调用，效率更高。 |

---

### **📌 仅限当前层级（不包括子文件夹）**

如果您只希望获取**当前目录**（不进入任何子目录）下的文件 MD5 值，可以使用以下命令：

```sh
md5sum *
```

#### **注意：**

- `md5sum *` 会尝试对所有文件和**目录**计算 MD5。它会跳过目录并给出错误提示，但依然能打印出所有文件的 MD5。
- 如果当前目录包含的文件数量**过多**，`md5sum *` 可能会因为参数列表过长而失败。此时，上面的 `find` 命令是更健壮的选择。

---

### 终端实测

由于不存在子文件夹，所以 `md5sum *` 和 `find . -type f -exec md5sum {} +` 输出的结果相同。命令执行结果表明 `vmoptions` 文件夹下所有文件的内容完全相同，如 `cat goland.vmoptions` 所示

```sh
vmoptions > md5sum *
505d97467ea81e9b83a09a7738561ab7  aqua.vmoptions
505d97467ea81e9b83a09a7738561ab7  clion.vmoptions
505d97467ea81e9b83a09a7738561ab7  datagrip.vmoptions
505d97467ea81e9b83a09a7738561ab7  dataspell.vmoptions
505d97467ea81e9b83a09a7738561ab7  devecostudio.vmoptions
505d97467ea81e9b83a09a7738561ab7  gateway.vmoptions
505d97467ea81e9b83a09a7738561ab7  goland.vmoptions
505d97467ea81e9b83a09a7738561ab7  idea.vmoptions
505d97467ea81e9b83a09a7738561ab7  jetbrains_client.vmoptions
505d97467ea81e9b83a09a7738561ab7  jetbrainsclient.vmoptions
505d97467ea81e9b83a09a7738561ab7  phpstorm.vmoptions
505d97467ea81e9b83a09a7738561ab7  pycharm.vmoptions
505d97467ea81e9b83a09a7738561ab7  rider.vmoptions
505d97467ea81e9b83a09a7738561ab7  rubymine.vmoptions
505d97467ea81e9b83a09a7738561ab7  rustrover.vmoptions
505d97467ea81e9b83a09a7738561ab7  studio.vmoptions
505d97467ea81e9b83a09a7738561ab7  webide.vmoptions
505d97467ea81e9b83a09a7738561ab7  webstorm.vmoptions
vmoptions >
vmoptions > find . -type f -exec md5sum {} +
505d97467ea81e9b83a09a7738561ab7  ./webstorm.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./clion.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./rustrover.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./pycharm.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./aqua.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./rubymine.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./idea.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./studio.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./devecostudio.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./dataspell.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./webide.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./gateway.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./jetbrains_client.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./datagrip.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./goland.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./jetbrainsclient.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./rider.vmoptions
505d97467ea81e9b83a09a7738561ab7  ./phpstorm.vmoptions
vmoptions >
vmoptions >
vmoptions >
vmoptions > cat goland.vmoptions
-Xms128m
-Xmx1024m
-XX:ReservedCodeCacheSize=512m
-XX:+IgnoreUnrecognizedVMOptions
-XX:+UseG1GC
-XX:SoftRefLRUPolicyMSPerMB=50
-XX:CICompilerCount=2
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-ea
-Dsun.io.useCanonCaches=false
-Djdk.http.auth.tunneling.disabledSchemes=""
-Djdk.attach.allowAttachSelf=true
-Djdk.module.illegalAccess.silent=true
-Dkotlinx.coroutines.debug=off
-XX:ErrorFile=$USER_HOME/java_error_in_idea_%p.log
-XX:HeapDumpPath=$USER_HOME/java_error_in_idea.hprof

--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED
--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED

vmoptions >

```
