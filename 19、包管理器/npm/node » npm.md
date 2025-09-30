### 调试工具

#### 推荐使用 ndb

此为 Google Chrome DevTools 团队成员专门为 Node.js 开发的一个集成调试环境。功能强大，但是启动速度慢。

```sh
# https://github.com/GoogleChromeLabs/ndb
npm install -g ndb
ndb node server.js
```

#### inspect-process 不支持 arm 架构

Node.js 官方推荐的调试工具是 Chrome DevTools，但是步骤繁琐，可用 inspect-process 解决这个麻烦（最终调用谷歌 DevTools 进行调试）。

```sh
# https://github.com/jaridmargolin/inspect-process
# ❗️ Only Mac 64 bits supported.

npm install -g inspect-process
inspect index.js
```

问题是在 arm 架构的机器上这玩意无法安装，于是放弃。改用以下**别名命令**一样可以轻松启动DevTools

```sh
# 编辑 $HOME/.zshrc 文件，加入以下别名
# ❗️ 如果 9229 端口被占用则报错

alias inspect='node --inspect-brk & sleep 1; open -a "Google Chrome" "chrome://inspect/#devices"'

# 然后在终端敲入以下命令进行调试
inspect index.js
```

如果端口被占用，别名命令会报错。最终改用以下 shell 脚本程序解决端口被占用的问题（脚本程序最终调用谷歌 DevTools 进行调试）

```sh
#!/bin/zsh
# 智能 Node inspect 启动器

# 使用方法：
# 在当前文件夹下新建 inspect 文件
# 将此代码块的内容复制进去
# 添加可执行权限 chmod u+x inspect
# 终端敲入命令 ./inspect foo.js
# 这会自动打开 chrome://inspect 页面，
# 然后点击 inspect 开始调试

# 默认端口
PORT=9229

# 检查端口是否占用，如果占用就往上加
while lsof -i :$PORT >/dev/null 2>&1; do
  PORT=$((PORT+1))
done

echo "✅ 使用调试端口: $PORT"

# 后台启动 Node 调试
node --inspect-brk=$PORT "$@" &

# 等待 Node 启动
sleep 1

# 打开 Chrome DevTools
open -a "Google Chrome" "chrome://inspect/#devices"

# 等待子进程结束
wait

```
