与前文所述的 [version 命令](./version%20命令.md) 有所不同，version 脚本是在 `package.json` 文件中定义的。例如以下 version 字段的字符串内容就是一个 version 脚本

```json
// package.json 文件部分内容
{
  "scripts": {
    "dev": "node esbuild.config.mjs",
    "build": "tsc -noEmit -skipLibCheck && node esbuild.config.mjs production",
    "version": "node version-bump.mjs && git add manifest.json versions.json"
  }
  // 其它部分
}
```

与开发阶段手动敲入的 `npm run dev` 有所不同，通常不需要手动执行 `npm run version` 脚本，而是通过 `npm version` 命令间接执行 version 脚本的。

## Gemini 的回答

`npm version <new-version>` 命令执行的**完整和精确的顺序**如下：

### 1. 更新 `package.json`

`npm version` 命令会读取你提供的 `<new-version>`（例如 `patch`, `minor`, `1.2.3` 等），然后修改项目根目录下的 `package.json` 文件，更新 `"version"` 字段。

### 2. 触发 `version` 脚本

`npm version` 命令在更新完 `package.json` 后，但在执行任何 Git 操作**之前**，会自动查找并运行 `package.json` 中定义的 `version` 生命周期脚本。

在您的例子中，就是执行：

```sh
  node version-bump.mjs && git add manifest.json versions.json
```

这个步骤确保了像 `manifest.json` 和 `versions.json` 这样的**额外版本相关文件**能够及时更新（这由 [`version-bump.mjs`](#附) 代码决定），并被 `git add` 暂存，以便在下一步中与 `package.json` 一起被提交。

### 3. 执行 Git 提交（Commit）

在 `version` 脚本成功执行完毕后，`npm version` 命令会执行 Git 提交操作。

提交信息（Commit Message）的格式通常是 `v<新版本号>` (例如 `v1.0.1`)。它会提交所有被修改和/或被脚本 `git add` 暂存的文件（包括 `package.json`、`manifest.json` 和 `versions.json`）。

### 4. 创建 Git 标签（Tag）

最后，`npm version` 命令会在这个新的版本提交上创建一个**Git 标签**，标签名与提交信息相同（例如 `v1.0.1`）。

## 为什么会触发 `npm run version` 脚本？

`npm version <new-version>` 命令触发 `version` 脚本，是因为它是 **npm 生命周期（Lifecycle）的一部分**，而不是你需要手动 `npm run version` 调用的。

**npm 生命周期脚本**是 npm 约定俗成的钩子（hooks）。当执行某些主要命令（如 `install`、`publish` 或 `version`）时，npm 会在特定的时间点自动执行这些脚本：

| **生命周期脚本** | **触发命令**      | **触发时机**                               | **作用**                                   |
| ---------------- | ----------------- | ------------------------------------------ | ------------------------------------------ |
| `preversion`     | `npm version`     | **更新 `package.json` 之前**               | 运行测试、检查代码等。                     |
| **`version`**    | **`npm version`** | **更新 `package.json` 之后，Git 提交之前** | **同步所有其他版本文件**、`git add` 它们。 |
| `postversion`    | `npm version`     | **Git 提交和打标签之后**                   | 推送 Git 标签（`git push --tags`）等。     |

因此，`version` 脚本的设计目的正是为了让开发者能够有机会在 Git 提交之前，**同步更新所有其他依赖于 `package.json` 版本号的文件**（就像您的 `version-bump.mjs` 所做的那样）。这是一个标准且强大的自动化版本管理机制。

## 附

```js
// version-bump.mjs 文件内容

import { readFileSync, writeFileSync } from "fs";

const targetVersion = process.env.npm_package_version;

// read minAppVersion from manifest.json and bump version to target version
let manifest = JSON.parse(readFileSync("manifest.json", "utf8"));
const { minAppVersion } = manifest;
manifest.version = targetVersion;
writeFileSync("manifest.json", JSON.stringify(manifest, null, "\t"));

// update versions.json with target version and minAppVersion from manifest.json
let versions = JSON.parse(readFileSync("versions.json", "utf8"));
versions[targetVersion] = minAppVersion;
writeFileSync("versions.json", JSON.stringify(versions, null, "\t"));
```
