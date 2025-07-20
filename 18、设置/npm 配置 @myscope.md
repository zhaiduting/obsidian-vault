假设 .npmrc 文件存在以下内容

```
@myscope:registry=https://mycustomregistry.example.org
registry=https://registry.npmmirror.com
```

### 🧠 含义解释

这两行分别配置了**作用域 registry**和**默认 registry**，优先级如下：

1. `@myscope:registry=...`：

    - 表示：所有以 `@myscope/` 开头的包，例如 `@myscope/foo`、`@myscope/bar`，将从 `https://mycustomregistry.example.org` 拉取。

2. `registry=...`：

    - 表示：除了 `@myscope` 作用域之外的**其他所有包**，默认从 `https://registry.npmmirror.com`（即淘宝镜像）拉取。

---

### 📦 示例对比

假设你执行以下安装命令：

#### 👉 1. 安装 `@myscope/foo`

```
npm install @myscope/foo
```

**结果：**

- npm 会从 `https://mycustomregistry.example.org/@myscope/foo` 下载这个包
- 因为它以 `@myscope/` 开头，使用的是**第 1 条配置**

---

#### 👉 2. 安装 `lodash`

```
npm install lodash
```

**结果：**

- npm 会从 `https://registry.npmmirror.com/lodash` 下载这个包
- 因为它没有作用域前缀，使用的是**默认 registry**

---

### 🧩 总结成表

| 包名           | 来源 registry                          | 原因           |
| -------------- | -------------------------------------- | -------------- |
| `@myscope/foo` | `https://mycustomregistry.example.org` | 匹配作用域配置 |
| `@myscope/bar` | `https://mycustomregistry.example.org` | 同上           |
| `vue`          | `https://registry.npmmirror.com`       | 使用默认配置   |
| `lodash`       | `https://registry.npmmirror.com`       | 使用默认配置   |

---

### ✅ 实用场景

这种配置组合非常适合以下情况：

| 场景                                   | 说明                               |
| -------------------------------------- | ---------------------------------- |
| 企业/组织有私有包（如 `@company/foo`） | 用 `@company:registry` 配置私有源  |
| 其余公共包用淘宝镜像加速               | 用 `registry=` 设置 npmmirror 镜像 |

---

可以在终端敲入以下命令验证配置是否生效：

```shell
npm config get registry
npm config get @myscope:registry
```

也可以加 `--verbose` 观察安装过程中的 registry 请求地址。
