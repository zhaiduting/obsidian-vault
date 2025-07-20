`.npmrc` 文件是 npm（Node Package Manager）用来**配置行为和设置选项**的配置文件，作用非常重要，常用于：

---

### 🧾 `.npmrc` 文件的作用

`.npmrc` 文件可以配置很多内容，常见用途包括：

| 用途             | 示例                                       | 说明                                         |
| ---------------- | ------------------------------------------ | -------------------------------------------- |
| 指定镜像源       | `registry=https://registry.npmmirror.com/` | 加快下载速度或使用私有镜像                   |
| 设置 token       | `//registry.npmjs.org/:_authToken=xxxx`    | 发布/安装私有包时使用                        |
| 缓存路径         | `cache=/custom/path/to/npm-cache`          | 修改默认缓存目录                             |
| 设置包作用域的源 | `@my-org:registry=https://npm.my-org.com`  | 某些组织的私有 npm 源                        |
| 设置默认前缀     | `prefix=/usr/local`                        | 改变全局包的安装位置                         |
| 自动保存依赖     | `save=true`                                | 执行 `npm install` 时自动写入 `package.json` |

---

### 🗂️ `.npmrc` 的四个层级（优先级从高到低）

| 层级         | 路径                         | 说明                            |
| ------------ | ---------------------------- | ------------------------------- |
| 项目级别     | `<项目目录>/.npmrc`          | 只影响当前项目                  |
| 用户级别     | `~/.npmrc`                   | 当前用户的默认配置（Mac/Linux） |
| 全局级别     | `$(npm prefix -g)/etc/npmrc` | 所有用户（较少用）              |
| npm 内置默认 | `npm config list -l` 可查看  | npm 自带的默认配置              |

---

### 📖 官方文档

npm 的官方文档页面如下：

- 🔗 **[npm-config 官方文档](https://docs.npmjs.com/cli/v10/configuring-npm/npmrc)**
