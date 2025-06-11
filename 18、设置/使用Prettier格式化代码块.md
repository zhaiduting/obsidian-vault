相当繁琐，基本过程是这样：

1. 安装三方插件 [obsidian-plugin-prettier](https://github.com/hipstersmoothie/obsidian-plugin-prettier)
2. 进入 Obsidian 笔记仓库；
3. 在仓库根目录下创建一个名为`.prettierrc`的空文件；
4. 从 [prettier.io](https://prettier.io/playground/) 复制一段 JSON 配置项，将其粘贴到`.prettierrc`文件中；
5. 安装 Prettier 后格式化生效；
6. 如果想格式化 Php 及 GoLang，需要安装相应的插件（实测无效）。

```
~ > cd obsidian-vault 
obsidian-vault > touch .prettierrc
obsidian-vault > pbpaste > .prettierrc # 将网站复制的配置项写入文件
obsidian-vault > yarn add --dev --exact prettier
```
