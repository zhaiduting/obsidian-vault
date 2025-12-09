在 mac 里面怎么使用 gcc 镜像编译以下 hello.c 程序并导出到访达里？

```c
#include <stdio.h>
int main() {
    printf("Hello, World!\n");
    return 0;
}
```

可以将当前目录挂载到 gcc 镜像里进行编译，编译结束后当前目录直接生成 hello 程序。命令如下

```sh
docker run --rm \
  -v "$PWD":/src \
  -w /src \
  gcc:latest \
  gcc hello.c -o hello
```

命令中的 v 指的是 volume（卷） 的缩写，w 是 workdir 的缩写

| 参数                   | 作用                             |
| ---------------------- | -------------------------------- |
| `-v "$PWD":/src`       | 把当前 Mac 目录挂载到容器 `/src` |
| `-w /src`              | 设定容器工作目录                 |
| `gcc:latest`           | 使用 gcc 镜像                    |
| `gcc hello.c -o hello` | 在容器里执行编译                 |

### 别名命令

将以上命令进一步抽象，得到以下 `docker-gcc` 别名命令

```sh
# 使用 Docker gcc 镜像编译 C 程序
# 用法示例: docker-gcc -static hello.c -o hello
alias docker-gcc='docker run --rm -v "$PWD":/src -w /src gcc:latest gcc'
```
